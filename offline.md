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

### Listening to events


## Testing
### Testing words
**demo**: the app is running  
**offline**: the app is offline  
**lie-fi**: the connection is lie-fi
