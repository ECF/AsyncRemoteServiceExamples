# Async Remote Service Examples

Example OSGi services that use [OSGi R7 Async Remote Services](https://osgi.org/specification/osgi.cmpn/7.0.0/service.remoteservices.html#d0e1407).  When used with 
[ECF's Remote Services Implementation](http://wiki.eclipse.org/Eclipse_Communication_Framework_Project#OSGi_Remote_Services) 
and a [supporting distribution provider](https://wiki.eclipse.org/Distribution_Providers), the method call will be 
invoked asynchronously by the service consumer.  As described [here](https://osgi.org/specification/osgi.cmpn/7.0.0/service.remoteservices.html#d0e1407), 
return value given in the service interface has one of the Java types:  CompletableFuture, Future, CompletionStage, or Promise, then
the service method will be invoked without blocking and with an (optional) timeout.
