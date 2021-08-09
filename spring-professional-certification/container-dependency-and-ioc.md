# Container, Dependency, and IoC

## ❓Question1: What is dependency injection and what are the advantages?

Dependency Injection is a technique of creating software in which objects do not create their dependencies on themself, instead, objects declare dependencies that they need and it is an external object job or framework job to provide concrete dependencies to objects.

### 📋 Types of Dependency Injection

* Constructor injection
* Setter injection
* Interface injection

### 📋 __Advantages of using dependency injection

* Increases code reusability
* Increases code readability
* Increases code maintainability
* Increases code testability
* Reduces coupling
* Increases cohesion

{% embed url="https://imgur.com/Io3wn2V" %}



## ❓Question2: What is a pattern? What is an anti-pattern? Is dependency injection a pattern?

A Software Design Pattern is a reusable solution often, commonly occurring problem in software design. It is a high-level description of how to solve the problem, that can be used in many different situations. Design patterns often represent best practices that developers can use to solve common problems.

### 📋Some examples of commonly used design patterns from GoF Design Patterns

* Factory Method
* Builder
* Template Method
* Strategy
* Observer
* Visitor
* Facade
* Composite

{% hint style="info" %}
🧙♂ Dependency Injection is a pattern that solves the problem of flexible dependencies creation.
{% endhint %}

{% hint style="danger" %}
⛔ Anti-pattern is an ineffective and counter-productive solution to an often occurring problem.
{% endhint %}

### ⚠ Examples of Anti-patterns in Object-Oriented Programming

* God Object
* Sequential coupling
* Circular dependency
* Constant interface

## ❓Question3: What are an interface and the advantages of making use of them in Java? Why are they recommended for Spring beans?

OOP Definition – An interface is a description of actions that an object can do, it is a way to enforce actions on an object that implements the interface

Java Definition – An interface is a reference type, which contains collections of abstract methods. The class that implements the interface must implement all methods from this interface, or it needs to declare methods as abstract if the object does not know how to implement the specified method.

### 📋Java Interface may contain also

* Constants
* Default Method \(Java 8\)
* Static Methods
* Nested Types

### 📋Advantages of using interfaces in Java

* Allows decoupling between contract and its implementation
* Allows declaring contract between callee and caller
* Increases interchangeability
* Increase testability

### 📋Advantages of using interfaces in Spring

* Allows for use of JDK Dynamic Proxy
* Allows implementation hiding
* Allows to easily switch beans

{% embed url="https://imgur.com/4uafm2k" %}

## ❓Question4: What is meant by application context?

Application Context is a central part of Spring application. It holds bean definitions and contains a registry of application components. It allows you to retrieve assembled and configured beans.

### 📋Application Context

* Initiates Beans
* Configure Beans
* Assemblies Beans
* Manages Beans Lifecycle
* Is a Bean Factory
* Is a Resource Loader
* Has ability to push events to registered even listeners
* Exposes Environment which allows resolving properties

### 📋Common Application Context types

* AnnotationConfigApplicationContext
* AnnotationConfigWebApplicationContext
* ClassPathXmlApplicationContext
* FileSystemXmlApplicationContext
* XmlWebApplicationContext

{% embed url="https://imgur.com/4fp6DDQ" %}

## ❓Question5: What is the concept of a "container" and what is its lifecycle?

The container is an execution environment that provides additional technical services for your code to use. Usually, containers use the IoC technique, which allows you to focus on creating the business aspect of the code, while technical aspects like communication details \(HTTP, REST, SOAP\) are provided by the execution environment.

Spring provides a container for beans. It manages the lifecycle of the beans and also provides additional services through the usage of Application Context.

### 📋Spring Container Lifecycle

* Application is started
* Spring container is created
* Container reads configuration
* Beans definitions are created from the configuration
* BeanFactoryPostProcessors are processing bean definitions
* Instances of Spring Beans are created
* Spring Bean is configured and assembled－resolve property values and inject dependencies
* BeanPostProcessors are called
* Application runs
* Application gets shutdown
* Spring Context is closed
* Destruction callbacks are invoked

## ❓Question6: How are you going to create a new instance of an ApplicationContext?

### 📋Non-Web Application

* AnnotationConfigApplicationContext
* ClassPathXmlApplicationContext
* FileSystemXmlApplicationContext

### 📋Web Application

* Servlet2: web.xml, ContextLoaderListener, DispatcherServlet
* Servlet3: XmlWebApplicationContext
* Servlet3: AnnotationConfigWebApplicationContext 

