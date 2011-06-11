FrameProxy.js allows to call functions/properties through browser windows/frames in a transparent manner.
It wraps functions/properties on an a window/frame and uses window.postMessage (HTML5) to invoke the same function/property on another window/frame.
This can be used to use a hidden iframe as proxy to call functions in a different domain.

## Cross-Domain requests

While the appropiate way to implement cross-domain request is using CORS http headers, many existent http APIs don't support Allow-Origin http headers.
FrameProxy.js allows to workaround cross-domain restriction in browsers.
The only requirment is to upload frameproxy.js and frameproxy.htm in the same domain you want to invoke.
frameproxy.htm gets loaded in a hidden iframe and ajax calls are proxied transparently to this frame using the standard way to comuncate between frames in HTML5, window.postMessage()

## Dependencies

* jQuery 1.5+

## Proxied Functions

By default, $.ajax, $.get, $.post and $.getJSON can be wrapped by transparent proxy functions. But any other can be added.

All invocations return a jQuery promise object so integration with jQuery is straightforward.

## Usage:

### 1. Server-side
* Host frameproxy.js and frameproxy.htm in the same domain of the service to invoke.
* Restrict access by modify frameproxy.htm, restriction can be by password, domain, uri, or allowed functions/properties.
 * options.domain, options.uri and options.expressions accept:
  * a string, '*' (all), a filter function or a RegExp
  * an Array of any of these

### 2. Client-side
* An a different domain page you can create a frameproxy client (this will load a hidden iframe):

´´´ javascript

  var proxy = new frameproxy.ProxyClient('http://myhost/frameproxy.htm')
          
	// wrap all functions in frameproxy.functions
	.wrapAll()
			  
	// or, wrap all functions in frameproxy.functions, and replace them by the proxied versions
	.wrapAll(true)
          
	// or, wrap other functions or properties 
	.wrap('navigator.userAgent', 'bloglib.getBlogComments')
  
	// or, wrap other functions or properties, and replace them by the proxied versions
	.wrap(true, 'navigator.userAgent', 'bloglib.getBlogComments')
  
	// invoked when proxy is ready to use
	.ready(function(){

		// if used .wrapAll() now you can:
		proxy.jQuery.ajax({ url: 'http://myhost/safedata' }).then(done, fail);
	  
		// if you used .wrapAll(true), the original function got replaced
		$.ajax({ url: 'mydata.json' });
	  
		// if you used .wrap('navigator.userAgent')
		proxy.navigator.userAgent().done(function(e){ alert('agent: '+e.result); });
		// note that properties are replaced by functions
	  
		// if you used .wrap(true, 'bloglib.getBlogComments')
		bloglib.getBlogComments({user:'jsmith'}).done(function(e){ alert('commments: '+e.data); });
	  
		// proxied functions return a jQuery promise
	});
	
´´´