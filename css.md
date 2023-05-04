---
title: CSS
---
## Typographie

### Ajuster la taille des caractères cyrilliques

```
@font-face {
  font-family: "Ajusted Cyrillic";
  size-adjust: 95%;
  src: local(arial);
  unicode-range: U+04??; // Cyrillic characters
}

.ajusted-cyrillic {
  font-family: "Ajusted Cyrillic", Arial, serif;
}
```