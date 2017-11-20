# Rxjava

## Holy trinity of rxJava

Map: R -> T

Flatmap: R -> Observable\<T\>

Compose: Observable\<R\> -> Observable\<T\>



## Publish Operator: convert an ordinary Observable into a connectable Observable

A connectable `Observable` resembles an ordinary `Observable`,
except that it does not begin emitting items when it is subscribed to,
but only the `Connect` operator is applied to it.
In this way you can prompt an `Observable` to begin emitting items at a time of your choosing.
(And this published `Observable` can be used more than once in a given rxjava expression)



## concatMap Operator

like `flatmap`, but respects order of incoming data; buffers values if necessary before emitting



## UI-enabling classes of RxJava

Class `RxView` has static helper methods to rx-ify view events.
The code can subscribe to an event to call an observer to do some work.
For example, button clicks can become observable.
This, in combination with `.map()` and `.buffer()` can be very powerful.
Class `RxTextView` has static methods to rx-ify text change events of text view.
The code can subscribe to the text change to process it.
This, in combination with `.debounce()` can be a handy way to process input when the user is done typing.



## RxJava + RetroFit (square)

The RetroFit annotations allow your web service API to return RxJava `Observable` results!



## Two-way data-binding for TextViews

`Processor` that multicasts all subsequently observed items to its current `Subscriber`s.
`PublishSubject`: use `PublishProcessor.create()` to subscribe to next changes to apply to a textview



## Simple and Advanced Polling

This utilizes `Flowables` to play the role of the observable.  `Flowable` objects implement the Reactive Streams Pattern

	https://dzone.com/articles/what-are-reactive-streams-in-java

“Reactive Streams, on the other hand, is a specification. For Java programmers, Reactive Streams is an API.
 Reactive Streams gives us a common API for Reactive Programming in Java.”

	http://blog.danlew.net/2016/01/25/rxjavas-repeatwhen-and-retrywhen-explained/

The `Flowable` “publishes” a stream of messages on some interval to which subscribers can subscribe.
An example of what to use a `Flowable` for is a notification handler.




## Simple and Advanced Exponential Backoff

When errors occur, retries with increasing delays may be in order.
Implementing `Function<Throwable, Publisher<?>>()` allows a flowable to take a throwable as input and return a
publisher as output. When a task is to be repeated, then a disposable subscriber can be subscribed to by a flowable.
The flowable can call the `delay()` builder method to introduce the exponential back-off algorithm of choice.



## Form Validation

Composing the use of `Flowable.combineLatest()`, `RxTextView`, and `DisposableSubscriber`,  form validation can be
100% rx-ified. `RxTextView.textChanges()` creates an observable of text changes of each form field.
`Flowable.combineLatest()` combines 3 source `Publisher`s by emitting an item that aggregates the latest
`Values` of each of the source `Publisher`s each time an item is received from any of the source `Publisher`s.

Personally, I can see where this would simplify/remove a lot of listener code, and the related asynchronous,
supporting code.



## Pseudo-caching

Demonstrates how the selector + merge + takeUntil is the best way to start observables in parallel.
This takes data from the disk cache until the network cache begins emitting.  Then the disk cache emissions are ignored.

    https://twitter.com/JakeWharton/status/786363146990649345



## RxBus

Create an event bus that can publish events and subscribers can anonymously subscribe to them.
For example, use (+/-) buttons to set a number.
For example, use tap count to validate that a form field has minimum number of characters.
For example, load data just-in-time when switching screens.

	http://blog.kaush.co/2014/12/24/implementing-an-event-bus-with-rxjava-rxbus/
	http://blog.kaush.co/2015/01/05/debouncedbuffer-with-rxjava/
	http://blog.kaush.co/2015/01/21/rxjava-tip-for-the-day-share-publish-refcount-and-all-that-jazz/



## Persist Observable data on Activity rotations

This technique uses the fragment manager of the main activity to reference a retained,
non-UI fragment over lifecycle changes of the main fragment.  This is made possible
with the setRetainInstance(true) method in the onCreate() of the worker fragment.

There are two options on what RxJava object to use to track the emissions of the worker fragment.
Either use a `ConnectableFlowable` (initiated with `publish()`/`connect()`) or use two `PublishProcessor`
(initiated with `takeUntil()`).

The kotlin example shows the use of android lifecycle view model and view model provider
as the mechanism to retain an `Observable` while a fragment is in scope.



## Volley request of network call

`Observable`s require blocking/synchronous functions.  Volley can get an asynchronous request and
wrap it in a synchronous, `RequestFuture` object to `get()` data.  Then it is a simple matter of
subscribing to the data using a `DisposableSubscriber` and a `Flowable` publisher to emit the `Observable` information.



## Pagination

Two approaches are shown where a disposable publisher wraps calls to the adapter that the recycler view delegates to
to get its items.



## Simple Timeout

The `timeout()` builder method can be chained into the building of the `Observable` so that a hard timeout limit can be
set. The optional `ObservableSource` is the fallback `Observable` to use in case of error, most likely for
error-handing.



## Setup and Teardown Resources

The `Flowable.using(observableFactory, resourceFactory, disposeFunction)` is a concise way of opening a resource,
consuming it, and cleaning up the source.