### 📋Spring Boot

* SpringBootConsoleApplication: CommandLineRunner
* SpringBootWebApplication: Embedded Tomcat

## ❓Question7: Can you describe the lifecycle of a Spring Bean in an ApplicationContext?

### 1 Context is Created

1. Beans Definitions are created base on Spring Bean Configuration
2. BeanFactoryPostProcessors are invoked

### 2 Bean is Created

1. An Instance of Bean is created
2. Properties and Dependencies are set
3. BeanPostProcessor: postProcessBeforeInitialization gets called
4. @PostConstruct method gets called
5. InitializingBean: afterPropertiesSet method gets called
6. @Bean\(init Method\) method gets called
7. BeanPostProcessor: postProcessAfterInitialization gets called

### 3 Bean is Ready

### 4 Bean is Destroyed

1. @PreDestroy method gets called
2. DisposableBean: destroy method gets called
3. @Bean\(destroy method\) method gets called

## ❓Question8:  How are you going to create an ApplicationContext in an integration test?

### 1 Make sure that you have spring-test dependency added

{% code title="pom.xml" %}
```markup
<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <scope>test</scope>
</dependency>
```
{% endcode %}

### 2 Add Spring Runner to your test

```java
@RunWith(SpringRunner.class)
```

### 3 Add Context Configuration to your test

```java
@ContextConfiguration(classes = ApplicationConfiguration.class
```

## ❓Question9: What is the preferred way to close an application context? Does Spring Boot do this for you?

### 📋Standalone Non-Web Applications

* Register Shutdown hook by calling ConfigurableApplicationContext\#registerShutdownHook – Recommended way
* Call ConfigurableApplicationContext\#close

### 📋Web Application

* ContextLoaderListener will automatically close context when the web container will stop the web application

### 📋Spring Boot

* Application Context will be automatically closed
* The Shutdown hook will be automatically registered
* ContextLoaderListener applies to Spring Boot Web Applications as well

## ❓Question10:  Can you describe: Dependency injection using Java configuration? Dependency injection using annotations \(@Component, @Autowired\)? Component scanning, Stereotypes and Meta-Annotations? Scopes for Spring beans? What is the default scope?

### 🎯Dependency Injection using Java Configuration

When using Dependency Injection using Java Configuration you need to explicitly define all your beans and you need to use @Autowire on @Bean method level to inject dependencies.

```java
@Configuration
public class ApplicationConfiguration {
    @Bean
    @Autowired
    public SpringBean1 springBean1(SpringBean2 springBean2, SpringBean3 springBean3) {
        return new SpringBean1(springBean2, springBean3);
    }

    @Bean
    public SpringBean2 springBean2() {
        return new SpringBean2();
    }

    @Bean
    public SpringBean3 springBean3() {
        return new SpringBean3();
    }
}
```

### 🎯Dependency injection using annotations

1 Create classes annotated with @Component annotations

{% code title="SpringBean1.java" %}
```java
@Component
public class SpringBean1
```
{% endcode %}

{% code title="SpringBean2.java" %}
```java
@Component
public class SpringBean2
```
{% endcode %}

{% code title="SpringBean3.java" %}
```java
@Component
public class SpringBean3
```
{% endcode %}

2 Define dependencies when required

{% code title="SpringBean1.java" %}
```java
@Autowired
private SpringBean2 springBean2;
@Autowired
private SpringBean3 springBean3;
```
{% endcode %}

3 Create Configuration with Component Scanning Enabled

{% code title="ApplicationConfiguration.java" %}
```java
@ComponentScan
public class ApplicationConfiguration 
```
{% endcode %}

### 🎯Component Scanning

Process in which Spring is scanning Classpath in search for classes annotated with stereotypes annotations \(@Component, @Repository, @Service, @Controller, …\) and based on those creates beans definitions.

{% tabs %}
{% tab title="Simple" %}
Simple component scanning within Configuration package and all subpackages

```java
@ComponentScan
public class ApplicationConfiguration
```
{% endtab %}

{% tab title="Advanced" %}
Advanced Component Scanning Rules

```java
@ComponentScan(
        basePackages = "com.spring.professional.exam.tutorial.module01.question10.annotations.beans",
        //basePackageClasses = SpringBean1.class,
        includeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*Bean.*"),
        excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*Bean1.*")
)
public class ApplicationConfigurationAdvanced
```
{% endtab %}
{% endtabs %}

### 🎯Stereotypes

