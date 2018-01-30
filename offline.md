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
```
navigator.serviceWorker.register('/sw.js').then(function(reg){
  console.log('Yay!);
}).catch(function(err){
  console.log('Boo!);
});
```

## Testing
### Testing words
**demo**: the app is running  
**offline**: the app is offline
**lie-fi**: the connection is lie-fi
