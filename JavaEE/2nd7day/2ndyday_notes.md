# <span style='color: olive'>Day 8/90</span>

Date : 14-April-2023



## <span style='color: indigo'>CDI - Context & Dependency Injection</span>

###  <span style='color: grey'>Context  & Scopes in action</span>

`@RequestScoped` : Every time an HTTP request is called, container should cause creation of a new bean. 

> So we expect to see a **new** hashcode every time this bean is created.

```java
@RequstScoped
public class RequestScope {
    public String getHashCode() {
        return this.hashCode() + " ";
    }
}
```

<hr> 


`@SessionScoped` : 1) This bean is bound to an HTTP session. 2) Also, if we open the request in another browser for instance, a new session will be created.

> 1) So we expect to see a **single** hashcode repeated for a given session.
> 2) So we expect to see **another** hashcode repeated for that session.
>
> > Session scope, manages the bean per client!

```java
@SessionScoped
public class SessionScope {
    public String getHashCode() {
        return this.hashCode() + " ";
    }
}
```

<hr>


`@ApplicationScoped` : It is singleton and only one contextual instance is created throughout the lifetime of application.

> So we expect to see the **same** hashcode, despite making a new request in new browser, tab, etc ...

```java
@ApplicationScoped
public class ApplicationScope {
    public String getHashCode() {
        return this.hashCode() + " ";
    }
}
```

<hr>


`@DependentScoped` : Since it is dependent, wherever we inject it, it should inherit that context.

> 

```java
public class DependentScope {
    public String getHashCode() {
        return this.hashCode() + " ";
    }
}
```

____________________________________________________________Now Test It____________________________________________________________

```java
// annotations ...
public class ScopesBean {
    @Inject
    private RequestScope requestScope;
    
    @Inject
    private SessionScope sessionScope;
    
    @Inject
    private ApplicationScope applicationScope;
    
    @Inject
    private DependentScope dependentScope;
    
    // other methods hidden for brevity ...
    // getters setter ...
}
```

### <span style='color: grey'>༼ つ ◕_◕ ༽つSummarize</span>

*Contexts* in CDI refer to a set of related objects that share a lifecycle and are managed by the container. *Scopes*, on the other hand, define the lifecycle of a bean instance within a particular context. It is basically a way to associate (manage) a bean with a given context.

There are several built-in scopes in CDI:

1. `@ApplicationScoped` - Beans with this annotation have a lifecycle that is tied to the application itself. They are created when the application starts up and destroyed when the application shuts down.
2. `@SessionScoped` - Beans with this annotation have a lifecycle that is tied to a user session. They are created when a user session is established and destroyed when the session ends.
3. `@ConversationScoped` - Beans with this annotation have a lifecycle that is tied to a specific conversation between the user and the application. They are created when the conversation starts and destroyed when the conversation ends.
4. `@RequestScoped` - Beans with this annotation have a lifecycle that is tied to a single HTTP request. They are created when the request is received and destroyed when the response is sent.
5. `@Dependent` - Beans with this annotation have a lifecycle that is tied to the lifecycle of the object that injects them. They are created when the injecting object is created and destroyed when it is destroyed.

### <span style='color: grey'>CDI Producers</span>

In CDI, a producer method is a method that creates and returns a bean instance for injection. It allows you to customize how a bean instance is created, including how its dependencies are injected.

To define a producer method, you first annotate it with the `@Produces` annotation. You then declare the bean type of the produced instance using the return type of the method. You can also use additional annotations to specify the scope of the produced instance or qualifier annotations to further identify the produced instance.

For example, suppose you have a `Logger` interface and want to inject an implementation of this interface into your application. You could define a producer method like this:

```java
import javax.enterprise.inject.Produces;
import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class LoggerFactory {
    @Produces
    public Logger createLogger() {
        return new ConsoleLogger();
    }
}
```

This producer method creates an instance of `ConsoleLogger` and makes it available for injection wherever an instance of `Logger` is required. The `@ApplicationScoped` annotation on the `LoggerFactory` class specifies the scope of the produced instance.

> What is a CDI producer?
>
> It is an API construct, that allows developers, to tend classes that we don't own into CDI managed and injectable beans. Whatever the method returns, it is eligible for CDI injection.
>
> - The producer method **MUST** have a return type and should not be of type void.
> - If the method marked with `@Produces` has parameters, the property inside the parameter **MUST** be an injectable bean.

