#Ng2-Restangular



This project is the follow-up of the [Restangular](https://github.com/mgonto/restangular/). Ng2-Restangular is an Angular 2 service that simplifies common GET, POST, DELETE, and UPDATE requests with a minimum of client code.
It's a perfect fit for any WebApp that consumes data from a RESTful API.

#Table of contents

- [Current stage](#current-stage)
- [How do I add this to my project?](#how-do-i-add-this-to-my-project)
- [Dependencies](#dependencies)
- [Starter Guide](#starter-guide)
  - [Quick configuration for Lazy Readers](#quick-configuration-for-lazy-readers)
  - [Using Restangular](#using-restangular)
    - [Creating Main Restangular object](#creating-main-restangular-object)
    - [Lets Code with Observables!](#lets-code-with-observables)
    - [Here is Example of code with using promises!](#here-is-example-of-code-with-using-promises)
  - [Configuring Restangular](#configuring-restangular)
    - [Properties](#properties)
      - [setBaseUrl](#setbaseurl)
      - [setExtraFields](#setextrafields)
      - [setParentless](#setparentless)
      - [addElementTransformer](#addelementtransformer)
      - [setTransformOnlyServerElements](#settransformonlyserverelements)
      - [setOnElemRestangularized](#setonelemrestangularized)
      - [addResponseInterceptor](#addresponseinterceptor)
      - [addRequestInterceptor](#addrequestinterceptor)
      - [addFullRequestInterceptor](#addfullrequestinterceptor)
      - [setErrorInterceptor](#seterrorinterceptor)
      - [setRestangularFields](#setrestangularfields)
      - [setMethodOverriders](#setmethodoverriders)
      - [setJsonp](#setjsonp)
      - [setDefaultRequestParams](#setdefaultrequestparams)
      - [setFullResponse](#setfullresponse)
      - [setDefaultHeaders](#setdefaultheaders)
      - [setRequestSuffix](#setrequestsuffix)
      - [setUseCannonicalId](#setusecannonicalid)
      - [setPlainByDefault](#setplainbydefault)
      - [setEncodeIds](#setencodeids)
      - [setDefaultResponseMethod](#setdefaultresponsemethod)
    - [Accessing configuration](#accessing-configuration)
    - [How to configure them globally](#how-to-configure-them-globally)
      - [Configuring in the AppModule](#configuring-in-the-appmodule)
      - [Configuring in the AppModule with Dependency Injection applied](#configuring-in-the-appmodule-with-dependency-injection-applied)
    - [How to create a Restangular service with a different configuration from the global one](#how-to-create-a-restangular-service-with-a-different-configuration-from-the-global-one)
    - [Decoupled Restangular Service](#decoupled-restangular-service)
  - [Methods description](#methods-description)
    - [Restangular methods](#restangular-methods)
    - [Element methods](#element-methods)
    - [Collection methods](#collection-methods)
    - [Custom methods](#custom-methods)
  - [Copying elements](#copying-elements)
  - [Enhanced promises](#enhanced-promises)
  - [Using values directly in templates with Promises](#using-values-directly-in-templates)
  - [URL Building](#url-building)
  - [Creating new Restangular Methods](#creating-new-restangular-methods)
  - [Adding Custom Methods to Collections](#adding-custom-methods-to-collections)
    - [Example:](#example)
  - [Adding Custom Methods to Models](#adding-custom-methods-to-models)
    - [Example:](#example-1)
- [FAQ](#faq)
    - [How can I handle errors?](#how-can-i-handle-errors)
    - [I need to send one header in EVERY Restangular request, how do I do this?](#i-need-to-send-one-header-in-every-restangular-request-how-do-i-do-this)
    - [How can I send a delete WITHOUT a body?](#how-can-I-send-a-delete-without-a-body?)
    - [I use Mongo and the ID of the elements is _id not id as the default. Therefore requests are sent to undefined routes](#i-use-mongo-and-the-id-of-the-elements-is-_id-not-id-as-the-default-therefore-requests-are-sent-to-undefined-routes)
    - [What if each of my models has a different ID name like CustomerID for Customer](#what-if-each-of-my-models-has-a-different-id-name-like-customerid-for-customer)
    - [How can I send files in my request using Restangular?](#how-can-i-send-files-in-my-request-using-restangular?)
    - [How do I handle CRUD operations in a List returned by Restangular?](#how-do-i-handle-crud-operations-in-a-list-returned-by-restangular)
    - [Removing an element from a collection, keeping the collection restangularized](#removing-an-element-from-a-collection,-keeping-the-collection-restangularized)
    - [How can I access the unrestangularized element as well as the restangularized one?](#how-can-i-access-the-unrestangularized-element-as-well-as-the-restangularized-one)
- [Server Frameworks](#server-frameworks)
- [License](#license)

**[Back to top](#table-of-contents)**

#Current stage

Ng2-Restangular is in beta-version now. Almost all functionality was transferred from the Restangular.
We are open to any cooperation in terms of its further development.

#How do I add this to my project?

You can download this by:

* Using npm and running `npm install restangular`

**[Back to top](#table-of-contents)**

#Dependencies

Restangular depends on Angular2, Lodash and q(this library won't be included in the next versions).

**[Back to top](#table-of-contents)**

#Starter Guide

## Quick Configuration (For Lazy Readers)
This is all you need to start using all the basic Restangular features.

````javascript
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';
import { RestangularModule } from 'ng2-restangular';


// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [
    // Importing RestangularModule and making default configs for restanglar
    RestangularModule.forRoot((RestangularProvider) => {
        RestangularProvider.setBaseUrl('http://api.test.local/v1');
        RestangularProvider.setDefaultHeaders({'Authorization': 'Bearer UDXPx-Xko0w4BRKajozCVy20X11MRZs1'});
      }
    ),
  ]
})
export class AppModule {
}

// later in code ...

@Component({
  ...
})
export class OtherComponent {
  constructor(private restangular: Restangular) {
  }

  ngOnInit() {
    // GET http://api.test.local/v1/users/2/accounts
    this.restangular.one('users', 2).all(accounts).getList();
  }

}
````
**[Back to top](#table-of-contents)**

## Using Restangular

### Creating Main Restangular object

There are 3 ways of creating a main Restangular object.
The first one and most common one is by stating the main route of all requests.
The second one is by stating the main route and object of all requests.

````javascript
// Only stating main route
Restangular.all('accounts')

// Stating main object
Restangular.one('accounts', 1234)

// Gets a list of all of those accounts
Restangular.several('accounts', 1234, 123, 12345);
````

**[Back to top](#table-of-contents)**

### Lets Code with Observables!

If you dont want to use observables see code below.
The default config is with using promises so you need to set response method to "observables".
You can do this in config when importing module or like in code below.
Now that we have our main Object let's start playing with it.



````typescript
@Component({
  ...
})
export class OtherComponent {
  allAccounts;
  accounts;
  account;
  
  constructor(private restangular: Restangular) {
    // Setting method of response.
    this.restangular.provider.setDefaultResponseMethod("observables");
  }
  
  ngOnInit() {
    
    // First way of creating a this.restangular object. Just saying the base URL
    let baseAccounts = this.restangular.all('accounts');
  
    // This will query /accounts and return a observable.
    baseAccounts.getList().subscribe( accounts => {
      this.allAccounts = accounts;
    });
  
      
    let newAccount = {name: "Gonto's account"};
  
    // POST /accounts
    baseAccounts.post(newAccount);
  
    // GET to http://www.google.com/ You set the URL in this case
    this.restangular.allUrl('googlers', 'http://www.google.com/').getList();
  
    // GET to http://www.google.com/1 You set the URL in this case
    this.restangular.oneUrl('googlers', 'http://www.google.com/1').get();
  
    // You can do RequestLess "connections" if you need as well
  
    // Just ONE GET to /accounts/123/buildings/456
    this.restangular.one('accounts', 123).one('buildings', 456).get();
  
    // Just ONE GET to /accounts/123/buildings
    this.restangular.one('accounts', 123).getList('buildings');
  
    // Here we use Observables 
    // GET /accounts
    baseAccounts.getList().subscribe( accounts => {
      // Here we can continue fetching the tree :).
    
      let firstAccount = accounts[0];
      // This will query /accounts/123/buildings considering 123 is the id of the firstAccount
      let buildings = firstAccount.getList("buildings");
    
      // GET /accounts/123/places?query=param with request header: x-user:mgonto
      let loggedInPlaces = firstAccount.getList("places", {query: 'param'}, {'x-user': 'mgonto'});
    
      // This is a regular JS object, we can change anything we want :)
      firstAccount.name = "Gonto";
    
      // If we wanted to keep the original as it is, we can copy it to a new element
      let editFirstAccount = this.restangular.copy(firstAccount);
      editFirstAccount.name = "New Name";
    
    
      // PUT /accounts/123. The name of this account will be changed from now on
      firstAccount.put();
      editFirstAccount.put();
    
      // PUT /accounts/123. Save will do POST or PUT accordingly
      firstAccount.save();
    
      // DELETE /accounts/123 We don't have first account anymore :(
      firstAccount.remove();
    
      var myBuilding = {
        name: "Gonto's Building",
        place: "Argentina"
      };
    
      // POST /accounts/123/buildings with MyBuilding information
      firstAccount.post("Buildings", myBuilding).subscribe( () => {
        console.log("Object saved OK");
      }, () => {
        console.log("There was an error saving");
      });
    
      // GET /accounts/123/users?query=params
      firstAccount.getList("users", {query: 'params'}).subscribe( users => {
        // Instead of posting nested element, a collection can post to itself
        // POST /accounts/123/users
        users.post({userName: 'unknown'});
      
        // Custom methods are available now :).
        // GET /accounts/123/users/messages?param=myParam
        users.customGET("messages", {param: "myParam"});
      
        var firstUser = users[0];
      
        // GET /accounts/123/users/456. Just in case we want to update one user :)
        let userFromServer = firstUser.get();
      
        // ALL http methods are available :)
        // HEAD /accounts/123/users/456
        firstUser.head()
      
      });
    
    }, () => {
      alert("Oops error from server :(");
    });
  
    // Second way of creating this.restangular object. URL and ID :)
    var account = this.restangular.one("accounts", 123);
  
    // GET /accounts/123?single=true
    this.account = account.get({single: true});
  
    // POST /accounts/123/messages?param=myParam with the body of name: "My Message"
    account.customPOST({name: "My Message"}, "messages", {param: "myParam"}, {})
  }
}
````

**[Back to top](#table-of-contents)**

### Here is Example of code with using promises!


````javascript
@Component({
  ...
})
export class OtherComponent {
  allAccounts;
  accounts;
  account;
  
  constructor(private restangular: Restangular) {
  }
  
  ngOnInit() {
    
    // First way of creating a this.restangular object. Just saying the base URL
    let baseAccounts = this.restangular.all('accounts');
  
    // This will query /accounts and return a promise.
    baseAccounts.getList().then(function(accounts) {
      this.allAccounts = accounts;
    });
  
    // Does a GET to /accounts
    // Returns an empty array by default. Once a value is returned from the server
    // that array is filled with those values. So you can use this in your template
    this.accounts = this.restangular.all('accounts').getList().$object;
  
    var newAccount = {name: "Gonto's account"};
  
    // POST /accounts
    baseAccounts.post(newAccount);
  
    // GET to http://www.google.com/ You set the URL in this case
    this.restangular.allUrl('googlers', 'http://www.google.com/').getList();
  
    // GET to http://www.google.com/1 You set the URL in this case
    this.restangular.oneUrl('googlers', 'http://www.google.com/1').get();
  
    // You can do RequestLess "connections" if you need as well
  
    // Just ONE GET to /accounts/123/buildings/456
    this.restangular.one('accounts', 123).one('buildings', 456).get();
  
    // Just ONE GET to /accounts/123/buildings
    this.restangular.one('accounts', 123).getList('buildings');
  
    // Here we use Promises then
    // GET /accounts
    baseAccounts.getList().then(function (accounts) {
      // Here we can continue fetching the tree :).
    
      var firstAccount = accounts[0];
      // This will query /accounts/123/buildings considering 123 is the id of the firstAccount
      this.buildings = firstAccount.getList("buildings");
    
      // GET /accounts/123/places?query=param with request header: x-user:mgonto
      this.loggedInPlaces = firstAccount.getList("places", {query: 'param'}, {'x-user': 'mgonto'});
    
      // This is a regular JS object, we can change anything we want :)
      firstAccount.name = "Gonto";
    
      // If we wanted to keep the original as it is, we can copy it to a new element
      var editFirstAccount = this.restangular.copy(firstAccount);
      editFirstAccount.name = "New Name";
    
    
      // PUT /accounts/123. The name of this account will be changed from now on
      firstAccount.put();
      editFirstAccount.put();
    
      // PUT /accounts/123. Save will do POST or PUT accordingly
      firstAccount.save();
    
      // DELETE /accounts/123 We don't have first account anymore :(
      firstAccount.remove();
    
      var myBuilding = {
        name: "Gonto's Building",
        place: "Argentina"
      };
    
      // POST /accounts/123/buildings with MyBuilding information
      firstAccount.post("Buildings", myBuilding).then(function() {
        console.log("Object saved OK");
      }, function() {
        console.log("There was an error saving");
      });
    
      // GET /accounts/123/users?query=params
      firstAccount.getList("users", {query: 'params'}).then(function(users) {
        // Instead of posting nested element, a collection can post to itself
        // POST /accounts/123/users
        users.post({userName: 'unknown'});
      
        // Custom methods are available now :).
        // GET /accounts/123/users/messages?param=myParam
        users.customGET("messages", {param: "myParam"});
      
        var firstUser = users[0];
      
        // GET /accounts/123/users/456. Just in case we want to update one user :)
        this.userFromServer = firstUser.get();
      
        // ALL http methods are available :)
        // HEAD /accounts/123/users/456
        firstUser.head()
      
      });
    
    }, function errorCallback() {
      alert("Oops error from server :(");
    });
  
    // Second way of creating this.restangular object. URL and ID :)
    var account = this.restangular.one("accounts", 123);
  
    // GET /accounts/123?single=true
    this.account = account.get({single: true});
  
    // POST /accounts/123/messages?param=myParam with the body of name: "My Message"
    account.customPOST({name: "My Message"}, "messages", {param: "myParam"}, {})
  }
}
````

**[Back to top](#table-of-contents)**


## Configuring Restangular

### Properties
Restangular comes with defaults for all of its properties but you can configure them. **So, if you don't need to configure something, there's no need to add the configuration.**
You can set all these configurations in **[RestangularModule](#how-to-configure-them-globally) to change the global configuration** or you can **use the [withConfig](#how-to-create-a-restangular-service-with-a-different-configuration-from-the-global-one) method in Restangular service to create a new Restangular service with some scoped configuration**. Check the section on this later.

#### setBaseUrl
The base URL for all calls to your API. For example if your URL for fetching accounts is http://example.com/api/v1/accounts, then your baseUrl is `/api/v1`. The default baseUrl is an empty string which resolves to the same url that Angular2 is running, but you can also set an absolute url like `http://api.example.com/api/v1` if you need to set another domain.

#### setExtraFields
These are the fields that you want to save from your parent resources if you need to display them. By default this is an Empty Array which will suit most cases

#### setParentless
Use this property to control whether Restangularized elements to have a parent or not. So, for example if you get an account and then get a nested list of buildings, you may want the buildings URL to be simple `/buildings/123` instead of `/accounts/123/buildings/123`. This property lets you do that.

This method accepts 1 parameter, it could be:

* Boolean: Specifies if all elements should be parentless or not
* Array: Specifies the routes (types) of all elements that should be parentless. For example `['buildings']`

#### addElementTransformer
This is a hook. After each element has been "restangularized" (Added the new methods from Restangular), the corresponding transformer will be called if it fits.

This should be used to add your own methods / functions to entities of certain types.

You can add as many element transformers as you want. The signature of this method can be one of the following:

* **addElementTransformer(route, transformer)**: Transformer is called with all elements that have been restangularized, no matter if they're collections or not.

* **addElementTransformer(route, isCollection, transformer)**: Transformer is called with all elements that have been restangularized and match the specification regarding if it's a collection or not (true | false)

#### setTransformOnlyServerElements
This sets whether transformers will be run for local objects and not by objects returned by the server. This is by default true but can be changed to false if needed (Most people won't need this).


#### setOnElemRestangularized
This is a hook. After each element has been "restangularized" (Added the new methods from Restangular), this will be called. It means that if you receive a list of objects in one call, this method will be called first for the collection and then for each element of the collection.

**I favor the usage of `addElementTransformer` instead of `onElemRestangularized` whenever possible as the implementation is much cleaner.**


This callback is a function that has 3 parameters:

* **elem**: The element that has just been restangularized. Can be a collection or a single element.
* **isCollection**: Boolean indicating if this is a collection or a single element.
* **what**: The model that is being modified. This is the "path" of this resource. For example `buildings`
* **Restangular**: The instanced service to use any of its methods

This can be used together with `addRestangularMethod` (Explained later) to add custom methods to an element


#### addResponseInterceptor
The responseInterceptor is called after we get each response from the server. It's a function that receives this arguments:

* **data**: The data received got from the server
* **operation**: The operation made. It'll be the HTTP method used except for a `GET` which returns a list of element which will return `getList` so that you can distinguish them.
* **what**: The model that's being requested. It can be for example: `accounts`, `buildings`, etc.
* **url**: The relative URL being requested. For example: `/api/v1/accounts/123`
* **response**: Full server response including headers
* **deferred**: The deferred promise for the request.

Some of the use cases of the responseInterceptor are handling wrapped responses and enhancing response elements with more methods among others.

The responseInterceptor must return the restangularized data element.

#### addRequestInterceptor
The requestInterceptor is called before sending any data to the server. It's a function that must return the element to be requested. This function receives the following arguments:

* **element**: The element to send to the server.
* **operation**: The operation made. It'll be the HTTP method used except for a `GET` which returns a list of element which will return `getList` so that you can distinguish them.
* **what**: The model that's being requested. It can be for example: `accounts`, `buildings`, etc.
* **url**: The relative URL being requested. For example: `/api/v1/accounts/123`

#### addFullRequestInterceptor
This adds a new fullRequestInterceptor. The fullRequestInterceptor is similar to the `requestInterceptor` but more powerful. It lets you change the element, the request parameters and the headers as well.

It's a function that receives the same as the `requestInterceptor` plus the headers and the query parameters (in that order).

It can return an object with any (or all) of following properties:
* **headers**: The headers to send
* **params**: The request parameters to send
* **element**: The element to send
* **httpConfig**: The httpConfig to call with

If a property isn't returned, the one sent is used.

#### setErrorInterceptor
The errorInterceptor is called whenever there's an error. It's a function that receives the response, the deferred (for the promise) and the Restangular-response handler as parameters.

The errorInterceptor function, whenever it returns `false`, prevents the promise linked to a Restangular request to be executed. All other return values (besides `false`) are ignored and the promise follows the usual path, eventually reaching the success or error hooks.

The feature to prevent the promise to complete is useful whenever you need to intercept each Restangular error response for every request in your Angular2 application in a single place, increasing debugging capabilities and hooking security features in a single place.

````javascript
var refreshAccesstoken = function() {
  return new Promise((resolve, reject) => {
    // Refresh access-token logic
    
    resolve(true);
  });
};

// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  imports: [ 
    // Importing RestangularModule and making default configs for restanglar
    RestangularModule.forRoot([Http], (Restangular, http)=>{
      Restangular.provider.setBaseUrl('http://api.test.com/v1');
      Restangular.provider.setDefaultResponseMethod('promise');
  
      // Configurating Error Interceptor
      Restangular.provider.setErrorInterceptor((response, deferred, responseHandler) => {
        if(response.status === 403) {
          refreshAccesstoken().then(() => {
            // Create new request and then call the handlers the usual way.
            http.get('http://api.test.com/v1/users/2', {})
            .map(res=>{
              if (response._body) {
                response.data = JSON.parse(response._body);
              } else {
                response.data = null
              }
              return response;
            })
            .toPromise().then(responseHandler, deferred.reject);
            //// Be aware that no request interceptors are called this way.
          });
          return false; // error handled
        }
        return true; // error not handled
      });
    }),
  ],
})
````

#### setRestangularFields

Restangular required 3 fields for every "Restangularized" element. These are:

* id: Id of the element. Default: id
* route: Name of the route of this element. Default: route
* parentResource: The reference to the parent resource. Default: parentResource
* restangularCollection: A boolean indicating if this is a collection or an element. Default: restangularCollection
* cannonicalId: If available, the path to the cannonical ID to use. Useful for PK changes
* etag: Where to save the ETag received from the server. Defaults to `restangularEtag`
* selfLink: The path to the property that has the URL to this item. If your REST API doesn't return a URL to an item, you can just leave it blank. Defaults to `href`

Also all of Restangular methods and functions are configurable through restangularFields property.
All of these fields except for `id` and `selfLink` are handled by Restangular, so most of the time you won't change them. You can configure the name of the property that will be binded to all of this fields by setting restangularFields property.

#### setMethodOverriders

You can now Override HTTP Methods. You can set here the array of methods to override. All those methods will be sent as POST and Restangular will add an X-HTTP-Method-Override header with the real HTTP method we wanted to do.

````javascript
RestangularProvider.setMethodOverriders(["Get","Put"]);
````

#### setJsonp
Typical web browsers prohibit requesting data from a server in a different domain (same-origin policy). JSONP or "JSON with padding" is a communication technique used in JavaScript programs running in web browsers to get around this.

For JSONP to work, a server must know how to reply with JSONP-formatted results. JSONP does not work with JSON-formatted results. The JSONP parameters passed as arguments to a script are defined by the server.

By setting the value of setJsonp to true, both `get` and `getList` will be performed using JSonp instead of the regular GET.

You will need to add the 'JSON_CALLBACK' string to your URLs (see [$http.jsonp](http://docs.angularjs.org/api/ng.$http#methods_jsonp)). You can use `setDefaultRequestParams` to accomplish this:
```javascript
RestangularProvider.setDefaultRequestParams('jsonp', {callback: 'JSON_CALLBACK'});
```

#### setDefaultRequestParams

You can set default Query parameters to be sent with every request and every method.

Additionally, if you want to configure request params per method, you can use `requestParams` configuration similar to `$http`. For example `RestangularProvider.requestParams.get = {single: true}`.

Supported method to configure are: remove, get, post, put, common (all)

````javascript
// set params for multiple methods at once
RestangularProvider.setDefaultRequestParams(['remove', 'post'], {confirm: true});

// set only for get method
RestangularProvider.setDefaultRequestParams('get', {limit: 10});

// or for all supported request methods
RestangularProvider.setDefaultRequestParams({apikey: "secret key"});
````

#### setFullResponse

You can set fullResponse to true to get the whole response every time you do any request. The full response has the restangularized data in the `data` field, and also has the headers and config sent. By default, it's set to false. Please note that in order for Restangular to access custom HTTP headers, your server must respond having the `Access-Control-Expose-Headers:` set.

````javascript
// set params for multiple methods at once
RestangularProvider.setFullResponse(true);
````

Or set it per service
````javascript
// Restangular factory that uses setFullResponse
export const REST_FUL_RESPONSE = new OpaqueToken('RestFulResponse');
export function RestFulResponseFactory(restangular: Restangular) {
  return restangular.withConfig((RestangularConfigurer) => {
    RestangularConfigurer.setFullResponse(true);
  });
}


// Configure factory in AppModule module
// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [RestangularModule],
  providers: [
    { provide: REST_FUL_RESPONSE, useFactory:  RestFulResponseFactory, deps: [Restangular] }
  ]
})
export class AppModule {}


// Let's use it in the component
@Component({
  ...
})
export class OtherComponent {
  users;
  
  constructor(@Inject(REST_FUL_RESPONSE) public restFulResponse) {
  }
  
  ngOnInit() {
    this.restFulResponse.all('users').getList().then((response) => {
      this.users = response.data;
      console.log(response.headers);
    });
  }
}
````

#### setDefaultHeaders

You can set default Headers to be sent with every request. Send format: {header_name: header_value}

````javascript
// set default header "token"
RestangularProvider.setDefaultHeaders({token: "x-restangular"});
````

#### setRequestSuffix

If all of your requests require to send some suffix to work, you can set it here. For example, if you need to send the format like `/users/123.json` you can add that `.json` to the suffix using the `setRequestSuffix` method

#### setUseCannonicalId

You can set this to either `true` or `false`. By default it's false. If set to true, then the cannonical ID from the element will be used for URL creation (in DELETE, PUT, POST, etc.). What this means is that if you change the ID of the element and then you do a put, if you set this to true, it'll use the "old" ID which was received from the server. If set to false, it'll use the new ID assigned to the element.

#### setPlainByDefault

You can set this to `true` or `false`. By default it's false. If set to true, data retrieved will be returned with no embed methods from restangular.

#### setEncodeIds

You can set here if you want to URL Encode IDs or not. By default, it's true.

#### setDefaultResponseMethod

You can choose what Response Method to use `observable` or `promise`, by default `promise`.

**[Back to top](#table-of-contents)**

### Accessing configuration

You can also access the configuration via `RestangularModule` and `Restangular.provider` via the `configuration` property if you don't want to use the setters. Check it out:

````js
Restangular.provider.configuration.requestSuffix = '/';
````

**[Back to top](#table-of-contents)**

### How to configure them globally

You can configure this in either the `AppModule`.

#### Configuring in the `AppModule`

````javascript
import { RestangularModule } from 'ng2-restangular';

// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [
    RestangularModule.forRoot((RestangularProvider)=>{
      RestangularProvider.setBaseUrl('http://api.restng2.local/v1');
      RestangularProvider.setDefaultHeaders({'Authorization': 'Bearer UDXPx-Xko0w4BRKajozCVy20X11MRZs1'});
    }),
  ]
})
export class AppModule {
}
````

**[Back to top](#table-of-contents)**


#### Configuring in the `AppModule` with Dependency Injection applied

````javascript
import { RestangularModule } from 'ng2-restangular';

// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [
    RestangularModule.forRoot([Http], (RestangularProvider, http)=>{
      RestangularProvider.setBaseUrl('http://api.restng2.local/v1');
      RestangularProvider.setDefaultHeaders({'Authorization': 'Bearer UDXPx-Xko0w4BRKajozCVy20X11MRZs1'});
      
      // Example of using Http service inside global config restangular
      RestangularProvider.addElementTransformer('me', true, ()=>{
        return http.get('http://api.test.com/v1/users/2', {});
      });
    }),
  ]
})
export class AppModule {
}
````

**[Back to top](#table-of-contents)**

### How to create a Restangular service with a different configuration from the global one
Let's assume that for most requests you need some configuration (The global one), and for just a bunch of methods you need another configuration. In that case, you'll need to create another Restangular service with this particular configuration. This scoped configuration will inherit all defaults from the global one. Let's see how.

````javascript
//Restangular service that uses Bing
export const RESTANGULAR_BING = new OpaqueToken('RestangularBing');
export function RestangularBingFactory(restangular: Restangular) {
  return restangular.withConfig((RestangularConfigurer) => {
     RestangularConfigurer.setBaseUrl('http://www.bing.com');
   });
}


// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [
    // Global configuration
    RestangularModule.forRoot((RestangularProvider)=>{
      RestangularProvider.setBaseUrl('http://www.google.com');
    }),
  ],
  providers: [
    { provide: RESTANGULAR_BING, useFactory:  RestangularBingFactory, deps: [Restangular] }
  ]
})
export class AppModule {}


// Let's use it in the component
@Component({
  ...
})
export class OtherComponent {
  constructor(
    @Inject(Restangular) public Restangular,
    @Inject(RESTANGULAR_BING) public RestangularBing
  ) {}

  ngOnInit() {
    // GET to http://www.google.com/users
    // Uses global configuration
    Restangular.all('users').getList()
  
    // GET to http://www.bing.com/users
    // Uses Bing configuration which is based on Global one, therefore .json is added.
    RestangularBing.all('users').getList()
  }
};
````

**[Back to top](#table-of-contents)**

### Decoupled Restangular Service

There're some times where you want to use Restangular but you don't want to expose Restangular object anywhere. For those cases, you can actually use the `service` feature of Restangular.

Let's see how it works:

````js
// Restangular factory that uses Users
export const USER_REST = new OpaqueToken('UserRest');
export function UserRestFactory(restangular: Restangular) {
  return restangular.service('users');
}


// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  declarations: [
    AppComponent,
  ],
  imports: [RestangularModule],
  providers: [
    { provide: USER_REST, useFactory:  UserRestFactory, deps: [Restangular] } // Configurating our factory
  ]
})
export class AppModule {
}


// Let's use it in the component
export class OtherComponent {
  constructor(@Inject(USER_REST) public User) {
    Users.one(2).get() // GET to /users/2
    Users.post({data}) // POST to /users
    
    // GET to /users
    Users.getList().then(function(users) {
      var user = users[0]; // user === {id: 1, name: "Tonto"}
      user.name = "Gonto";
      // PUT to /users/1
      user.put();
    })
  }
}
````

We can also use Nested RESTful resources with this:

````js
var Cars = Restangular.service('cars', Restangular.one('users', 1));

Cars.getList() // GET to /users/1/cars
````

**[Back to top](#table-of-contents)**

## Methods description

There are 3 sets of methods. Collections have some methods and elements have others. There are are also some common methods for all of them

### Restangular methods
These are the methods that can be called on the Restangular object.
* **one(route, id)**: This will create a new Restangular object that is just a pointer to one element with the route `route` and the specified id.
* **all(route)**: This will create a new Restangular object that is just a pointer to a list of elements for the specified path.
* **oneUrl(route, url)**: This will create a new Restangular object that is just a pointer to one element with the specified URL.
* **allUrl(route, url)**: This creates a Restangular object that is just a pointer to a list at the specified URL.
* **copy(fromElement)**: This will create a copy of the from element so that we can modify the copied one.
* **restangularizeElement(parent, element, route, queryParams)**: Restangularizes a new element
* **restangularizeCollection(parent, element, route, queryParams)**: Restangularizes a new collection

**[Back to top](#table-of-contents)**

### Element methods
* **get([queryParams, headers])**: Gets the element. Query params and headers are optionals
* **getList(subElement, [queryParams, headers])**: Gets a nested resource. subElement is mandatory. **It's a string with the name of the nested resource (and URL)**. For example `buildings`
* **put([queryParams, headers])**: Does a put to the current element
* **post(subElement, elementToPost, [queryParams, headers])**: Does a POST and creates a subElement. Subelement is mandatory and is the nested resource. Element to post is the object to post to the server
* **remove([queryParams, headers])**: Does a DELETE. By default, `remove` sends a request with an empty object, which may cause problems with some servers or browsers. [This](https://github.com/mgonto/restangular/issues/193) shows how to configure RESTangular to have no payload.
* **head([queryParams, headers])**: Does a HEAD
* **trace([queryParams, headers])**: Does a TRACE
* **options([queryParams, headers])**: Does a OPTIONS
* **patch(object, [queryParams, headers])**: Does a PATCH
* **one(route, id)**: Used for RequestLess connections and URL Building. See section below.
* **all(route)**: Used for RequestLess connections and URL Building. See section below.
* **several(route, ids*)**: Used for RequestLess connections and URL Building. See section below.
* **oneUrl(route, url)**: This will create a new Restangular object that is just a pointer to one element with the specified URL.
* **allUrl(route, url)**: This creates a Restangular object that is just a pointer to a list at the specified URL.
* **getRestangularUrl()**: Gets the URL of the current object.
* **getRequestedUrl()**: Gets the real URL the current object was requested with (incl. GET parameters). Will equal getRestangularUrl() when no parameters were used, before calling `get()`, or when using on a nested child.
* **getParentList()**: Gets the parent list to which it belongs (if any)
* **clone()**: Copies the element. It's an alias to calling `Restangular.copy(elem)`.
* **plain()**: Returns the plain element received from the server without any of the enhanced methods from Restangular. It's an alias to calling `Restangular.stripRestangular(elem)`
* **save**: Calling save will determine whether to do PUT or POST accordingly

**[Back to top](#table-of-contents)**

### Collection methods
* **getList([queryParams, headers]): Gets itself again (Remember this is a collection)**.
* **get([id]): Gets one item from the collection by id**.
* **post(elementToPost, [queryParams, headers])**: Creates a new element of this collection.
* **head([queryParams, headers])**: Does a HEAD
* **trace: ([queryParams, headers])**: Does a TRACE
* **options: ([queryParams, headers])**: Does a OPTIONS
* **patch(object, [queryParams, headers])**: Does a PATCH
* **remove([queryParams, headers])**: Does a DELETE. By default, `remove` sends a request with an empty object, which may cause problems with some servers or browsers. [This](https://github.com/mgonto/restangular/issues/193) shows how to configure RESTangular to have no payload.
* **putElement(index, params, headers)**: Puts the element on the required index and returns a promise of the updated new array
````js
Restangular.all('users').getList()
.then((users) => {
  users.putElement(2, {'name': 'new name'});
});
````
* **getRestangularUrl()**: Gets the URL of the current object.
* **getRequestedUrl()**: Gets the real URL the current object was requested with (incl. GET parameters). Will equal getRestangularUrl() when no parameters were used, before calling `getList()`, or when using on a nested child.
* **one(route, id)**: Used for RequestLess connections and URL Building. See section below.
* **all(route)**: Used for RequestLess connections and URL Building. See section below.
* **several(route, ids*)**: Used for RequestLess connections and URL Building. See section below.
* **oneUrl(route, url)**: This will create a new Restangular object that is just a pointer to one element with the specified URL.
* **allUrl(route, url)**: This creates a Restangular object that is just a pointer to a list at the specified URL.
* **clone()**: Copies the collection. It's an alias to calling `Restangular.copy(collection)`.

**[Back to top](#table-of-contents)**

### Custom methods
* **customGET(path, [params, headers])**: Does a GET to the specific path. Optionally you can set params and headers.
* **customGETLIST(path, [params, headers])**: Does a GET to the specific path. **In this case, you expect to get an array, not a single element**. Optionally you can set params and headers.
* **customDELETE(path, [params, headers])**: Does a DELETE to the specific path. Optionally you can set params and headers.
* **customPOST([elem, path, params, headers])**: Does a POST to the specific path. Optionally you can set params and headers and elem. Elem is the element to post. If it's not set, it's assumed that it's the element itself from which you're calling this function.
* **customPUT([elem, path, params, headers])**: Does a PUT to the specific path. Optionally you can set params and headers and elem. Elem is the element to post. If it's not set, it's assumed that it's the element itself from which you're calling this function.
* **customPATCH([elem, path, params, headers])**: Does a PATCH to the specific path. Accepts the same arguments as customPUT.
* **customOperation(operation, path, [params, headers, elem])**: This does a custom operation to the path that we specify. This method is actually used from all the others in this subsection. Operation can be one of: get, post, put, remove, head, options, patch, trace
* **addRestangularMethod(name, operation, [path, params, headers, elem])**: This will add a new restangular method to this object with the name `name` to the operation and path specified (or current path otherwise). There's a section on how to do this later.

Let's see an example of this:

````javascript
// GET /accounts/123/messages
Restangular.one("accounts", 123).customGET("messages")

// GET /accounts/messages?param=param2
Restangular.all("accounts").customGET("messages", {param: "param2"})
````

**[Back to top](#table-of-contents)**

## Copying elements
Before modifying an object, we sometimes want to copy it and then modify the copied object. We can use `Restangular.copy(fromElement)`.

**[Back to top](#table-of-contents)**

## Enhanced promises

Restangular uses enhanced promises when returning. What does this mean? All promises returned now have 2 additional methods and collection promises have 3. These are the methods:

* **call(methodName, params*)**: This will return a new promise of the previous value, after calling the method called methodName with the parameters params.
* **get(fieldName)**: This will return a new promise for the type of the field. The param of this new promise is the property `fieldName` from the original promise result.
* **push(object)**: This method will only be in the promises of arrays. It's a subset of the call method that does a push.
* **$object**: This returns the reference to the object that will be filled once the server responds a value. This means that if you call `getList` this will be an empty array by default. Once the array is returned from the server, this same `$object` property will get filled with results from the server.

I know these explanations are quite complicated, so let's see an example :D.

````javascript
var buildings = Restangular.all("buildings").getList();

// New promise after adding the new building
// Now you can show in scope this newBuildings promise and it'll show all the buildings
// received from server plus the new one added
var newBuildings = buildings.push({name: "gonto"});

var newBuildingsSame = buildings.call("push", {name: "gonto"});

// This is a promise of a number value. You can show it in the UI
var lengthPromise = buildings.get("length");

lengthPromise.then(function(length) {
  // Here the length is the real length value of the returned collection of buildings
});
````

**[Back to top](#table-of-contents)**

## Using values directly in templates with Promises

Since Angular2, Promise unwrapping in templates has been disabled by default and will be deprecated soon.

**This means that the following will cease to work**:

````js
this.accounts = this.restangular.all('accounts').getList();
````

````html
<tr *ngFor="let account of accounts"">
  <td>{{account.name}}</td>
</tr>
````

**As this was a really handy way of working with Restangular, I've made a feature similar to $resource that will enable this behavior again**:

````js
this.accounts = this.restangular.all('accounts').getList().$object;
````

````html
<tr *ngFor="let account of accounts"">
  <td>{{account.name}}</td>
</tr>
````

The `$object` property is a new property I've added to promises. By default, it'll be an empty array or object. Once the sever has responded with the real value, that object or array is filled with the correct response, therefore making the ng-repeat work :). Pretty neat :D


**[Back to top](#table-of-contents)**

## URL Building
Sometimes, we have a lot of nested entities (and their IDs), but we just want the last child. In those cases, doing a request for everything to get the last child is overkill. For those cases, I've added the possibility to create URLs using the same API as creating a new Restangular object. This connections are created without making any requests. Let's see how to do this:

````javascript

var restangularSpaces = Restangular.one("accounts",123).one("buildings", 456).all("spaces");

// This will do ONE get to /accounts/123/buildings/456/spaces
restangularSpaces.getList()

// This will do ONE get to /accounts/123/buildings/456/spaces/789
Restangular.one("accounts", 123).one("buildings", 456).one("spaces", 789).get()

// POST /accounts/123/buildings/456/spaces
Restangular.one("accounts", 123).one("buildings", 456).all("spaces").post({name: "New Space"});

// DELETE /accounts/123/buildings/456
Restangular.one("accounts", 123).one("buildings", 456).remove();
````

**[Back to top](#table-of-contents)**

## Creating new Restangular Methods

Let's assume that your API needs some custom methods to work. If that's the case, always calling customGET or customPOST for that method with all parameters is a pain in the ass. That's why every element has a `addRestangularMethod` method.

This can be used together with the hook `addElementTransformer` to do some neat stuff. Let's see an example to learn this:

````javascript
// AppModule is the main entry point into Angular2 bootstraping process
@NgModule({
  bootstrap: [ AppComponent ],
  imports: [ // import Angular's modules
    RestangularModule.forRoot((RestangularProvider)=>{
      // It will transform all building elements, NOT collections
      RestangularProvider.addElementTransformer('buildings', false, function(building) {
        // This will add a method called evaluate that will do a get to path evaluate with NO default
        // query params and with some default header
        // signature is (name, operation, path, params, headers, elementToPost)
    
        building.addRestangularMethod('evaluate', 'get', 'evaluate', undefined, {'myHeader': 'value'});
    
        return building;
      });
  
      RestangularProvider.addElementTransformer('users', true, function(user) {
        // This will add a method called login that will do a POST to the path login
        // signature is (name, operation, path, params, headers, elementToPost)
    
        user.addRestangularMethod('login', 'post', 'login');
    
        return user;
      });
    }),
  ],
})

// Then, later in your code you can do the following:

// GET to /buildings/123/evaluate?myParam=param with headers myHeader: value

// Signature for this "custom created" methods is (params, headers, elem) if it's a safe operation (GET, OPTIONS, etc.)
// If it's an unsafe operation (POST, PUT, etc.), signature is (elem, params, headers).

// If something is set to any of this variables, the default set in the method creation will be overridden
// If nothing is set, then the defaults are sent
Restangular.one('buildings', 123).evaluate({myParam: 'param'});

// GET to /buildings/123/evaluate?myParam=param with headers myHeader: specialHeaderCase

Restangular.one('buildings', 123).evaluate({myParam: 'param'}, {'myHeader': 'specialHeaderCase'});

// Here the body of the POST is going to be {key: value} as POST is an unsafe operation
Restangular.all('users').login({key: value});

````

**[Back to top](#table-of-contents)**

## Adding Custom Methods to Collections

Create custom methods for your collection using Restangular.extendCollection(). This is an alias for:

```js
  RestangularProvider.addElementTransformer(route, true, fn);
```

### Example:
```js
  // create methods for your collection
  Restangular.extendCollection('accounts', function(collection) {
    collection.totalAmount = function() {
      // implementation here
    };

    return collection;
  });

  var accountsPromise = Restangular.all('accounts').getList();

  accountsPromise.then(function(accounts) {
    accounts.totalAmount(); // invoke your custom collection method
  });
```

**[Back to top](#table-of-contents)**

## Adding Custom Methods to Models

Create custom methods for your models using Restangular.extendModel(). This is an alias for:

```js
  RestangularProvider.addElementTransformer(route, false, fn);
```

**[Back to top](#table-of-contents)**

### Example:
```js
  Restangular.extendModel('accounts', function(model) {
    model.prettifyAmount = function() {};
    return model;
  });

  var accountPromise = Restangular.one('accounts', 1).get();

  accountPromise.then(function(account) {
    account.prettifyAmount(); // invoke your custom model method
  });
```

**[Back to top](#table-of-contents)**

# FAQ

#### **How can I handle errors?**

Errors can be checked on the second argument of the then.

````javascript
Restangular.all("accounts").getList().then(function() {
  console.log("All ok");
}, function(response) {
  console.log("Error with status code", response.status);
});
````

#### **I need to send one header in EVERY Restangular request, how do I do this?**

You can use `defaultHeaders` property for this or `$httpProvider.defaults.headers`, whichever suits you better. `defaultsHeaders` can be scoped with `withConfig` so it's really cool.

#### **How can I send a delete WITHOUT a body?**

You must add a requestInterceptor for this.

````js
Restangular.provider.setRequestInterceptor(function(elem, operation) {
  if (operation === "remove") {
     return null;
  }
  return elem;
})
````

#### **I use Mongo and the ID of the elements is `_id` not `id` as the default. Therefore requests are sent to undefined routes**

What you need to do is to configure the `RestangularFields` and set the `id` field to `_id`. Let's see how:

````javascript
RestangularProvider.setRestangularFields({
  id: "_id"
});
````

#### **What if each of my models has a different ID name like CustomerID for Customer**

In some cases, people have different ID name for each entity. For example, they have CustomerID for customer and EquipmentID for Equipment. If that's the case, you can override Restangular's getIdFromElem. For that, you need to do:

````js
Restangular.provider.configuration.getIdFromElem = function(elem) {
  // if route is customers ==> returns customerID
  return elem[_.initial(elem.route).join('') + "ID"];
}
````
With that, you'd get what you need :)

#### **How can I send files in my request using Restangular?**

This can be done using the customPOST / customPUT method. Look at the following example:
````js
Restangular.all('users')
.customPOST(formData, undefined, undefined, { 'Content-Type': undefined });
````
This basically tells the request to use the *Content-Type: multipart/form-data* as the header. Also *formData* is the body of the request, be sure to add all the params here, including the File you want to send of course.

#### **How do I handle CRUD operations in a List returned by Restangular?**

The best option for doing CRUD operations with a list, is to actually use the "real" list, and not the promise. It makes it easy to interact with it.

Let's see an example :).

````javascript
// Here we use then to resolve the promise.
Restangular.all('users').getList().then(function(users) {
  this.users = users;
  var userWithId = _.find(users, function(user) {
    return user.id === 123;
  });

  userWithId.name = "Gonto";
  userWithId.put();

  // Alternatively delete the element from the list when finished
  userWithId.remove().then(function() {
    // Updating the list and removing the user after the response is OK.
    this.users = _.without(this.users, userWithId);
  });

});
````

When you actually get a list by doing

````javascript
this.owners = house.getList('owners').$object;
````

You're actually assigning a Promise to the owners value. As Angular knows how to process promises, if in your view you do an *ngFor of owners variable, results will be shown once the promise is resolved (Response arrived).
However, changes to that promise that you do from your HTML won't be seen in the component, as it's not a real array. It's just a promise of an array.

#### Removing an element from a collection, keeping the collection restangularized

While the example above removes the deleted user from the collection, it also overwrites the collection object with a plain array (because of `_.without`) which no longer knows about its Restangular attributes.

If want to keep the restangularized collection, remove the element by modifying the collection in place:

```javascript
userWithId.remove().then(function() {
  var index = $scope.users.indexOf(userWithId);
  if (index > -1) this.users.splice(index, 1);
});
```

#### How can I access the `unrestangularized` element as well as the `restangularized` one?

In order to get this done, you need to use the `responseExtractor`. You need to set a property there that will point to the original response received. Also, you need to actually copy this response as that response is the one that's going to be `restangularized` later

````javascript
Restangular.provider.setResponseExtractor(function(response) {
  var newResponse = response;
  if (_.isArray(response)) {
    _.forEach(newResponse, function(value, key) {
      newResponse[key].originalElement = _.clone(value);
    });
  } else {
    newResponse.originalElement = _.clone(response);
  }

  return newResponse;
});
````
Alternatively, if you just want the stripped out response on any given call, you can use the .plain() method, doing something like this:

````javascript

this.showData = function () {
  baseUrl.post(someData).then(function(response) {
    console.log(response.plain());
  });
};
````

**[Back to top](#table-of-contents)**

# Server Frameworks

Users reported that this server frameworks play real nice with Restangular, as they let you create a Nested RESTful Resources API easily:

* Ruby on Rails
* CakePHP, Laravel and FatFREE, Symfony2 with RestBundle, Silex for PHP
* Play1 & 2 for Java & scala
* Dropwizard for Java
* Restify and Express for NodeJS
* Tastypie and Django Rest Framework for Django
* Slim Framework
* Symfony2 with FOSRestBundle (PHP)
* Microsoft ASP.NET Web API 2
* Grails Framework ([example](https://github.com/rmondejar/grails-angular-duo))

**[Back to top](#table-of-contents)**

# License

The MIT License

**[Back to top](#table-of-contents)**