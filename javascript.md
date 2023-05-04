---
title: JavaScript
---
## Testing

### Chai/Mocha/Sinon cheatsheet

```
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