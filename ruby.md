---
title: Ruby
---
## Ruby on Rails

### Quelques astuces avec FactoryBot

#### Générer un identifiant unique sur un modèle

```ruby
# spec/fixtures/usernames.rb
USERNAMES = ['hermione.granger',
             'ron.weasley',
             'harry.potter',
             'fred.weasley',
             'georges.weasley',
             'luna.lovegood',
             'ginny.weasley',
             'draco.malfoy',
             'albus.dumbledore',
             'hannah.abbott',
             'godric.gryffindor',
             'helga.hufflepuff',
             'neville.longbottom',
             'remus.lupin']

# spec/factory/user.rb
require 'fixtures/usernames'

FactoryBot.define do
  sequence :email do |n|
    username = USERNAMES[n] || "wizard_#{n}"

    "#{username}@hogwarts.wiz"
  end
end
```

#### Utiliser un alias pour nommer un modèle

Cette technique sert bien dans les cas où le modèle est utilisé dans une association.

```ruby
FactoryBot.define do
  factory :user, aliases: [:owner] do
    email
  end
end
```
### Factory one-to-many

Cette technique sert bien dans les cas où la classe spécifie une association _one-to-many_ avec le helper `has_many`.

```ruby
FactoryBot.define do
  trait :with_books do
    items { create_list :book, 3 }
  end
end
```

### Factory many-to-many

Avec des factories associées qui n'existent pas et doivent être créées en même temps : 

```ruby
FactoryBot.define do
  trait :with_bedrooms do
    after :create do |user|
      user.bedrooms = create_list :bedroom, 3
    end
  end
end
```

Avec des factories associées existantes : 

```ruby
FactoryBot.define do
 transient do
    taught_by nil
  end
  
  after(:create) do |user, factory|
    if taught_by
      create(:mentorship, user: user, professor: factory.taught_by)
    end
  end
end
```
### Autres astuces que je n'ai pas encore triées

```ruby
  
  # by-passing validations
  factory :user do
    …
    
    to_create { |instance| instance.save(validate: false) }
  end
  
  # custom factory name
  factory :bare_user, class: User do
    …
  end
  
  # using Active Storage
  factory :user do
    …
    
    after :build do |user|
      user.image.attach(
        io: File.open('spec', 'fixtures', 'files', 'YOUR_FILE.jpg'),
        filename: 'YOUR_FILE.jpg',
        content_type: 'image/jpeg',
       )
    end
  end
end
```

### Quelques astuces avec ActiveStorage

```ruby
# SEE CONFIGURATION ON: https://github.com/rails/rails/blob/main/guides/source/configuring.md#configuring-active-storage

# Delete variations when config.active_storage.track_variants = true
my_model_instance.image.service.delete_prefixed(my_model_instance.image.key)

# List all variations on a model
# Here, `my_model_attachment_name` refers to the name I give to the attachment with the `:has_one_attached` macro.
# cf. https://edgeguides.rubyonrails.org/active_storage_overview.html
my_model_attachment_name = "image"
my_model_variations_transformations = MyModel.attachment_reflections[my_model_attachment_name].variants.keys.map do |variant_name|
   MyModel.first.public_send(my_model_attachment_name).variant(variant_name).variation.transformations
end
# Example of an output 
# =>
# [{:format=>"jpg", :resize_to_fit=>[400, 400], :saver=>{:quality=>30}},
#  {:format=>"jpg", :resize_to_fit=>[600, 600], :saver=>{:quality=>30}},
#  {:format=>"jpg", :resize_to_limit=>[800, 800], :saver=>{:quality=>70}}]

# List all existing variation digests
current_variant_variation_digests = my_model_variations_transformations.map do |transformations|
  # Use the same digest as ActiveRecord::Variation
  # cf. https://github.com/rails/rails/blob/main/activestorage/app/models/active_storage/variation.rb#L78
  OpenSSL::Digest::SHA1.base64digest Marshal.dump(transformations.symbolize_keys)
end

# Count unused old variants
all_variation_digests = ActiveStorage::VariantRecord.all.distinct.pluck(:variation_digest)
useless_digests = all_variation_digests - current_variant_variation_digests
unused_old_variants = ActiveStorage::VariantRecord.where(variation_digest: useless_digests)
unused_old_variants_count = unused_old_variants.count

# Delete unused variant records and their files from my storage
unused_old_variants.destroy_all

# Only delete files of unused blobs on your storage
useless_digests.each do |digest|
  ActiveStorage::Blob.service.delete(digest)
end

# Use cached public files with S3

# Add this to config/storage.yml
# public: true,
# upload: 
#   cache_control: public, max-age=<%= whatever I want %>, immutable

# If this is only for the variants, destroy all the variants and reupload them using variant.processed.url
# If this is for the original attachments and the variants, purge the attachments (cf. https://github.com/rails/rails/blob/3ea99f53fafbcacfda58b11e2c0537fc043742f2/activestorage/lib/active_storage/attached/one.rb#L7)

# RSpec config
config.before do
  ActiveStorage::Current.urls_options = {
    host: "example.com",
  }
end
```