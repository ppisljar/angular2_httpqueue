# angular2_httpqueue
Angular2 HTTP service which handles offline status


idea is to make a layer on top of Http which is transparent to the user but comes with this additional features:
- connection tracking
    observable keeps track of its connection status. if one call fails (timesout or host unreachable) it will mark connection as down
    and will not try to make another call until connection is back up or ping_delay ms pass
    it can also do periodic checks on connection (if ping_url is provided)
- exposes connection status
- guarantees that request will be executed once the connection is back (in case its down)
- exposes the list of requests in progress and allows setting the list
    which allows us to store its state in localStorage and then restore it next time application loads

this way the consumer should have less problems writting application that will also work offline
when user goes temporary offline, he would continue to use the application
application would notify him about the offline status (binding to the getStatus() of this service)
some UI features could also be disabled based on this status

when he would normally execute http.get and get an error, here he would be sure that once connection is back his data will be updated
he would still get error notification, when he could inform the user that the data he is seeing could not be up to date due to offline status

this comes to full power with update put and delete request. we can work offline, creating new entries (lets say in our ToDo app)
when we come back online all data will be sent to the server in the same order as we did the actions.

we can provide the user with information about how many requests are still pending and for example when he tries to leave the pge
we could notify him about pending requests and offer him an option to save them and send them later, or discard them


### example usage

1. import HttpQueue
```
  import {HttpQueue} from './app/services/http-queue.service';
```
2. register provider in bootstrap
```
  bootstrap(App, [
    ...HTTP_PROVIDERS,
    HttpQueue
  ])
```
3. import HttpQueue in your components constructor
```
  constructor(public httpQueue: HttpQueue) {
```
4. use httpQueue just as you would use http
```
  Observable.interval(10000)
      .switchMap(() => this.httpQueue.get('/', {resend: true}))
      .subscribe(res => {
        console.log(res);
      })
```

### more

you can read more about this on [my blog here](http://peter.pisljar.si/#/en/projects/angular2_httpqueue)

you can view a running example on my [playground here](http://peter.pisljar.si/playground/angular2_httpqueue)
