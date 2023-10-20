---
title: "JavaScript"
order: 4
---
## Turbo

### Mettre à jour la navigation avec Turbo

Dans certains cas, on veut mettre à jour la navigation côté client tout en utilisant des [`turbo-stream`](https://turbo.hotwired.dev/reference/streams). 

Cas d'usage : 
- Sur une page avec des onglets, on veut que chaque clic sur un onglet change l'URL.
- Par défaut, [Turbo](https://github.com/hotwired/turbo/tree/c207f5b25758e4a084e8ae42e49712b91cf37114) ne mettra pas l'URL à jour.
- On doit ajouter un bout de JavaScript personnalisé pour faire ça.

```javascript
const url = new URL(path)

// Update the browser history
history.pushState({}, null, path) 

// Turbo uses the URL of the page it renders to save a snapshot (HTML output for cache) 
// just before rendering a new page.
// This means that when we update the URL programatically with JavaScript with history.pushState,
// Turbo still uses the first URL that matches Turbo rendering.
// We have to tell Turbo what is the last rendered URL, so it can save the snapshot for the cache under the right URL. This is important to make the back button work properly.
Turbo.navigator.view.lastRenderedLocation = url

// Update Turbo history
Turbo.navigator.history.replace(url)
```

## Test

### Mise en place de tests avec Chai/Mocha/Sinon

```javascript
// ========== Stubs and mocks ==========

// Using sinon-test and sinon-chai

import chai, { expect } from 'chai'
import sinon from 'sinon'
import sinonChai from 'sinon-chai'

global.chai = chai
global.expect = expect
chai.use(sinonChai)
const sinonTest = require('sinon-test')(sinon)

describe('myTest', () => {
  it('myExpectation', sinonTest(function() {
    const stubbedFunction = this.stub()
    
    expect(stubbedFunction).to.have.been.called
  }))
})

// Stub environment variable (example with NODE_ENV)

describe('myTest', () => {
  let originalEnv
  
  before(() => {
    originalEnv = Object.getOwnPropertyDescriptor(process.env, 'NODE_ENV')
    Object.defineProperty(process.env, 'NODE_ENV', { value: 'production' })
  })
  
  after(() => {
    Object.defineProperty(process.env, 'NODE_ENV', originalEnv)
  })
})

// Mocking http request on a Node server

import chai, { expect } from 'chai'
import chaiHttp from 'chai-http'
import server from './myServer'

// myServer have to be a Node server. Eg: 
//   const server = restify.createServer()
//   server.get(…)
//   server.post(…)
//   server.listen(…)
//   
//   export default server

global.chai = chai
chai.use(chaiHttp)

describe('GET /myEndpoint', () => {
  it('myExpectation', (done) => {
    chai.request(server)
      .get('/myEndpoint')
      .end((error, response) => {
        const { status, body } = res
        
        // my expectation…
        
        done()
      })
  })
})

// Stubbing ES6 default import

import * as myDefaultImport from './my-default-import'

describe('myTest', () => {
  it('myExpectation', sinonTest(function() {
    const stubbedDefaultImport = this.stub(myDefaultImport, 'defaultImportFunction')
    
    // my expectation…
  }))
})

// ========== Promises ==========

// Testing a promise

describe('myTest', () => {
  it('myExpectation', (done) => {
    myPromise.then(() => {
      // my expectation…
      done()
    })
  })
  
  it('myExpectation', (done) => {
    myPromise.catch(() => {
      // my expectation…
      done()
    })
  })
})
``` 