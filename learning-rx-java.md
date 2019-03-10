## Learning RxJava

by Thomas Nield


Please [purchase the book](https://www.amazon.com/Learning-RxJava-Thomas-Nield/dp/1787120422) and support the author.


- The core type you work with is **Observable**
- Fundamental idea: *events are data and data are events*
- Three core entities: **Observable, Observer, Operator**
- An **Observable\<T>** pushes things of type T, through a series of operators until it arrives at an **Observer**
- **Observable** can push data/events (called emissions) from virtually any source (database query, live twitter feed)
- **Emissions** are pushed one at a time all the way to Observer
- To make an Observable actually push things we need an Observer. We can use operators between Observable and Observer to manipulate each pushed item in some way. Each operator returns a new Observable derived-off the previous one but reflects that transformation
- **Lambdas** are essentially mini functions that allow us to quickly pass instructions on what action to take with each incoming item
- Java 8 Streams or Kotlin sequences pull the items while Observable pushes the items, seem subtle but the impact of a pushed based iteration is far more powerful
- When you become experienced in RxJava, you will find cases where RxJava may not be appropriate e.g. a trained Rx veteran who sees that returning List\<String> is perhaps better than returning Observable>String>

Observable passes items down a chain to an Observer and works by passing three type of events:

1. **onNext:** passes each item one at a time from the source Observable all the way down to the Observer
2. **onComplete:** communicates a completion event all the way down to the Observer, indicating that no more onNext calls will occur
3. **onError:** communicates an error up the chain to the Observer, where the Observer typically defines how to handle it. Unless retry() operator is used to intercept the error, the Observable schain typically terminates and no more emissions will occur

**Observable** contract dictates that emissions must be passed sequentially and one at a time. Emissions cannot be passed by an Observable concurrently or in parallel. This is not a limitation but a simplification to reason with.

**onComplete** communicates up the chain to the Observer that no more items are coming. Observables can indeed be infinite, and if this is the case, the onComplete event will never be called.

Technically, a source could stop emitting onNext calls and never call onComplete -> likely a bad design

The **onNext** method is a way to hand each item to the next step in the chain

onNext, onComplete and onError do not necessarily push directly to the final Observer. They can also push to an operator serving as the next step in the chain.

**Observables** no longer support emitting null values. If you need to emit a null, consider wrapping it in a Java 8 or Google Guava Optional

Since operators such as **map** and **filter** yield new Observables (derived off the original Observable and which internally use Observer implementations to receive emissions), we can chain all our returned Observables with the next operator rather than unnecessarily saving each one to an intermediary variable

**Observable.create()** can be useful in hooking into certain sources that are not reactive but you likely won’t need to use it instead we can use Observable.just() that will call onNext for each item and then invoke onComplete

**Observable.fromIterable()** to emit the items from any Iterable type such as List

Each Observable returned by an operator is internally an Observer that receives, transforms, and relays emissions to the next Observer downstream. It does not know whether the next Observer is an other operator or the final Observer at the end of the chain.

Instead of specifying a lambda argument to subscribe we can also pass it our own implementation of an Observer

Errors that happen anywhere in the Observable chain will be propagated to onError to be handled and then terminate the Observable with no more emissions

Most of the subscribe overload variants return a **Disposable**. Disposable allow us to disconnect an Observable from an Observer so emissions are terminated early, which is critical for infinite or long-running Observables

**Cold Observable** will replay the emissions to each Observer ensuring that all Observers get all the data. It’ll first play all emissions to first Observer then onComplete and then it will play all emissions to second Observer and then call onComplete

Most data driven Observables (databases, text files, JSON) are cold including .just() and .fromIterable() factories. It is important to know how a source Observable is architected e.g. RxJava-JDBC runs a query each time for each Observer which means data can change between two subscriptions

**Hot Observable** broadcasts the same emissions to all Observers at the same time. If an observer comes in afterwards some emissions were pushed, the second Observer will have missed those emissions

Hot Observable often represent events rather than finite datasets. There is a time-sensitive component where late observers can miss previously emitted date.

**ConnectableObservable** will take any observable even if it is cold and make it hot so that all emissions are played to all Observers at once: publish() to get ConnectableObservable and then call connect()

- **Observable.range():** to emit a consecutive range of integers
- **Observable.rangeLong():** to emit larger numbers
- **Observable.interval():** to emit a consecutive long emission (starting at 0)
- **Observable.future():** to turn Futures into Observables
- **Observable.empty():** to emit nothing and calls onComplete, essentially RxJava’s concept of null
- **Observable.never():** different from .empty() that it never calls onComplete, primarily used for testing and not that often in production
- **Observable.error():** to create an Observable that immediately calls onError
- **Observable.defer():** do not create the Observable until the observer subscribes, and create a fresh Observable for each observer (ensures Observer gets the latest data/parameters of Observable). You have to return Observable and deal with checked exceptions
- **Observable.fromCallable():** to defer execution of a function e.g. a function that downloads file from network or throws exception, useful when you want to return value as opposed to an Observable

**Single\<T>** is essentially an Observable\<T> that will only emit one item. It is limited only to operators that make sense for a single emission. The onSuccess essentially consolidates onNext and onComplete into a single event that accepts the one emission.

**Maybe** is just like Single except that it allows no emission to occur at all

**Completable** is concerned with action being completed, it does not receive emissions and does not have onNext or onSuccess to receive emissions, but it does have onError and onComplete

When you are working with infinite or long-running Observables, you likely will run into situations where you want to explicitly stop the emissions and dispose of everything associated with that subscription.
Finite Observables that call onComplete will typically dispose of themselves safely when they are done

The Disposable is a link between an Observable and an active Observer, and you can call its dispose method to stop emissions and dispose of all resources used for that Observer

subscribe() method will actually return a **Disposable**
**isDisposed** indicates whether it has been disposed already
**CompositeDisposable:** If you have several subscriptions that need to be disposed all at once
ObservableEmitter: Abstraction over an Observer that allows associating a resource with it

You should strive to use operators to express business logic so your code stays as reactive as possible. Operators themselves are Observers to the Observable they are called on

The operators can suppress emissions by simply not calling the onNext function downstream for a disqualified emission and therefore does not go down the chain to Observer

The **filter()** operator accepts Predicate\<T> for a given Observable\<T>. This means that you provide it a lambda that qualifies each emission by mapping it to a Boolean value and emissions with false will not go forward

Note that if all emissions fail to meet your criteria, the returned Observable will be empty, with no emissions occurring before onComplete is called

The **take()** operator has two overloads. One will take a specified number of emissions and then call onComplete. The other overload will take emissions within a specific time duration and then call onComplete

- **skip():** opposite of the take operator. It will ignore the specified number of emissions and then emit the ones that follow
- **takeWhile():** takes emissions while a condition derived from each emission is true
- **skipWhile():** skip emissions while they quality with a condition

The **takeUntil()** operator is similar to takeWhile(), but it accepts another Observable as a parameter. It will keep taking emissions until that other Observable pushes an emission. 

The **skipUntil()** operator has similar behavior. It also accepts another Observable as an argument but it will keep skipping until the other Observable emits something

- **distinct():** will emit each unique emission, but it will suppress any duplicates that follow. Equality is based on hashCode/equals implementation of the emitted objects.
- **distinctUntilChanged():** will ignore duplicate “consecutive” emissions. 
- **elementAt():** You can get a specific emission by its index specified by a Long, starting at 0, it returns a MayBe instead of a Observable because if there are fewer emissions than the sought index will be empty
- **elementAtOrError():** return a Single and will emit an error if an element at that index is not found
- **map():** does a one-to-one conversion for each emission
- **cast():** map-like operator to cast each emission to a different type
- **startWith():** allows you to insert a T emission that precedes all the other emissions
startWithArray(): if you want to start with more than one emission
- **defaultIfEmpty():** resort to a single emission if a given Observable comes out empty, we can specify a default T emission if no emissions occur when onComplete is called
- **switchIfEmpty():** specifies a different Observable to emit values from if the source Observable is empty
- **sorted():** to sort the emissions that implement Comparable\<T>. Internally it will collect all the emissions and then re-emit them in their sorted order, this can have some performance implications. If you use this against an infinite Observable you may get an OutOfMemory error
- **delay():** It will hold any received emissions and delay each one for the specified time periods
- **repeat():** will repeat subscription after onComplete a specified number of times
- **scan():** for every emission you add it to an accumulation then it will emit each incremental accumulation

**Reducing operators:** Consolidate emissions into a single emission, nearly all of these operators only work on a finite Observable that calls onComplete because typically we can consolidate only finite datasets

- **count():** it will count the number of emissions and emit through a Single once onComplete is called
- **reduce():** yields a final emission reflecting the final accumulation once onComplete is called. Depending upon which overload is used, it can yield a Single or Maybe
- **all():** verifies that each emission qualifies with a specified condition and return a Single\<Boolean>. If they all pass, it will emit True. If it encounters one that fails, it will immediately emit False
- **any():** will check whether at least one emission meets a specific criterion and return a Single\<Boolean>. The moment it finds an emission that qualifies it will emit true and then call onComplete

**contains():** will check whether a specific element (based on the hashCode/equals implementation) ever emits from an Observable. It will return a Single\<Boolean> that will emit true if it is found and false if it is not

**Collection operators:** accumulate all emissions into a collection such as a list/map and then emit that entire collection as a single emission.

- **toList():** will collect incoming emissions into a List\<T> and then push that entire List\<T> as a single emission (through Single\<List\<T>>)
- **toSortedList():** will collect the emissions into a list that sorts the items naturally based on their Comparator
- **toMap():** operator will collect emissions into Map\<K,T> where K is the key type derived off a lambda argument producing the key for each emission
- **toMultiMap():** will maintain a List of corresponding values for each key
- **collect():**


