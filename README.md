5.Fifth day task

IoC Container Internals
IoC (Inversion of Control) Container is the core of Spring. It manages the creation, configuration, and lifecycle of beans.

How it works internally:

BeanDefinition Loading:

Spring reads metadata about beans (@Component, @Service, XML, or Java Config).

Stores metadata in a BeanDefinition registry.

Bean Instantiation:

The container decides when and how to create the bean based on scope and lazy initialization.

Dependency Injection:

Constructor, setter, or field injection is applied.

BeanPostProcessors:

Spring allows modifying beans before and after initialization (postProcessBeforeInitialization, postProcessAfterInitialization).

Aware Interfaces:

Beans can implement interfaces like ApplicationContextAware or BeanNameAware to access container metadata.

Lifecycle callbacks:

@PostConstruct, InitializingBean.afterPropertiesSet(), DisposableBean.destroy(), or custom destroy-method.

2️. BeanFactory vs ApplicationContext Feature BeanFactory ApplicationContext Container type Basic IoC container Advanced IoC container Bean loading Lazy by default Eager loading (singleton beans at startup) Features Instantiation & DI only Internationalization, event publishing, AOP, etc. Common use Lightweight apps, memory-sensitive Web apps, full Spring features Interface examples BeanFactory, XmlBeanFactory ApplicationContext, AnnotationConfigApplicationContext

Takeaway:

ApplicationContext extends BeanFactory with more enterprise-level capabilities.

In almost all modern Spring apps, we use ApplicationContext.

3️. Bean Scopes: request/session in web apps

Spring supports different bean scopes:

Scope Description singleton One instance per Spring container (default) prototype A new instance every time requested request One bean per HTTP request (web apps) session One bean per HTTP session application One bean per ServletContext websocket One bean per WebSocket session

Example:

@Service @Scope("request") public class RequestScopedService { }

A new RequestScopedService is created for each HTTP request.

4️. Bean Lifecycle

Steps from creation to destruction:

Instantiation

Bean object created (constructor or factory method).

Dependency Injection

Fields, setters, or constructor dependencies are injected.

BeanNameAware / BeanFactoryAware / ApplicationContextAware

Bean gets metadata from container if implemented.

BeanPostProcessor - before initialization

postProcessBeforeInitialization is called.

Initialization

@PostConstruct or afterPropertiesSet() executes.

BeanPostProcessor - after initialization

postProcessAfterInitialization is called.

Ready to use

Bean is now fully managed by Spring.

Destruction

Singleton beans: @PreDestroy or DisposableBean.destroy() executed when container shuts down.

Prototype beans are not managed after creation.

5️. Circular Dependency Solving in Spring

A circular dependency occurs when Bean A depends on Bean B, and Bean B depends on Bean A.

How Spring handles it:

Setter Injection (field/setter)

Spring can resolve circular dependencies automatically by creating a proxy or early reference.

Constructor Injection

Circular dependencies cannot be resolved by default → will throw BeanCurrentlyInCreationException.

Workaround: use @Lazy on one dependency to delay instantiation.

Example:

@Service public class A { @Autowired private B b; }

@Service public class B { @Autowired @Lazy private A a; }

@Lazy allows Spring to break the circular dependency.

6️. @ComponentScan Deep Behavior

@ComponentScan is responsible for scanning packages to find beans (@Component, @Service, @Repository, @Controller).

Important points:

Base package scanning

@SpringBootApplication already includes @ComponentScan on the same package and subpackages.

Filters

Can include/exclude certain classes using includeFilters or excludeFilters.

Deep scanning

Spring recursively scans all subpackages of the base package.

Example:

@SpringBootApplication @ComponentScan(basePackages = {"com.medical.service", "com.medical.controller"}) public class MedicalApplication { }

Only scans specified packages.