#### <span style='color: #734F96'>Scoping Returned Beans</span>

what will be the scope of the producer methods? It will be `@Dependent`.

#### <span style='color: #734F96'>Field Producers</span>

same as method producer, it just depends on your use case. 

- If you need to do some kind of work in the method using let's say the parameters, use method producer.

- an example :

  ```java
  @Produces
  @PersisteceContext
  EntityManager entityManager;
  
  // now whereever we want to inject
  // ...
  @Inject
  EntityManager manager;
  ```

  

#### <span style='color: #734F96'>Qualifying Beans</span>

How do we clarify an ambiguity to the CDI runtime?

We use CDI qualifiers to avoid ambiguity.

in our previous example "CDI Qualifiers - Day 7",

```java
// ...
@Produces
@Police // this will solve the ambiguity
public Salute getSalutation() {
    return new Police();
}
```

#### <span style='color: #734F96'>Disposers</span>

Let's explain this with example :

```Java
// ...
@Produces
public List<String> getLuckyDish() {
    List<String> dishes = List.of("food1","food2","food3");
    return dishes;
}

public void dispose(@Disposes List<String> dishes) {
    dishes = null;
}
```

Disposers basically inverse of producers. It gives the chance to custom cleaner. It disposes of the bean created.

#### <span style='color: grey'>༼ つ ◕_◕ ༽つSummarize</span>

CDI *Producer* 

- is a way to transform mostly classes that we don't own into CDI managed beans.

- We can use either producer <u>method</u> or <u>field</u>. whatever is returned is eligible for injection. 
- We can also Scope the method or field. 
- We can also prevent ambiguity by using qualifiers.

CDI *Disposer* 

- is a void method that has parameters with `@Disposes` and the method will inject the custom producer and you can do custom cleanup on it in you disposer method.

## <span style='color: #8B0000'>Done</span>

