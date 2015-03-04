#scanthng.js 
## Identify Products & Thngs directly from a (mobile) browser...

`scanthng.js` is an [evrythng.js](https://github.com/evrythng/evrythng.js) module that lets you identify Products and Thngs. By using a blend of cutting-edge HTML5 and our backend product recognition service, it allows a (mobile) browser to take a picture of an object, a QR code or a barcode and recognize them as [EVRYTHNG Products or Thngs](https://dashboard.evrythng.com)!

## Setting your Thngs and Products to work with scanthng.js

### Prerequisites
Before using scanthng you'll need:

* [An EVRYTHNG developer account](https://dashboard.evrythng.com)
* To create an Application as `scanthng.js` operates on a per Application basis. Note: you need to create a Project in [dashboard](https://dashboard.evrythng.com/projects).

### Supported Devices

The following mobile browsers are currently supported by `scanthng.js`:

* Android 3.0+ browser
* Chrome for Android 0.16+
* iOS version 6+ (Chrome or Safari, except for 8.0 and 8.0.1)
* Firefox Mobile 10.0+
* IE 10+

### Using QR codes

QR codes can be used to identify both Products and Thngs (i.e., unique instances of Products). To enable this all you need to do is to create a Thngs or a Product (via our API or dashboard) and enable a Redirection. This basically creates a short identity for your object and stores it directly in a QR code.

You can now use `scanthng.js` to identify the QR of this Thng or Product!

### Using 1D barcodes

Usually a 1D barcode identifies a type of product (aka SKU) and not an instance. However, the EVRYTHNG engine supports identifying both Thngs and Products based on 1D barcodes.

To enable this, edit the `data` field of your Thng or Product and add an Identifier. `type` must match the type of barcode you want to read, currently we support the following types:
* `ean_13`
* `ean_8`
* `upc_8`
* `upc_13`

The `value` field must match the full number on the barcode, e.g., 3057640100178.

You can now use `scanthng.js` to identify the 1D barcode of of this Thng or Product!

### Using image recognition

Image recognition allows you to recognize Products simply by taking a picture of the product itself. Unlike 1D and QR code recongnition, image recognition is not enabled as a default in your account and requires a premium account. [Contact us to enable it for your account](https://evrythng.com/contact-us/).

If you do have this feature enabled, you can activate image recognition for any Product through the dashboard by clicking on "Setup image recognition" on the Product page.

##Adding scanthng.js to your web app

###Dependencies

`scanthng.js` is a module of [`evrythng.js`](https://github.com/evrythng/evrythng.js), our main Javascript SDK.

###Adding link to script

To add `scanthng.js` to your project, you can just use our CDN to serve the file by using a script tag like this:

    <script src='//d10ka0m22z5ju5.cloudfront.net/toolkit/scanthng/scanthng-2.0.4.js'></script>

**Note**: For `scanthng.js` to work, you must load `evrythng.js` first.

###Installing as [Bower component](http://bower.io)

If you're using Bower in your project, simply run

    bower install scanthng

`Scanthng.js` (and `evrythng.js` if it's not installed yet) will be downloaded and installed in your project's components folder.
Now add it to your project:

    <script src="bower_components/scanthng/scanthng.js"></script>

**Note**: Remember to load `evrythng.js` first!

## Basic usage - scanning an object

Triggering an identification action is a two-step process. First of all, we instantiate an App with `evrythng.js` like this:

    var app = new EVT.App(APP-KEY-HERE);
    
then, we initialise a ScanThng instance:

    var st = new EVT.ScanThng(app);

Finally, we call the `identify` method on the instance we just created and use a promise to fetch the results:

    st.identify()
        .then(function(result) {
            // Do something on success
          },
          function(error) {
            // Do something on error
          });

This will trigger the full identification process, allowing the user to take a picture, then processing it to ensure best results and sending an API request to Evrythng API. 
Promises are the preferred style, but callbacks are also supported:

    st.identify(
        {}, 
        function(result) {
          // success callback
        }, 
        function(error) {
          // error callback
        }
      );

**Note**: Due to browser limitations, the `identify` method without supplied image data **must** be called as a result of a user action - a click event handler or similar.

###Simplistic usage example

```html
<!DOCTYPE html>
<html>
  <body>
    <button id="identify">Identify</button>

    <script src="http://cdn.evrythng.net/toolkit/evrythng-js-sdk/evrythng.js"></script>
    <script src="http://cdn.evrythng.net/toolkit/scanthng/scanthng-2.0.4.js"></script>
    <script type="text/javascript">    
    (function(){
      // Initialise Evrythng.js App
      var app = new EVT.App('YOUR-APP-API-KEY');
      // Initialise Scanthng
      var st = new EVT.ScanThng(app);

      // Add click event handler to start identification when user clicks the button
      var el = document.getElementById('identify');
      el.addEventListener('click', function(){
        /*
         * We use a promise but callbacks are also supported:
         * s.identify(options, successCb, errorCb);
         */
        st.identify({
            type: 'objpic', // options are: objpic, 1dbarcode, qrcode
            redirect: false // 'true' means we get redirected to the App 
                            // corresponding to this Product
          })
          .then(function(result){
            // Do something with the results, like loading the Product/Thng
            // information from the EVRYTHNG API
            console.log('SUCCESS', result);
          },
          function(error){
            console.log('ERROR', error);
          });
      })
    })();
    </script>
</body>
</html>
```

## Advanced usage - decoding existing image

The `identify` method accepts an optional imageData argument which bypasses the user interaction and jumps straight to image conversion and API request. The argument must be a string containing a base64 encoded image.

    var imageData = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAA...';
    st.identify(imageData)
      .then(function(result) {
          // Do something on success
        },
        function(error) {
          // Do something on error
        });

Of course we can still supply options and/or callbacks:

    st.identify(
      imageDataUrl,
      { option1: value1}
      function(result) {
        // Do something on success
      },
      function(error) {
        // Do something on error
      }
    );

## Options

`scanthng.js` supports the following configuration options.

These options can be passed as parameters to each call of the identify method

    st.identify({ option1 : value1 }) ...

Or set as default for all calls:

    st.setup({ option1: value1 });
    st.identify() ...

### type
Type: `String`
Default: `qrcode`

Indicate the type of image that the user is supposed to be scanning. Accepts a string with any of the following values: `qrcode`, `1dbarcode` or `objpic`. `objpic` is the option to indicate for scanning product labels.

### redirect
Type: `Boolean`
Default: `true`

Indicates whether the library should automatically redirect user to the redirection URL associated with the scanned Thng or Product. This URL can be set in the [dashboard](https://dashboard.evrythng.com) on any Product or Thng page.

### threshold
Type: `Integer`
Default: `0`
Range: `0..99`

If `type` is set to `objpic`, you can set an additional parameter to decide whether you want the response to contain only the best result or a list of matching results ordered by descending score (which is a percentage).
The value specified in the `threshold` parameter is used to specify what deviation from the strongest score other items may be within in order to be included in the response.

Example: image recognition resulted in three matches:

* Product_1, score: 80
* Product_2, score: 70
* Product_3, score: 50
* Product_4, score: 10

Depending on the `threshold` value, the output of identify() will be different:
* `threshold = 0` will return a single match, `Product1` object
* `threshold = 5` will return a list with only the first match: [`Product1`]
* `threshold = 10` will return a list of matches in the `data` field: `[ Product1, Product2 ]`
* `threshold = 50` will return a list of matches in the `data` field: `[ Product1, Product2, Product3 ]`

**Note**: Setting this option to a positive value will disable automatic redirection and creating scan action if more than one match was found.

### timeout
Type: `Integer`
Default: `10000`

Sets the timeout for AJAX calls and geolocation, in ms.

### imageConversion
    imageConversion : {
      greyscale: Boolean,
      resizeTo: Integer
    }
    
#### imageConversion.greyscale
Type: `Boolean`
Default: `true`
    
Indicates whether the library should to send a black and white version of the scanned image for identification.
If you do not need to distinguish similar images with different colors, this yields better and faster results.

#### imageConversion.resizeTo
Type: `Integer`
Default: `240`
Range: `144..`
    
Sets the maximum *smaller* dimension of the image (in pixels, automatically resized) to be sent to the server for recognition. The best trade-off between speed and quality is currently around 240.

### spinner
    spinner: {
      enabled: true,
      appendTo: document.getElementsByTagName('body')[0],
      options: {
        length: 30,
        radius: 48,
        hwaccel: true
      }
    }

`scanthng.js` uses the [`spin.js`](http://fgnass.github.io/spin.js/) library to display a configurable spinner.

####enabled
Type: `Boolean`
Default: `true`

Indicates whether to display the built-in spinner. Set to `false` to disable it.

####appendTo
Type: `DOM Element`
Default: `document.getElementsByTagName('body')[0]`

Reference to DOM element our spinner will be attached to. If invalid or null, spinner will be attached to the body.

####options
Type: `Object`
Default: `{ length: 30, radius: 48, hwaccel: true }`

Spinner options as described in [`spin.js` documentation](http://fgnass.github.io/spin.js/).

###createAnonymousUser
Type: `Boolean`
Default: `false`

If enabled, `scanthng.js` will try to create an Anonymous User and save it in local storage (falling back to cookies) for subsequent requests. For convenience, this User will be added to the output of the `identify()` method.

The most common use case for this is easily tracking users from the beginning, by device, without forcing them to create an account or login with Facebook in our "experience" app. Obviously, Anonymous Users are not as "valuable" as full App Users, because we don't store their personal details, but in some situations that's good enough.

###createScanAction
Type: `Boolean`
Default: `false`

If enabled, `scanthng.js` will try to create a Scan Action after identifying the Thng or Product. It uses `EVT.settings.geolocation` to decide whether to ask for device location. If user allows this, the precise location will be recorded in this Action, otherwise the Engine will guess a broad location from IP.
If this Scan Action triggered any Reactor rules (which now can work using the precise location from user's device, if it was provided), the reactions will be added to the output of the `identify()` method.
Now, if one of those reactions was a redirection and the `redirect` option is set, `scanthng.js` will redirect the user to URL defined in the reaction instead of the default one.
**Note**: this option will be ignored if `threshold` is set to a positive value.
