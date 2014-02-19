angular-block-ui
============
A simple AngularJS module that allows you to block user interaction on AJAX requests. Blocking is done automatically for each http request and/or manually via an injectable service.

#### Dependencies
Besides AngularJS (~1.2.4), none.  

#### Usage
By default the module will block the user interface on each pending request made from the browser. This behavior can be modified in the configuration.
 
It's also possible to do the blocking manually. The blockUI module exposes a service by the same name. Access to the service is gained by injecting it into your controller or directive:

    angular.module('myApp').controller('MyController', function($scope, blockUI) {
      // A function called from user interface, which performs an async operation.
      $scope.onSave = function(item) {
    
        // Block the user interface
        blockUI.start();

        // Perform the async operation    
        item.$save(function() {
      
          // Unblock the user interface
          blockUI.stop();
          
        });
      };
    });

BlockUI service methods
=======================

#### start
The start method will start the user interface block. Because multiple user interface elements can request a user interface block at the same time, the service keeps track of the number of start calls. Each call to start() will increase the count and every call to stop() will decrease the value. Whenever the count reaches 0 the block will end.

*Note: By default the block is immediately active after calling this method, but to prevent trashing the user interface everytime a button is pressed, the block is visible after a short delay. This behaviour can be modified in the configuration.*

**Arguments:**

* **message** (string)
Indicates the message to be shown in the overlay. If none is provided, the default message from the configuration is used.

#### stop
This will decrease the block count. The block will end if the count is 0.

#### reset
The reset will force a unblock by setting the block count to 0.

#### message
Allows the message shown in the overlay to be updated while to block is active.

#### done
Queues a callback function to be called when the block has finished. This can be useful whenever you wish to redirect the user to a different location while there are still pending AJAX requests.

**Arguments:**

* **callback** (function)
The callback function to queue.

BlockUI overlay template
========================

The html and styling of the builtin template is kept barebone. It consist of two divs (overlay and message):

    <div ng-show="blockCount > 0" class="block-ui-overlay" ng-class="{ 'block-ui-visible': blocking }"></div>
    <div ng-show="blocking" class="block-ui-message">{{ message }}</div>

A custom template can be specified in the module configuration.

BlockUI module configuration
============================

The configuration of the BlockUI module can be modified via the **blockUIConfigProvider** during the config phase of your Angular application:

    angular.module('myApp').config(function(blockUIConfigProvider) {
      
      // Change the default overlay message
      blockUIConfigProvider.message('Please stop clicking!');
      
      // Change the default delay to 100ms before the blocking is visible
      blockUIConfigProvider.delay(100);
      
    });

### Methods

#### message
Changes the default message to be used when no message has been provided to the *start* method of the service. Default value is *'Loading ...'*.

    // Change the default overlay message
    blockUIConfigProvider.message('Please wait');

#### delay
Specifies the amount in milliseconds before the block is visible to the user. By delaying a visible block your application will appear more responsive. The default value is *250*.

    // Change the default delay to 100ms before the blocking is visible ...
    blockUIConfigProvider.delay(100);
    
    // ... or completely remove the delay
    blockUIConfigProvider.delay(1);
    
#### template
Specifies a custom template to use as the overlay.

    // Provide a custom template to use
    blockUIConfigProvider.template('<div class="block-ui-overlay">{{ message }}</div>');

#### templateUrl
Specifies a url to retrieve the template from. *The current release only works with pre-cached templates, which means that this url should be known in the $templateCache service of Angular. If you're using the grunt with html2js or angular-templates, which I highly recommend, you're already set.*

    // Provide the custom template via a url
    blockUIConfigProvider.templateUrl('my-templates/block-ui-overlay.html');

#### autoBlock
By default the BlockUI module will start a block whenever the Angular *$http* service has an pending request. If you don't want this behaviour and want to do all the blocking manually you can change this value to *false*.

    // Disable automatically blocking of the user interface
    blockUIConfigProvider.autoBlock(false);

#### resetOnException
By default the BlockUI module will reset the block count and hide the overlay whenever an exception has occured. You can set this value to *false* if you don't want this behaviour.

    // Disable clearing block whenever an exception has occured
    blockUIConfigProvider.resetOnException(false);