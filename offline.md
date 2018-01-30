# Learning Progressive Web Apps from Udacity
## Install and run project
You need [git](https://git-scm.com/downloads) and [node.js](https://nodejs.org/) to start the project.
### Download from git
```
git clone https://github.com/jakearchibald/wittr
```

### Install and run (in directory)
To follow the steps, you need to enter directory `wittr` after downloading project from git.
#### Install dependencies
```
npm install
```

#### Run project
```
npm run serve
```

#### Open ports
```
localhost:8888 - Wittr  
localhost: 8889 - Tester
```
## Service Worker
### Register and callbacks

Service workers **only work with HTTPS** except _on localhost_.

```javascript
navigator.serviceWorker.register('/sw.js').then(function(reg){
  console.log('Yay!');
}).catch(function(err){
  console.log('Boo!');
});
```

It **won't re-register** if you call it twice, just _return a promise for the existing registration_.  

### Scope

You can have multiple service workers with different scopes, which is handy, when multiple projects share the same origin.

```javascript
navigator.serviceWorker.register('/sw.js', {
  scope: '/myapp/'
});
```

The service worker will control any page whose **URL begins with the scope** and will _ignore anything that don't_.  
(Trailing slash also matters, so for example, `/my-app` isn't working)  

The **default scope** is determined by _the location of the service worker script_.

### Feature detection

Service worker is **progressive enhancement** friendly. 

To check if the browser implements serviceWorker, you can use the following:

```javascript
if(navigator.serviceWorker){
  navigator.serviceWorker.register('/sw.js');
}
```

### Lifecycle

#### Initialization
Service Worker only takes control of pages when they're loaded.

So it takes two refresh to see the results of service worker:  
1. The browser downloads the script what registers the service worker.
2. The browser load the whole page through the service worker (all requests has to go through the service worker)

#### Update

If the page loads via the Service Worker, it will check for an update to the service worker in the background.  
And if it finds it has changed it becames the next version. But it doesn't take control, it waits.  
It won't take over until all pages using the current version are gone. This ensures that there's only one version of your site running a given time like native apps.

Because the service worker script goes through the browser cache, it is supposed to don't cache service worker script.
If the browser cache is more than a day for a service worker it ignores that after 24 hours and checks for updates of the service worker in use.

**Force reload service worker** without modifiyng origin: refresh while holding `Shift`

### Listening to events

#### Fetch

When the user navigates to a page within your service worker scope, all requests (html, css, js) trigger a fetch event.

Logging all requests:  
```javascript
self.addEventListener('fetch',function(){
  console.log(event.request);
});
```

### Responding to events

#### respondWith
```javascript
self.addEventListener('fetch',function(event){
  event.respondWith(
    new Response('Hello <b>world</b>', {
      headers: {'foo':'bar'}
    })
  );
});
```
`event.respondeWith` takes a _Response_ object or a _promise that resolves with a response_.

The **second parameter is an object** which has _**headers** property_.

#### fetch

It's way more modern and correct than XMLHttpRequest, for example:
```javascript
fetch('/foo').then(function(response){
  return response.json();
}).then(function(data){
  console.log(data);
}).catch(function(){
  console.log('It failed');
});
```
Fetch returns a promise that resolves to a response so it works very well with `respondWith`.  
The Fetch API performs a _normal browser fetch_ so the results may **come from the cache.**

##### More selective fetching

Here we're returning a gif on 404 error and a text message when offline.

```javascript
self.addEventListener('fetch', function(event) {
  event.respondWith(
    fetch(event.request).then(function(response) {
      if (response.status === 404) {
        return fetch('/imgs/dr-evil.gif');
      }
      return response;
    }).catch(function() {
      return new Response("Uh oh, that totally failed!");
    })
  );
});
```

## The cache API

### Open or create cache

We're opening or creating (if not exists) a cache with the same function:
```javascript
caches.open('my-stuff').then(function(cache){
  //..
});
```

That returns a promise for a cache of that name. If I haven't opened a cache of that name before, it creates one and returns it.

### Add request-response pairs to the cache

Adding single pair:
```javascript
cache.put(request, response);
```

Adding multiple pairs:
```javascript
cache.addAll([
  '/foo',
  '/bar'
]);
```

This operation is atomic. If **any of these fail to cache, none of them are added**.  

`addAll` uses `fetch` under the hood so it goes via the browser cache.

### Get something out of the cache

```javascript
cache.match(request);
```
This will return a promise for a matching response if one is found, or `null` otherwise.

```javascript
caches.match(request);
```
`caches.match` do the same, but it tries to find a match in any cache, starting with the oldest.

### When to store cache - the _install_ event

```javascript
self.addEventListener('install', function(event){
  event.waitUntil(promise);
});
```

The install event occures whi

### Example for storing and returning from cache

```javascript
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('wittr-static-v1').then(function(cache) {
      return cache.addAll([
        '/',
        'js/main.js',
        'css/main.css',
        'imgs/icon.png',
        'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
        'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
      ]);
    })
  );
});

self.addEventListener('fetch', function(event) {
  event.respondWith(
      caches.match(event.request).then(function(response){
        if (response) return response;
        return fetch(event.request);
      })
    
  );
});
```

### Deleting cache

Return caches names:
```javascript
caches.keys();
```
`caches.keys` returns promises.

Delete caches:
```javascript
caches.delete(cacheName);
```

When do we delete caches? On `activate` event:
```javascript
self.addEventListener('activate', function(event){
  event.waitUntil(
    //..
  )
});
```

#### Example of updating cache
```javascript
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('wittr-static-v2').then(function(cache) {
      return cache.addAll([
        '/',
        'js/main.js',
        'css/main.css',
        'imgs/icon.png',
        'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
        'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
      ]);
    })
  );
});

self.addEventListener('activate', function(event) {
  event.waitUntil(
    caches.delete('wittr-static-v1')
  );
});

self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
```

## Testing
### Testing words
**demo**: the app is running  
**offline**: the app is offline  
**lie-fi**: the connection is lie-fi  
**registered**: the service worker registered  
**sw-waiting**: service worker waiting  
**sw-active**: the latest service worker is active  
**html-response**: there is a html response with class `a-winner-is-me`  