Stereotypes are annotations applied to classes to describe role which will be performed by this class. Spring discovered classes annotated by stereotypes and creates bean definitions based on those types.

📋 Types of stereotypes

* Component – generic component in the system, root stereotype, candidate for autoscanning
* Service – class will contain business logic
* Repository – class is a data repository \(used for data access objects, persistence\)
* Controller – class is a controller, usually a web controller \(used with @RequestMapping\)

### 🎯Meta-Annotations

Meta-annotations are annotations that can be used to create new annotations.

@RestController annotation is using @Controller and @ResponseBody to define its behavior

{% code title="@RestController" %}
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
    @AliasFor(annotation = Controller.class)
    String value() default "";
}
```
{% endcode %}

### 🎯Scopes of Spring Beans

| Scope | Description |
| :--- | :--- |
| Singleton | Single Bean per Spring Container - Default |
| Prototype | New Instance each time Bean is Requested |
| Request | New Instance per each HTTP Request |
| Session | New Instance per each HTTP Session |
| Application | One Instance per each ServletContext |
| Websocket | One Instance per each WebSocket |

## ❓Question11: Are beans lazily or eagerly instantiated by default? How do you alter this behavior?

### 🎯Lazy and Eager Instance Creation vs Scope Type

{% tabs %}
{% tab title="Singleton" %}
**Singleton Beans** are eagerly instantiated by default
{% endtab %}

{% tab title="Prototype" %}
**Prototype Beans** are lazily instantiated by default \(instance is created when bean is requested\)

{% hint style="info" %}
🧙♂ however, if Singleton Bean has dependency on Prototype Bean, then Prototype Bean Instance will be created eagerly to satisfy dependencies for Singleton Bean
{% endhint %}
{% endtab %}
{% endtabs %}

### 🎯Altering Behavior

🧠Create classes annotated with @Component annotations

```java
@ComponentScan(lazyInit = true)
```

* Setting lazyInit to true, will make all beans lazy, even Singleton Beans 
* Setting lazyInit to false \(default\), will create Singleton Beans Eagerly and Prototype Beans Lazily

🧠You can also change default behavior by using @Lazy annotation

* @Lazy annotation takes one parameter - Whether lazy initialization should occur
* By default @Lazy is used to mark bean as lazily instantiated
* You can use @Lazy\(false\) to force Eager Instantiation – use case for @ComponentScan\(lazyInit = true\) when some beans always needs to be instantiated eagerly

🧠@Lazy can be applied to

* Classed annotated with @Component – makes bean Lazy or as specified by @Lazy parameter
* Classes annotated with @Configuration annotation – make all beans provided by configuration lazy or as specified by @Lazy parameter
* Method annotated with @Bean annotation – makes bean created by method Lazy or as specified by @Lazy parameter

## ❓Question12: What is a property source? How would you use @PropertySource?

### 📋PropertySource is Spring Abstraction on Environment Key-Value pairs, which can come from

* JVM Properties
* System Environmental Variables
* JNDI Properties
* Servlet Parameters
* Properties File Located on Filesystem
* Properties File Located on Classpath

{% hint style="info" %}
🧙♂ You read properties with usage of **@PropertySource** or **@PropertySources** annotation
{% endhint %}

```java
@PropertySources({
@PropertySource("file:${app-home}/app-db.properties"),
@PropertySource("classpath:/app-defaults.properties")
})
```

{% hint style="info" %}
🧙♂ You access properties with usage of **@Value** annotation
{% endhint %}

```java
@Value("${db.host}")
private String dbHost;