- [x] Day 8/90 ==> [JavaEE: Day 8/90 - Context and Dependency Injection (CDI)](https://www.linkedin.com/pulse/javaee-day-890-context-dependency-injection-cdi-seyed-ali)



# <span style='color: olive'>Day 9/90</span>

Date : 15-April-2023



## <span style='color: indigo'>CDI - Context & Dependency Injection</span>

### <span style='color: grey'>CDI Interceptors</span>

In the context of the CDI API, interceptors are a type of component that can intercept method invocations and perform additional operations before or after the method is called.

CDI interceptors are defined using an annotation, `@Interceptor`, and can be used to implement cross-cutting concerns such as logging, security, or performance monitoring across multiple beans in an application.

To define an interceptor, you would create a class and annotate it with `@Interceptor`. Within the class, you can define methods that intercept calls to other classes and execute additional logic.

CDI interceptors operate using a chain-of-responsibility pattern. When a method is invoked on an intercepted bean, the interceptor chain is executed, with each interceptor potentially modifying the behavior of the method before passing control to the next interceptor in the chain. The final interceptor in the chain invokes the original method.

To declare interceptors in JavaEE, we need to do 2 things :

1. We need to declare an `@InterceptorBinding` (an interface)

   ```java
   // example
   @InterceptorBinding
   @Retention(RetentionPolicy.RUNTIME)
   @Target({ElementType.METHOD, ElementType.TYPE})
   @Inherited
   public @interface Logged {
   }
   ```

   `@InterceptorBinding` : is an annotation that we'll be using to trigger an interceptor or to trigger a method to be intercepted or a class to be intercepted.

2. After that, we need to declare an interceptor binding code. We can think of it as the implementation of it. This interceptor binding code will run, when a method or class is intercepted.

   ```java
   // from previous example
   // Bind interceptor to this class
   @Logged // the bining interface we declared earlier
   @Interceptor
   @Priority(Interceptor.Priority.APPLICATION)
   public class LoggedInterceptor {
       @Inject
       private Logger logger;
       
       // mocked user; could be from db
       private String user = "user";
       
       @AroundInvoke
       public Object logMethodCall(InvocationContext context) throws Exception {
           // for example, log user who called method and time
           logger.log(Level.INFO, "User {0} invoked {1} method at {2}", new Object[]{user, context.getMethod().getName(), LocalDateTime.now()});
           return context.proceed();
       }
   }
   ```

   `@AroundInvoke` : The `@AroundInvoke` annotation indicates that the `logMethodCall` method intercepts all method invocations. 

   `InvocationContext` : The `InvocationContext` parameter provides access to information about the intercepted method, such as its name and parameters.

   > Exposes contextual information about the intercepted invocation and operations that enable interceptor methods to control the behaviorof the invocation chain.
   >
   > ~ Java Documentation

==> So now wherever we want to intercept a method, in this example, we'll annotate that method with `@Logged` annotation. Anytime the method is invoked, the container will come to the `@AroundInvoke` marked method and execute what's in the method. After whatever logic it has done, It will then proceed to the target method (`return context.proceed()`).

> Interceptors are similar to Aspect-Oriented-Programming (AOP). It is a way for us to intercept calls to a method or entire methods in a given class. 
>
> For example : Security logging or auditing and then we can decide wether the method should proceed or not. 

#### <span style='color: #734F96'>Activating Using Priority Annotation</span>

In the context of CDI interceptors, priority is a way to specify the order in which interceptors are executed when multiple interceptors are applied to a single method or class.

Interceptors with higher priority values are executed before those with lower priority values. The default priority value for an interceptor is 1000. You can specify a different priority value by using the `@Priority` annotation, which takes an integer value as its argument. 

Priority can be also declared as Integers.

There are some common conventions that you can follow. Here are some common priority values and what they might represent:

- High priority (e.g. 1000 or higher): These interceptors should be executed before most other interceptors. They might handle authentication or authorization logic, for example.
- Medium priority (e.g. 500 to 999): These interceptors might handle general-purpose cross-cutting concerns like logging or exception handling.
- Low priority (e.g. 0 to 499): These interceptors might handle less important cross-cutting concerns like caching or performance optimization.

#### <span style='color: #734F96'>Let's Run the example!</span>

We used our interceptor (`@Logged`) in :

```java
@Stateless // simple Stateless EJB class
public class AuditedService {
    @Inject
    private Logger logger;
    
    // This method will only be called after the Logged Interceptor has returned i.e. InvocationContext=>proceed
    // This annotation could also be put on the class, making every method of the class intercepted
    @Logged
    public void auditedMethod() {
    logger.log (Level.INFO, "0K so we are able to call this method after auditing took place") ;
	}
}
```

then, we used `AuditedService` bean in :

```java
@Web
public class ScopesBean implements {
    @lnject
    private AuditedService auditedService;
    
    // Producer object
    @lnject
    private Logger logger;

    // Lifecyle callback
    @PostConstruct
    private void init() {
    auditedService.auditedMethod(); // we called the intercepted method here <----
    logger.log(Level.INFO, "**********************Scopes bean called**********************");            
}
```

## <span style='color: #8B0000'>Done</span>

- [x] Day 9/90 ==> [JavaEE: Day 9/90 - Context and Dependency Injection (CDI)](https://www.linkedin.com/pulse/javaee-day-990-context-dependency-injection-cdi-seyed-ali)

 

# <span style='color: olive'>Day 10/90</span>

Date : 16-April-2023

no progress

## <span style='color: #8B0000'>Done</span>

- [x] Day 10/90 ==> [JavaEE: Day 10&11/90 - Context and Dependency Injection (CDI)](https://www.linkedin.com/pulse/javaee-day-101190-context-dependency-injection-cdi-seyed-ali)

  

# <span style='color: olive'>Day 11/90</span>

Date : 17-April-2023

no progress

## <span style='color: #8B0000'>Done</span>

- [x] Day 11/90 ==> [JavaEE: Day 10&11/90 - Context and Dependency Injection (CDI)](https://www.linkedin.com/pulse/javaee-day-101190-context-dependency-injection-cdi-seyed-ali)



# <span style='color: olive'>Day 12/90</span>

Date : 18-April-2023



## <span style='color: indigo'>CDI - Context & Dependency Injection</span>

### <span style='color: grey'>CDI Events</span>

In the CDI API, an event is a mechanism for loosely coupling components in an application. It allows one component to notify other components that something of interest has occurred, without those components needing to know anything about each other.

An event in CDI is represented by an object that carries information about the occurrence that triggered the event. When an event is fired, any observer methods that have been registered to listen for that event are called with the event object as a parameter.

Observer methods can be defined in any bean that is managed by the CDI container, and they can be annotated with the `@Observes` annotation to indicate which events they should listen for. In this way, CDI provides a flexible and extensible way to handle decoupled communication between components in an application.

> It is a way to be able to communicate a proportion of application without compile time dependency. An event will be fired with a passed payload, and then we'll have an observer expecting the payload.

### <span style='color: grey'>Event Interface</span>

The payload that we're going to pass to the `Event interface`. Simple POJO class.

```java
public class Payload {
    private String email;
    private LocalDateTime loginTime;
    // getters & setters & constructors ...
}
```



```java
@Web //we created this qualifier (annotation/interface annotation) --> @RequestScoped, @Named (for JSF)
public class EventBean {
    @Inject
    private User user; //another POJO with email & pass
    
    @Inject
    Event<Payload> plainEvent;
    
    @Inject
    @PopularStand //custom qualifire with --> @Qualifier
    private Event<Payload> eventDataEvent;
    
    @Inject
    @Admin
    private Event<Payload> conditionalEvent;
    
    public void login() {
        //Do credentials check and logic, then fire the event
        //someSecurityManager.loginUser(user.getEmail(), user.getPassword());
        
        plainEvent.fire(new Payload(user.getEmail()), user.getPassword());
        
        LocatDateTime now = LocalDateTime.now();
        System.out.println(now);

        eventDataEvent.fire(new LocalDateTime.now()));
        fireAsync = eventDataEvent.fireAsync(new EventData(user.getEmait(), long secs = ChronoUnit.SECONDS.between(now, LocalDateTime.now());
        System.out.println("It took us this number of seconds to login" + secs);

        //Qualified Observer
        conditionatEvent.fire(new LocatDateTime.now())) ;
        LocalDateTime.now()));
    }
}
```

the observer :

```java
@RequestScoped
public class EventObserver implements Serializable {
 	@Inject 
    private Logger logger;
    
    void plainEvent(@Observes Payload payload) {
        //persist in db, sent to another application outside your app
        //essentially you can do whatever you want with the event data here
        //we will just log it
        logger.log(Level.INFO, "User {0} logged-in at {1}. Logged from PLAIN_EVENT_OBSERVER", new Object[] {payload.getEmail(), payload.getLoginTime()});
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            logger.log(Level.SEVERE, null, e);
        }
    }
    
    void userLoggedIn(@Observes @PopularStand Payload payload) {
        //persist in db, sent to another application outside your app
        //essentially you can do whatever you want with the event data here
        //we will just log it
        logger.log(Level.INFO, "User {0} logged in at {1}", new Object[]{payload.getEmail(), payload.getLoginTime()});
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            logger.log(Level.SEVERE, null, e);
        }
    }
    
    void asyncObserver(@ObservesAsync @PopularStand Payload payload) {
        //persist in db, sent to another application outside your app
        //essentially you can do whatever you want with the event data here
        //we will just log it
        logger.log(Level.INFO, "User {0} logged in at {1}", new Object[]{payload.getEmail(), payload.getLoginTime()});
        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            logger.log(Level.SEVERE, null, e);
        }
    }
    
    void conditionalObserver(@Observes(notifyObserver = Reception.IF_EXISTS, during = TransactionPhase.AFTER_COMPLETEION) @Admin Payload payload) {
        logger.log(Level.INFO, "The CEO {0} logged in at {1}", new Object[]{payload.getEmail(), payload.getLoginTime()});
    }
}
```



> - Observers must be of type `void`.

#### <span style='color: #734F96'>Plain Event</span>

From previous example, `plainEvent field in bean` :

When an event is fired, **any** observer that observing for that particular event, will get notified.

From previous example, `plainEvent observer method`

the observers will observe the event, any event of that particular payload type. Anywhere. Simple as that.

This is not recommended when we have an event, and multiple observers, but you don't want all of them invoked when you want to fire this event. You may want to invoke only one of them. In this case, we use the concept of qualifiers.

#### <span style='color: #734F96'>Qualifying Events</span>

Qualified events are events that are just labeled with qualifiers, where you tell CDI runtime how one or more things are related.

from previous example `in observer class --> userLoggedIn` :

when we fire `eventDataEvent`, the `userLoggedIn` method will get invoked, but the `plainEvent` method won't get invoked because it does not have the particular qualifier, in our case `@PopularStand`.

#### <span style='color: #734F96'>Conditional Observers</span>

Out of the box, observers will be invoked once an event is fired. Once the event is fired, the container will look for that particular event's observer. There are situations where you want to invoke an observer conditionally. We can do that using notifier construct (previous example : `observer class --> conditionalObserver(...)`)

```java
void conditionalObserver(@Observes(notifyObserver = Reception.IF_EXISTS, //we're telling the container to only invoke this particular qualifier if there is a contextual bean that declaring the event observer in the context
                                   
during = TransactionPhase.AFTER_COMPLETEION) //we're also telling to also invoke it if there is a transaction and the transaction succeeds
                                  ))
```



#### <span style='color: #734F96'>Async Events</span>

we use `event.fireAsync(the payload)`.

this object returns CompleteionStage of type Payload `(CompletionStage<Payload>)` object. 

and for the observer `void asyncObserver(@ObservesAsync)`.

#### <span style='color: #734F96'>Prioritizing Observer Method Invocation</span>

use `@Priority(...)`

we can replace `...` with :

1. simple integers (the higher the number, the lower the priority)

2. or we can use CDI Interceptor API (recommended)

   `@Priority(Interceptor.Priority.APPLICATION + 200)`

## <span style='color: grey'>༼ つ ◕_◕ ༽つSummary of CDI API</span>

What I have learned from CDI API

- **Bean discovery mode :** there is a process where the container scans your applciations archive to discover beans to be managed.
- **bean.xml file :** which is what we use to set bean discovery mode. Out of the box, bean discovery mode is set to `annotated` which means CDI container will only manage beans that are annotated with specific CDI annotations.
- **Container :**  It is the runtime environment where it manages instantiaion of beans. 
- **Beans & Contextual instances :** beans are just the templates, java classes from which contextual instances are created.
- **Injection point :** The class, that the beans are being injected to. Field, constructor, method injection.
- **Lifecycle callbacks :** Mehtods that get invoked at the lifecycle of bean. PostConstruct, PreDestroy.
- **Qualifiers :** We use qualifiers to link certain things together so that the container can manage based on our need. For avoiding ambiguous beans.
- **Stereotypes :** Stereotypes are interfaces grouping together commonly used CDI annotations into one, so that when you use that one particular stereotype, you are using all those other API annnotations that we put together.
- **Context & Scopes :** A <u>Scope</u> defines the context in which a bean instance exists, and thus determines how long an instance will be preserved and when it will be destroyed. *<u>Contexts</u>* in CDI refer to the runtime environment that manages the lifecycle of a bean instance. `@ApplicationScoped, @RequestScoped, @SessionScoped, @Dependent`.
- **Producer :** 
- **Interceptors :** It is a way of implementing cross cutting concerns.
- **Event :** Helps us to write Reactive application.



## <span style='color: #8B0000'>Done</span>

- [x] Day 12/90 ==> [JavaEE: Day 12/90 - Context and Dependency Injection (CDI)](https://www.linkedin.com/pulse/javaee-day-1290-context-dependency-injection-cdi-seyed-ali)



# <span style='color: olive'>Day 13/90</span>

Date : 19-April-2023



## <span style='color: grey'>Java Persistence API (JPA)</span>

JPA stands for Java Persistence API, and it is a framework in JavaEE (also in Spring framework) that makes it easier for developers to store and retrieve data from databases. Think of it as a way to interact with a database without having to write SQL code directly. This can save time and make it easier to manage your data persistence layer.

What we are going to learn :

- Object Relational Mapping
- Entity Manger
- Query Language
- Advance JPA 

### <span style='color: navy'>Setting up Payara Server</span>

Download the payara server 5, full version and add it to the IDE. It'll show the name as Glassfish in the IDE.

### <span style='color: navy'>JPA Entity</span>

Any simple Java POJO class can be JPA entity. We just need to mark it with `@Entity` and add a field for a unique identifier i.e. `@Id`. 

Every instance of jpa entity class will reperesent as a row in db, and id will act as a unique identifier for that instance like student id for every student in a school.

```java
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Tax {
    @Id
    private Long id;
    //other fields & setters, getters, constructors
}
```



#### <span style='color: navy'>Customizing Table Mapping</span>

`@Table(name = "any_name")` : customize the name of the class that is going to be saved as table.

`@Table(name = "any_name", schema = "HR")` : It will act as a prefix for the table like => `HR.any_name`



#### <span style='color: navy'>Using Super Classes</span>

In a Enterprise application, we'll have multiple classes that map to the table. All these classes have a unique identifiers that are marked with `@Id`. Since one of the tenets of software developement is dry (don't repeat yourself), we can group all these shared and common property id's into one and extend that class all across the entities.

- create an abstract class
- mark it with `@MappedSupperClass` : this makes the class abstract i.e. there won't be any table for this class in db. Put all the properties in the marked class, into the classes that extend it.
- provide the id property

```java
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@MappedSuperclass
public abstract class AbstractEntityID {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    protected String userEmail;

    @Version
    protected Long version;
}
```

#### <span style='color: navy'>Overriding Super Class Field</span>

We want to override the id in the `AbstractEntityID` and give it another name. To do that :

```java
@OverrideAttribute(name = "name_of_the_field", column = @Column(name = "taxId"))
@Entity
public class Tax extends AbstractEntityID {
    ...
}
```

#### <span style='color: navy'>Mapping Simple Java Types</span>

JavaEE JPA API provides an easy and efficient way to map simple Java types to corresponding database columns. Simple Java types such as boolean, integer, string etc. can be easily mapped to their respective column types in the database using annotations like `@Column` and `@Basic`.

For example, if you have a Java class with a String field named "name", you can annotate it with `@Column(name="NAME")` to map it to a database column named "NAME". Similarly, you can use `@Basic` annotation to specify default mapping properties for basic types. `@Basic` annotation is optional.

If you don't provide the `@Column(name)`, JPA will give the default name i.e. the name of the java field.

#### <span style='color: navy'>Transient Fields</span>

In Java EE JPA API, a transient field is a field that is marked with the "transient" keyword and is not persisted to the database. This means that when an entity object is saved or retrieved from the database, any fields marked as transient are ignored. Transient fields are often used for storing data that is not relevant to the persistence of the object, such as derived values or temporary variables. However, it is important to note that transient fields can still be serialized and deserialized along with the entity object, so care should be taken when using them in distributed systems.

#### <span style='color: navy'>Field Access Type</span>

In Java EE JPA API, the `Field Access Type` is a way to define how entity class fields should be accessed by the JPA provider. There are two types of access: Field access and Property access.

With `Field Access`, the JPA provider accesses the fields directly, using reflection. In this case, all entity class fields must be declared as `private` and must not have any custom get/set methods. Here is an example:

```
javaCopy Code@Entity
@Access(AccessType.FIELD)
public class Employee {
    @Id
    private Long id;
    
    private String firstName;
    private String lastName;
    
    // Constructors, getters and setters
}
```

With `Property Access`, the JPA provider accesses the properties of the class using their corresponding getter and setter methods. In this case, all entity class fields must be declared as `private`, and the corresponding getter and setter methods must be implemented. Here is an example:

```
javaCopy Code@Entity
@Access(AccessType.PROPERTY)
public class Employee {
    private Long id;
    
    private String firstName;
    private String lastName;

    @Id
    public Long getId() {
        return id;
    }
    
    public void setId(Long id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }
    
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }
    
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    // Other methods
}
```

It is important to note that the default access type is `Field Access`. However, you can explicitly specify the access type using the `@Access` annotation.

#### <span style='color: navy'>Mapping Enumerator Type</span>

In Java EE JPA API, enum types can be mapped to database columns using the `@Enumerated` annotation. This annotation can be applied to a field or getter method of an entity class to specify how the enum value should be persisted in the database.

The `@Enumerated` annotation supports two modes of conversion: `ORDINAL` and `STRING`. When the `ORDINAL` mode is used, the enum value is stored as its ordinal position in the enum declaration (starting from 0). In the `STRING` mode, the enum value is stored as its string representation (i.e., the name of the enum constant).

It is important to note that the `ORDINAL` mode should only be used if the order of the enum constants is unlikely to change, as changing their order would affect the data stored in the database. (like adding another constant in the enum class.)

```java
@Entity
public class Tax {
    @Id
    @GeneratedValue(strategy = IDENTITY)
    private Long id;
    
    @Enumerated(EnumeratedType.STRING)
    private CustomEnumClass customEnumClass;
    
    ...
}
```

## <span style='color: #8B0000'>Done</span>

- [x] Day 13/90 ==> [JavaEE: Day 13/90 - Java Persistence API (JPA)](https://www.linkedin.com/pulse/javaee-day-1390-java-persistence-api-jpa-seyed-ali)



# <span style='color: olive'>Day 14/90</span>

## <span style='color: grey'>Java Persistence API (JPA) (continue)</span>

### <span style='color: navy'>Mapping Large Objects (e.g. images)</span>

The Java EE JPA API provides a powerful and flexible way to map large objects in a relational database. This is done using the `@Lob` annotation, which can be used on fields of type `String`, `byte[]`, or `Serializable`.

When a field is annotated with `@Lob`, JPA will automatically create a separate table for the large object, and store a reference to that object in the original table. This allows for efficient storage and retrieval of large objects, while still maintaining the benefits of a relational database.

It's worth noting that while `@Lob` is convenient, it should be used sparingly as it can have performance implications. For very large objects or frequent updates, it may be better to store the data in a separate table altogether.

```java
// ...
@Entity
public class Demo {
    @Lob
    private byte[] picture;
    // ...
}
```

It is better to make the large objects as lazy fetching.

```java
...
@Lob
@Basic(fetch = FetchType.LAZY)
private byte[] picture;
...
```

### <span style='color: navy'>Lazy & Eager Fetching Of Entity State</span>

Fetching is the process of retrieving data from the database and loading it into memory so it can be used by your application. In JPA, there are two types of fetching strategies: Lazy and Eager.

Lazy fetching means that JPA will only load the data when it is actually needed. For example, if you have an object that has a list of related objects, JPA will not load that list until you try to access it. This can help keep your application running fast because it saves on unnecessary loading of data.

Eager fetching, on the other hand, loads all of the data for an object and its related objects when the object is loaded from the database. This can be beneficial if you know that you will need all of the data anyway, but it can also slow down your application if you are working with large amounts of data.

In general, it's a good idea to use lazy fetching whenever you can. Lazy loading can help make your application more responsive and efficient because it only loads data when it's needed. However, there may be cases where eager loading makes more sense. For example, if you know that you will always need certain data when you load an object, eager fetching could be more efficient because you won't need to load the data later.

To implement lazy or eager fetching in JPA, you can use annotations in your code. For example, to specify that a relationship between two entities should be lazily fetched, you would use the `@OneToMany` or `@ManyToOne` annotation with the fetch attribute set to `FetchType.LAZY`. Conversely, to specify that a relationship should be fetched eagerly, you would set the fetch attribute to FetchType.EAGER.

### <span style='color: navy'>Mapping Java 8 DateTime Types</span>

In Java EE, the Java Persistence API (JPA) provides a standard way to map Java objects to relational databases. When mapping date and time values in Java 8, JPA can use the new `java.time` package which offers several new classes for representing date and time values.

### <span style='color: navy'>Mapping Embeddable classes</span>

In Java EE JPA API, you can use something called an "embeddable class" to represent complex data types within your entity classes. These embeddable classes are non-entity classes that you can embed within your entity classes.

To map an embeddable class, you just need to add the `@Embeddable` annotation at the beginning of the class definition. Then, within your entity class, you can use the `@Embedded`annotation on the corresponding field to map the embeddable class.

You can also use other JPA annotations such as `@Column` or `@Temporal` within your embeddable class to specify how its fields should be mapped.

Using embeddable classes can help you manage complex data structures within your entities without having to create additional database tables.

>  Embeddable is essentially an object that has no identity on its own, and it becomes a part of the class in which it is embedded.

## <span style='color: #8B0000'>Done</span>

- [x] Day 14/90 ===> [JavaEE: Day 14/90 - Java Persistence API (JPA)](https://www.linkedin.com/pulse/javaee-day-1490-java-persistence-api-jpa-seyed-ali)

