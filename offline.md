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


## Testing
### Testing words
**demo**: the app is running  
**offline**: the app is offline  
**lie-fi**: the connection is lie-fi  
**registered**: the service worker registered  
**sw-waiting**: service worker waiting  
**sw-active**: the latest service worker is active  
**html-response**: there is a html response with class `a-winner-is-me`  