@Value("${java.home}")
private String javaHome
```

## ❓Question13: What is a BeanFactoryPostProcessor and what is it used for? When is it invoked? Why would you define a static @Bean method? What is a ProperySourcesPlaceholderConfigurer used for?

🎯 BeanFactoryPostProcessor is an interface that contains single method postProcessBeanFactory, implementing it allows you to create logic that will modify Spring Bean Metadata before any Bean is created. BeanFactoryPostProcessor does not create any beans, however it can access and alter Metadata that is used later to create Beans.

🎯BeanFactoryPostProcessor is invoked after Spring will read or discover Bean Definitions, but before any Spring Bean is created.

🎯 Because BeanFactoryPostProcessor is also a Spring Bean, but a special kind of Bean that should be invoked before other types of beans get created, Spring needs to have ability to create it before any other beans. This is why BeanFactoryPostProcessors needs to be registered from static method level.

```java
@Bean
public static CustomBeanFactoryPostProcessor customerBeanFactoryPostProcessor() {
return new CustomBeanFactoryPostProcessor();
}
```

🎯 PropertySourcesPlaceholderConfigurer is a BeanFactoryPostProcessor that is used to resolve properties placeholder in Spring Beans on fields annotated with **@Value\("${property\_name}"\)**.

## ❓Question14: What is a BeanPostProcessor and how is it different to a BeanFactoryPostProcessor? What do they do? When are they called? What is an initialization method and how is it declared on a Spring bean? What is a destroy method, how is it declared and when is it called? Consider how you enable JSR-250 annotations like @PostConstruct and @PreDestroy? When/how will they get called? How else can you define an initialization or destruction method for a Spring bean?

### ❓What is a BeanPostProcessor and how is it different to a BeanFactoryPostProcessor? What do they do? When are they called?

🎯**BeanPostProcessor** is an interface that allows you to create extensions to Spring Framework that will modify Spring Beans objects during initialization. 

📋This interface contains two methods

* postProcessBeforeInitialization
* postProcessAfterInitialization

{% hint style="info" %}
🧙♂Implementing those methods allows you to modify created and assembled bean objects or even switch object that will represent the bean.
{% endhint %}

🎯 Main difference compared to BeanFactoryPostProcessor is that BeanFactoryPostProcessor works with Bean Definitions while BeanPostProcessor works with Bean Objects.

### 📋BeanFactoryPostProcessor and BeanPostProcessor in Spring Container Lifecycle

1. Beans Definitions are created based on Spring Bean Configuration
2. **BeanFactoryPostProcessors** are invoked
3. Instance of Bean is Created
4. Properties and Dependencies are set
5. **BeanPostProcessor**::**postProcessBeforeInitialization** gets called
6. **@PostConstruct** method gets called
7. InitializingBean::afterPropertiesSet method gets called
8. **@Bean**\(initMethod\) method gets called
9. **BeanPostProcessor**::**postProcessAfterInitialization** gets called

### ❓What is a BeanPostProcessor and how is it different to a BeanFactoryPostProcessor? What do they do? When are they called?

🎯Recommended way to define **BeanPostProcessor** is through static @Bean method in Application Configuration. This is because **BeanPostProcessor** should be created early, before other Beans Objects are ready.

```java
@Bean
public static CustomBeanPostProcessor customBeanPostProcessor() {
return new CustomBeanPostProcessor();
}
```

{% hint style="info" %}
🧙♂It is also possible to create BeanPostProcessor through regular registration in Application Configuration or through Component Scanning and @Component annotation, however because in that case bean can be created late in processes, recommended way is options provided above.
{% endhint %}

### ❓What is an initialization method and how is it declared on a Spring bean?

🎯Initialization method is a method that you can write for Spring Bean if you need to perform some initialization code that depends on properties and/or dependencies injected into Spring Bean.

📋 You can declare Initialization method in three ways

* Create method in Spring Bean annotated with **@PostConstruct**
* Implement **InitializingBean**::**afterPropertiesSet**
* Create Bean in Configuration class with **@Bean** method and use **@Bean\(initMethod\)**

### ❓What is a destroy method, how is it declared?

🎯Destroy method is a method in Spring Bean that you can use to implement any cleanup logic for resources used by the Bean. Method will be called when Spring Bean will be taken out of use, this is usually happening when Spring Context is closed.

📋You can declare destroy method in following ways

* Create method annotated with **@PreDestroy** annotation
* Implement **DisposableBean**::**destroy**
* Create Bean in Configuration class with **@Bean** method and use **@Bean\(destroyMethod\)**

### ❓Consider how you enable JSR-250 annotations like @PostConstruct and @PreDestroy?

🎯When using AnnotationConfigApplicationContext support for **@PostConstruct** and **@PreDestroy** is added automatically.

{% hint style="info" %}
🧙♂ Those annotations are handled by CommonAnnotationBeanPostProcessor with is automatically registered by AnnotationConfigApplicationContext.
{% endhint %}

### ❓When/how will they \(initialization, destroy methods\) get called?

1Context is Created

1. Beans Definitions are created based on Spring Bean Configuration
2. **BeanFactoryPostProcessors** are invoked

2Bean is Created

1. Instance of Bean is Created
2. Properties and Dependencies are set
3. **BeanPostProcessor**::**postProcessBeforeInitialization** gets called
4. **@PostConstruct** method gets called
5. **InitializingBean**::**afterPropertiesSet** method gets called
6. **@Bean\(initMethod\)** method gets called
7. **BeanPostProcessor**::**postProcessAfterInitialization** gets called

3Bean is Ready to use

4Bean is Destroyed \(usually when context is closed\)

1. **@PreDestroy** method gets called
2. **DisposableBean**::**destroy** method gets called
3. **@Bean\(destroyMethod\)** method gets called

## ❓Question15: What does component-scanning do?

### 🎯Component Scanning

Process in which Spring is scanning Classpath in search for classes annotated with stereotypes annotations \(@Component, @Repository, @Service, @Controller, …\) and based on those creates beans definitions.

🎯Simple component scanning within Configuration package and all sub packages

```java
@ComponentScan
public class ApplicationConfiguration {
}
```

🎯Advanced Component Scanning Rules

```java
@ComponentScan(
basePackages = "com.spring.professional.exam.tutorial.module01.question15.advanced.beans",
includeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*Bean"),
excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*(Controller|Service).*")
)
public class ApplicationConfiguration {
}
```

## ❓Question16: What is the behavior of the annotation @Autowired with regards to field injection, constructor injection and method injection?

**@Autowired** is an annotation that is processed by AutowiredAnnotationBeanPostProcessor, which can be put onto class constructor, field, setter method, or config method. Using this annotation enables automatic Spring Dependency Resolution that is primarily based on types.

**@Autowired** has a property required which can be used to tell Spring if dependency is required or optional. By default, dependency is required. If **@Autowire**d with required dependency is used on top of constructor or method that contains multiple arguments, then all arguments are considered required dependency unless the argument is of type Optional, is marked as **@Nullable**, or is marked as **@Autowired\(required = false\)**.

If @**Autowired** is used on top of Collection or Map then Spring will inject all beans matching the type into Collection and key-value pairs as BeanName-Bean into Map. Order of elements depends on the usage of **@Order**, **@Priority** annotations, and **implementation of the Ordered interface**.

### 📋@Autowired uses the following steps when resolving the dependency

1. Match exactly by type, if only one is found, finish.
2. If multiple beans of the same type are found, check if any contains @Primary annotation, if yes, inject @Primary bean and finish.
3. If no exactly one match exists, check if @Qualifier exists for the field, if yes use @Qualifier to find matching bean.
4. If still no exactly one bean found, narrow the search by using bean name.
5. If still no exactly one bean is found, throw an exception \(NoSuchBeanDefinitionException, NoUniqueBeanDefinitionException, …\).

### 🎯@Autowired with field injection is used like this

* Autowired fields can have any visibility level
* The injection is happening after Bean is created but before any init method \(@PostConstruct, InitializingBean, @Bean\(init Method\)\) is called
* By default field is required, however, you can use Optional, @Nullable, or @Autowired\(required = false\) to indicate that field is not required

### 🎯@Autowired can be used with constructors like this

```java
@Autowired
public RecordsService(DbRecordsReader recordsReader, DbRecordsProcessor recordsProcessor) {
this.recordsReader = recordsReader;
this.recordsProcessor = recordsProcessor;
}
```

{% hint style="info" %}
🧙♂Constructor can have any access modifier \(public, protected, private, package-private\).
{% endhint %}

If there is only one constructor in a class, there is no need to use @Autowired on top of it, Spring will use this default constructor anyway and will inject dependencies into it.

If a class defines multiple constructors, then you are obligated to use @Autowired to tell Spring which constructor should be used to create Spring Bean. If you will have a class with multiple constructors without any of constructors marked as @Autowired then Spring will throw NoSuchMethodException.

By default all arguments in constructors are required, however, you can use Optional, @Nullable, or @Autowired\(required = false\) to indicate that parameter is not required.

### 🎯@Autowired can be used with method injection like this

```java
public void setRecordsReader(DbRecordsReader recordsReader) {
this.recordsReader = recordsReader;
}
```

**@Autowired** method can have any visibility level and also can contain multiple parameters. If the method contains multiple parameters, then by default it is assumed that in the @Autowired method all parameters are required. If Spring will be unable to resolve all dependencies for this method, NoSuchBeanDefinitionException or NoUniqueBeanDefinitionException will be thrown.

When using **@Autowired\(required = false\)** with a method, it will be invoked only if Spring can resolve all parameters.

If you want Spring to invoke the method only with arguments partially resolved, you need to use the @Autowired method with parameters marked as Optional, @Nullable, or @Autowired\(required = false\) to indicate that this parameter is not required.



