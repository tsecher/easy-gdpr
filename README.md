# Easy-GDPR

Easy-GDPR is a javascript tool allowing users to manage their personal data services according to the General Data Protection Regulation (GDPR). Easy-GDPR is highly customizable and allows you to integrate it easily in your website.


## Installation 

### Using NPM
    
    npm install easy-gdpr

### Using CDN 
Easy-GDPR allows you to use onlty the elements that your website need. So the user don't load the code for services you don't declare. That's why the easy-GDPR core, templates and services are in different js files. So you need to load each one of the package you will need.

```html
<!-- Add the core : -->
<script type="text/javascript" src="//cdn.jsdelivr.net/gh/tsecher/easy-gdpr@master/dist/egdpr.js">

<!-- Add the template you want. Here is the default template : -->
<script type="text/javascript" src="//cdn.jsdelivr.net/gh/tsecher/easy-gdpr@1.x/src/templates/default/dist/default.js">
<link rel="stylesheet" href="//cdn.jsdelivr.net/gh/tsecher/easy-gdpr@1.x/src/templates/default/dist/default.css">
```


## Getting started

### Initialize easy-gdpr
To initialize the basic easy-gdpr tool you just have to load the core, set a template and then call init the function.

Using a es2015 packager like wepback
_(if you use a CDN, easy-gdpr core is available with the global variable `eGDPR`.)_
```javascript
// Import the core.
import { eGDPR } from "easy-gdpr"
// Import the default template
import { DefaultTemplate } from "easy-gdpr/src/templates/default/Default"

eGDPR
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

Here, we initialize the core and the view of easy-gdpr. However we did not add any service. So for the moment, the popup will just show a message telling there is no personal data service declared on your website.




> __Why do you have to init a template ? Why does it not implemented by default ?__
>
> Well... easy-GDPR is designed to allow you to customize the template, so you can choose any template or create your own. If a default template was loaded by default, it would also be loaded event if you wouldn't use it. This would digrease teh loading performance. So that's why, by default, there is no template defined. Yeah, that's true, there is a line code necessary before initialization, but we think it is the best compromise.

## Declare services
Now we want to declare services, so we can use the `addService` method which can be chained.
For performance, the best way is to declare services before initialisation. But you can declare services whenever you want.

```javascript
import { GTAGService } from "easy-gdpr/src/services/gtag/gtag"
import { MatomoService } from "easy-gdpr/src/services/matomo/matomo"


eGDPR
    // ----- Add services : 
    .addService( new GTAGService('U-*******') )
    .addService( new MatomoService('//matomo.php') )

    // -----
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

You can also use the `createService` method. The difference between `addService` and `createService` is : 
- `addService` declares the service and returns the egdpr core object
- `createService` declares the service and returns the service object. It can be usefull if you want to setup the service.



## Customize

### Group Services

You can group services if you want, using the `eGDPR.createGroup()` method. The group object allows you to add service into it.

```javascript
import { GTAGService } from "easy-gdpr/src/services/gtag/gtag"
import { MatomoService } from "easy-gdpr/src/services/matomo/matomo"

// Create services :
const gta = eGDPR.createService( new GTAGService('U-*******') )
const matomo = eGDPR.createService( new MatomoService('//matomo.php') )

// Create group :
const trackingGroup = eGDPR.createGroup('tracking_group', 'User Tracking', 'List of user tracking services')
// Add services to the group :
trackingGroup
    .addService(gta)
    .addService(matomo)

// Then initialize
eGDPR
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

### Handle services and groups order
By default, the services are ordered according to the order of their declaration. You can manage this order using the `setWeight()` method.


```javascript
import { GTAGService } from "easy-gdpr/src/services/gtag/gtag"
import { MatomoService } from "easy-gdpr/src/services/matomo/matomo"

// Create services :
const gta = eGDPR.createService( new GTAGService('U-*******') )
const matomo = eGDPR.createService( new MatomoService('//matomo.php') )
matomo.setWeight( -1 ) // Place matomo first.

// Then initialize
eGDPR
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

### Update texts and translations
You can easily redefine the default texts. Texts are managed with translations and you can override it easily. Each template use its own translations, such as services.
So you can override translation with an object according to the text_id : 
```json
{
    "text_id": "Your customized text"
}
```
The text ID can be found in the dom in the attribute `data-egdpr-l`.

So you can override translation using `eGDPR.addTranslation` : 
```javascript
import { GTAGService } from "easy-gdpr/src/services/gtag/gtag"
import { MatomoService } from "easy-gdpr/src/services/matomo/matomo"

// Create services :
const gta = eGDPR.createService( new GTAGService('U-*******') )
const matomo = eGDPR.createService( new MatomoService('//matomo.php') )

// Then initialize
eGDPR
    .addTranslations({
        'Your personal data': 'Manage your choice'
    })
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

### Create your service
#### Simple service.
You can create a simple service using the `addService` or `createService` with a simple object : 
```javascript

// Then initialize
eGDPR
    // -------- Add a simple servuce
    .addService(
        {
            id: 'my_own_service',
            name: 'My Service',
            description: 'My own service description',
            start: function(){
                console.log('My own service is starting')
            }
        }
    )
    // ----
    .setTemplate( new DefaultTemplate() ) // Initialize the view
    .init() // Initialize the core
```

#### Reusable service
You can also create a service class. This class will be added in a single file and will be totally independant to easy-gdpr and your website as the easy-gdpr philosphy. This modular way allows you to reuse and import the service only when you need it.
Easy-gdpr brings you some tools to generate this class. To create a new service you can use this command : 

```npm run egdpr new service```

This will create a directory with auto completed elements in ./easy-gdpr/services/{your service id}. Then you only ha to fill the start method. 


### Create your template
In the same way as the reusable service you can create a reusable template. To generate a new template, you can use the following command : 

```
npm run egdpr new template
```

This will generate a class that provides some method that will be used by the view.


In addition to the template generation, you can generate translation files with the command :
```
npm run egdpr translate {id of the language}
```
This will parse your template class and extract the strings called by `this.html()`.


## Placeholders
Easy-gdpr allows you to manage the DOM content depending on a service. For example, you want to add an iframe that should be visible only when a specific service has been enabled by the user. In this case, you can use the power of the `noscript` tag and the attribute `data-egdpr-visible-if-enabled`. 
The content of the noscript tag will not be played because javascript is enabled. Easy-gdpr will turn this tag into a div when the related service will be enabled. Then the content will be display.

In the following example, we add a youtube embed iframe. In this case, the iframe will only be displayed when the `youtube` service runs. : 
```html
<body>
    <!-- the hidden content until the youtube service is not enabled -->
    <noscript data-egdpr-visible-if-enabled="youtube">
        <iframe width="90%" height="315" src="https://www.youtube.com/embed/YOUTUBE_ID" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </noscript>

<!-- the visible content used as a placeholder until the youtube service is not enabled -->
    <div data-egdpr-hidden-if-enabled="youtube">
        Please enable the youtube service to play this video.
        <div data-egdpr-service-enable="youtube">Enable</div>
    </div>
</body>
```