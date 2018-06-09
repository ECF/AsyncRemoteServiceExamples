# Async Remote Service Examples

Example OSGi services that use [OSGi R7 Async Remote Services](https://osgi.org/specification/osgi.cmpn/7.0.0/service.remoteservices.html#d0e1407).  When used with 
[ECF's Remote Services Implementation](http://wiki.eclipse.org/Eclipse_Communication_Framework_Project#OSGi_Remote_Services) 
and a [supporting distribution provider](https://wiki.eclipse.org/Distribution_Providers), the method call will be 
invoked asynchronously by the service consumer.  As described [here](https://osgi.org/specification/osgi.cmpn/7.0.0/service.remoteservices.html#d0e1407), 
return value given in the service interface has one of the Java types:  CompletableFuture, Future, CompletionStage, or Promise, then
the service method will be invoked without blocking and with an (optional) timeout.

For example, here is a 'hello' service interface:

<pre>
public interface IHello {

    String sayHello(String from, String message);
	
    CompletableFuture<String> sayHelloAsync(String from, String message);
	
    Promise<String> sayHelloPromise(String from, String message);
	
}
</pre>

Since the sayHelloAsync and sayHelloPromise methods can be used over a network by remote service consumers with a guarantee of asynchronous invocation and optionally with a given timeout.  Here is an implementation that uses the OSGi R7 specified intents to do so:

<pre>
@Component(immediate=true,property = { "service.intents=osgi.async",  // R7 osgi.async remote service property
                                       "service.exported.interfaces=*",  // RSA-required remote service property
				       "osgi.basic.timeout:Long=50000"  // timeout of 50000ms=50 seconds
									                   }
public class HelloImpl implements IHello {

...implementation of IHello...
</pre>

When the HelloImpl service instance is exported, the osgi.async property will require the distribution provider to create a proxy that
returns a CompletableFuture instance immediately, and require the remote service invocation timeout after 50 seconds.  Here is an example consumer for this service:

<pre>
void bindHello(IHello hello) {
	
    hello.sayHelloAsync("JavaAsync","Howdy Python").whenComplete((resp,except) -> {
                                                        if (except != null)
                                                            except.printStackTrace();
                                                        else
                                                            System.out.println("sayHelloAsync received result="+resp);
                                                    });
}
</pre>

When invoked, the sayHelloAsync call will immediately return a CompletableFuture<String> instance that will timeout after 50s, or call whenComplete before that timeout with either a valid resp instance or an exception.   This runtime behavior is provided via a R7-compliant distribution provider.
  
This repostiory contains this example, as well as other examples of both synchronous and asynchronous remote services.



