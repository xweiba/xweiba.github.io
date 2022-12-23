# Spring-AnnotationApplicationContext 核心注入流程
在构造方法执行完毕时, 容器就已经创建好了.

## 1. AnnotatedBeanDefinitionReader
`this.reader = new AnnotatedBeanDefinitionReader(this);` 时会注入一个 `ConfigurationClassPostProcessor`

`ConfigurationClassPostProcessor` 实现了 `BeanDefinitionRegistrarPostProcessor` 接口, `BeanDefinitionRegistrarPostProcessor` 继承了 `BeanFactoryPostProcessor`.

## 2. ClassPathBeanDefinitionScanner
`this.scanner = new ClassPathBeanDefinitionScanner(this)` 时会在 `includeFilters` 中添加一个 `new AnnotationTypeFilter(Component.class)` 用来支持 `@Component` 注解.

## 3. register(componentClasses);
将配置类注入到 `beanDefinitionMap` 中.

## 4. refresh()
真正的处理流程

### 4.1 

2. 在BeanFactory初始化完毕后开始处理 BeanDefinitionRegistrarPostProcessor, 调用 ConfigurationClassPostProcessor 的 postProcessorBeanDefinitionRegister 接口, 解析 AppConfig.class 配置类, 并将其注入为 bean.
  2.1 通过 ConfigurationClassParser


FactoryBean是一个包装了对象的Bean, 他会生成两个Beandefinition, 创建两个Bean, 一个是他本身A, 还有一个是他getObject()接口返回的对象B, 他与@Bean注解到方法上的的区别就是, 他在A对象创建Bean时, 不会调用Spring创建Bean生命周期的全部方法, 只会调用BeanPostProcessor的before方法, 因为要通过这个创建动态代理. A对象撞见Bean时会调用, B对象创建Bean时不会调用, 因为B对象是在A对象doCreateBean过程中创建的.

@DependsOn 依赖指定bean的注解, 被注解的类在bean的创建过程中会先创建此注解标记的beanName

1. new AnnotationApplicationContext(AppConfig.class)
2. AnnotationApplicationContext 构造方法调用父类构造方法创建FactoryBen对象。
3. 初始化扫描器Scanner, 并配置一个includefilter过滤器,添加 @ComponentScan 注解.
4. 使用 Spring 的类的元信息解析器解析 AppConfig.class, 这个解析器是使用  AMS 字节码解析技术实现的, 因为 classloader 解析类时会把 class 加载到jvm中. 在接下来扫描包时不是每一个class都是bean的.
5. 开始扫描bean的流程, 前先通过excludeFilter判断 AppConfig 是不是在排除中, 是直接返回,
6. 不在排除中, 再判断是否在includefilterr中, 因为有默认的 @ComponentScan , 取其值. 通过元信息解析器的方法获取值下的所有class的元信息,如b.
7. 还是会通过filter过滤器过滤, 过滤成功后会判断是否有 @Candidate 条件判断注解, 有的话在通过条件判断是否成功. 且不为接口|不包含非静态子类|不包含@lookup注解方法的抽象类, 成功再往下解析.最后将class的包名xxx.xxx.xxService存入Beandefinition的beanClass(此阶段的beanName),扫描完成返回所有的Beandefinition集合Beandefinitions. 此时只设置了名字. 
8. 遍历Beandefinition开始做真正的解析, 设置scope值, 生成baen的名字, 首先通过Component注解的值, 没有值的话通过类的短名生成(默认首字符小写. 注意如果类的前两个字符都是大写, 那么首字符不会小写, 而是直接用类名 如ABTest.class->ABTest, Abtest-> abtest)
9. 给Beandefinition设置默认值,
10. 解析 @Lazy @primary @DependsOn @Role @Description
11. 检查Spring容器中是否已经存在该Beanname, 如果有相同的的名字, 且Beandefinition相同, Beandefinition对应的class也相同, 那么会抛异常, 如果Beandefinition不同, 但class相同会忽略, 这种情况只存在于该类被两个@ComponentScan注解扫描到了, 所以会忽略, 如果在同一个@ComponentScan中出现同名的会抛出异常. 
12. Spring容器中不存在创建一个BeandefinitionHolder对象, 添加到Beandefinitions中并注册到BeandefinitionMap中.
12. 初始化完毕后开始遍历Beandefinitions, 首先合并Beandefinition, 如果Bean A有parent属性, 找到parent的Beandefinition B, 将B克隆一份, 然后将b存在的属性复制给B. 返回合并后的RootBeandefinition. 如果没有parent, 将A克隆一份, 返回RootBeandefinition, 都会存入RootBeandefinition map中. 后面都是使用这个Map的RootBeandefinition.
13. 获取到RootBeandefinition后, 判断是否为非抽象的Beandefinition(注意不是class是否是抽象类, 而是Bean的属性是否设置为了抽象的,基本没用), 非懒加载且是单例的, 开始创建Bean.
14. 创建前判断是否为FactoryBean,不是的走getBean(beanname)方法创Bean. 里面会调用doCreateBan(),内部会添加到单例池中.
15. 如果是FactoryBean, 会先创建一个带 &+beanName 的Bean, 也就是FactoryBean对应的Bean,注意他在单例池中的key没有&前缀, 然后判断FactoryBean实例化对象是否是实现了SmartFactoryBean(它继承了FactoryBean), 且实现的isEagerInit()接口返回true, 那么此时会调用getBean(beanName)去创建FactoryBean的getObject()对象B的Bean, B不会存到单例池, 存放的是factoryBeanObjectCache缓存map中.
16. 所有的非懒加载单例bean都创建完毕后, 判断单例bean是否实现了 SmartInitialzingSingleton接口. 实现了就调用接口的afterSingletonsInstantiated()方法. 这是一个可扩展的点.
17. getBean(beanName) 中, 如果传入的beanname前缀包含&会先将所有&去掉->newBeanname, 然后通过newBeanname去单例池中获取bean, 
18. 能取到Bean, 判断beanname是否包含&, 如果包含&会判断取出的bean是否是factoryBean, 不是的话抛异常.是的话就直接返回.  如果传入的beanname不带&, 那么取出factoryBean的bean后会判断是否是FactoryBean, 是的话返回它的getObject()对应的Bean.
19. 取不到就通过newBeanname创建Bean.


# doGetBean
1. tranfromeBeanName(), 将传入的beanName进行转换, 带&前缀的去掉前缀(FactoryBean), 别名的取出主id;
2. getSingleton(beanName) 获取bean. //因为绝大多数都是单例bean, 所以这里会直接去单例池去拿. 28法则 
3. 拿不到开始创建, 
4. 检查是否有父BeanFactory, 且当前RootBeandefinitionMap中没有beanname对应的bean, 调用父BeanFactory的getBean方法去获取.
5. 没有的话从RootBeandefinitionMap中通过beanName获取合并后的Beandefinition A;
6. 检查A是不是Abstract的
7. 遍历A中的DependsOns, 检查依赖的Beanname B, 检查B中是又否又通过@DependsOn注解依赖了A, 是抛出异常(循环依赖). 不是存入dependentBeanMap, key为B, value为A的beanname. 然后通过B的beanName创建bean. 创建完毕继续往下走.
8. 判断A是单例还是多例的还是其他的作用域(Scope)如Session等, 如果是单例的, 通过 getSingleton(beanna, lamba表达式), 获取bean
9. 进入 getSingleon方法. 首先通过BeanName判断单例池中有没有,有直接返回, 没有把beanname加入到正在创建中的singletonsCurrentluInCretaion集合中. 然后执行lamba表达式. lambda表达式中调用createBean()创建出真正的对象, 放入单例池中.
10. 如果是多例的先调用beforPrototypeCreation(beanName), 再调用createBean, 最后调用afterPrototypeCreation(beanName)
11. 如果是其他作用域先去Scope的缓存中scopes中获取, 获取不到去创建Scope对应的bean.

# createBean(beanName, beandefinition, argas)
1. 将 beandefinition 赋值给中间变量mbdTouse, 通过 beandefinition 进行class加载->resolvedClass.  加载时beanName还是类名, 如果不设置BeanClassLoader的话(过context.getBeanFactory().setBeanClassLoader(xxx)来设置),会使用默认的ClassLoader. 默认的classLoader首先会获取当前线程的classLoader(在加载Spring容器前通过Thread.currentThread().setClassLoader(xxxx)来设置, 默认是空的, 支持tomcat打破双亲委派的类加载器场景), 如果没加载到再获取当前类的类加载器, 如果当前类被放到了jre/lib中, 他会被bootstrapClassLoader加载, 也就是jvm加载, 在运行时是拿不到的. 还拿不到的话会调用ClassLoader.getSystemClassLoader()方法, 其实就是AppClassloader.获取到类加载器后, 再通过BeanName获取到bean的类名className. 如果设置了SpringEl表达式, 会先通过El表达式去获取Bean的class.没有的话就通过ClassLoader.loadClass(className)返回class对象resolvedClass了
2. 克隆一个BeanDefinition, 并将class对象设置进去. mbdTouse = new RootBeandefinition(beandefinition); mbdTouse.setBeanClass(resolvedClass)
3. 调用实例化前resolveBeforeInstantiation(beanName, mbdTouse)接口 InstantiationAwarBeanPosetProcessor, 会判断是否为合成Bean, 合成Bean该接口不执行., 和BeanPostProcessor接口不同, BeanPostProcessor时对象已经创建了, InstantiationAwarBeanPosetProcessor是对象创建之前. 它的接口签名是public Object Instantiation(class,beanName), 而BeanPostProcessor是public Object initialization(object, beanName), InstantiationAwarBeanPosetProcessor可以自定义对象的实例化.如果你实现了多个InstantiationAwarBeanPosetProcessor接口, 并且内部都是针对同一个bean的,那么只要有一个InstantiationAwarBeanPosetProcessor的postProcessorBeforeInstantiation(class, beanName) 返回了对象, 其他的都不会调用了. 因为这个接口就是用来自定义对象的. 注意这个接口可以返回任意对象. 
4. 如果InstantiationAwarBeanPosetProcessor接口返回了对象, 他会调用一次bean = applyBeanPostProcessorsAfterInitialization(bean, beanName); 对象实例化后的接口. BeanPostProcessor.postProcessAfterInitialization(result, beanName); 因为它要对对象做AOP. 没有创建对象执行doCreateBean(beanName, mbdToUse, args)

# doCreateBean(beanName, mbdToUse, args)
1. 首先通过beanName在factoryBeanInstanceCache中获取instanceWrapper,(有可能在本Bean创建之前，就有其他Bean把当前Bean给创建出来了（比如依赖注入过程中）), 没有的话调用 createBeanInstance(beanName, mbd, args)创建 instanceWrapper;
2. createBeanInstance(beanName, mbd, args) 中先判断有没有加载class, 没有的话先加载.
3. 通过 getInstanceSupplier 实例化
  ```java
// BeanDefinition中添加了Supplier，则调用Supplier来得到对象
Supplier<?> instanceSupplier = mbd.getInstanceSupplier();
if (instanceSupplier != null) {
	return obtainFromSupplier(instanceSupplier, beanName);
}
  ```
4. 没有的话通过 @Bean 方式创建 instanceWrapper
5. 还没有的话开始通过构造方法创建 instanceWrapper.
6. 首先判断 args 参数是否为空, 且mbd中已经找过构造方法了, 是的话如果mbd.constructorArgumentsResolved为true, 通过有参构造方法autowireConstructor(beanName, mbd, null, null);(方法内会拿到缓存好的构造方法的入参)创建instanceWrapper, 没有的话通过无参构造instantiateBean(beanName, mbd);创建.
7. 没有找过构造方法的话下面开始通过推断构造来创建 instanceWrapper
8. 首先通过 SmartInstantiationAwareBeanPostProcessor 扩展点找构造方法, 可以利用SmartInstantiationAwareBeanPostProcessor来控制用beanClass中的哪些构造方法, 比如AutowiredAnnotationBeanPostProcessor会把加了@Autowired注解的构造方法找出来
9. 通过构造方法创建 instanceWrapper
10. 实例化之后会调用合并后的扩展点 MergedBeanDefinitionPostProcessor的postProcessMergedBeanDefinition(mbd, beanType, beanName);方法. 这个里面可以通过 mdb.setInitMethed("方法名") 设置初始化方法. 通过mdb.getPropertyValues().add("orderService", new orderService()), 对属性赋值.
11. 处理循环依赖
12. 调用InstantiationAwarBeanPosetProcessor的postProcessorAfterInstrantiation(instanceWrapper.instance, beanName) 实例化后方法. 注意这里传入的obj是没有被代理的对象.此时属性还是没有注入值的.
13. 开始属性注入, 首先会执行Spring自带的依赖注入, 就是如果你的@Bean(autowired=Autowired.BY_NAME)写了autowired属性, 并设置了 Autowired.BY_NAME 或者 Autowired.BY_TYPE, 他会调用你Bean对象中的所有set方法, 通过set注入属性.注意没有属性他也会调用, ByType是通过方法入参类型调用的, ByName是通过setXXX的xxx方法注入的. 该方法可以使没有被@Component注解的类也有属性注入的能力, 因为 @Component 相对于是一个插件的能力, 如果在容器启动时不设置includeFilter @Component, 那么Spring就没有属性注入的能力了. 而且如果你使用xml方式启动容器, 且不开启注解配置, 那么没有 @Component 是不是都不能属性注入了? 
14. 然后调用InstantiationAwarBeanPosetProcessor的的postProcessorProperties() 方法. Spring有一个AutowiredAnnotationBeanPostProcessor实现类完成了@Autowired 注解注入的能力. CommonAnnotationBeanPostProcessor实现类完成了 @Resource @WebServiceRef @EJB 注解注入的能力
15. 在AutowiredAnnotationBeanPostProcessor中, 如果你在前期扩展点通过BeanDefinition.getPropertyValues().add("xxx", new XXX())给xxx赋值, 那么@Autowired注解不会再对这个属性赋值.
16. 属性填充完毕后执行 initializeBean(beanName, exposedObject, mbd); 初始化真正的Bean
17. 调用invokeAwareMethods(String beanName, Object bean)方法, 里面会调用 ((BeanNameAware) bean).setBeanName(beanName); ((BeanClassLoaderAware) bean).setBeanClassLoader(bcl); ((BeanFactoryAware) bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
18. 调用初始化前 wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName); 执行所有的 BeanPostProcessors.postProcessBeforeInitialization(result, beanName);
19. 初始化前中有一个Spring默认的 CommonAnnotationBeanPostProcessor 实现类的构造方法中会设置setInitAnnotationType(PostConstruct.class),setDestroyAnnotationType(PreDestroy.class); 设置完成后, 在调用postProcessBeforeInitialization方法时就会处理@PostConstruct注解和@PreDestroy注解. 然后在他的父类InitDestroyAnnotationBeanPostProcessor的buildLifecycleMetadata的doWithLocalMethods中, 会找到所有的@PostConstruct注解的方法, 加入到initConstructMethod集合中, 注意这里是循环调用, 如果有父类, 会把父类的方法放到前面,先执行父类方法, 返回LifecycleMetadata, 并通过LifecycleMetadata.invokeDestroyMethods(bean, beanName);执行方法. 
20. 初始化前还有一个实现类 ApplicationContextAwareProcessor, 如果bean是EnvironmentAware, EmbeddedValueResolverAware, ResourceLoaderAware, ApplicationEventPublisherAware, MessageSourceAware, ApplicationContextAware, ApplicationStartupAware其中某一个接口的实现类的, 那么就执行接口对应的方法.
19. 调用初始化 invokeInitMethods(beanName, wrappedBean, mbd);
20. 首先判断是否实现了 InitializingBean 接口, 实现了调用 ((InitializingBean) bean).afterPropertiesSet();
21. 接下来如果 BeanDefinition 中有mbd.getInitMethodName(); 且不是afterPropertiesSet方法, 执行InitMethodName对应的方法.
22. 继续执行初始化后方法 wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName); 执行所有的BeanPostProcessor.postProcessAfterInitialization(result, beanName); 
23. 初始化后的 BeanPostProcessor.postProcessAfterInitialization(result, beanName); 中会做AOP. 注意只有 @Configuration和添加@AspectJAutoProxyRegistrar且类上有@Aspect或类或方法上 有@Transactional 注解的才会被代理.
24. 并且AOP对应的bean也是通过 ImportBeanDefinitionRegistrar 注入的. 
24. initializeBean执行完毕后, 执行registerDisposableBeanIfNecessary(beanName, bean, mbd); 这是bean销毁相关的


# Bean销毁
实现DisposableBean或AutoCloseale接口或在方法上添加 @Perdestory 注解,在Bean销毁时调用. spring容器销毁的时候执行. 通过context.registerShutdownHook()注册关闭钩子也可以调用.

registerDisposableBeanIfNecessary(beanName, bean, mbd); 中会记录Bean有没有实现DisposableBean或AutoCloseale接口, 然后判断 BeanDefinition 有没有指定销毁的方法, BeanDefinition.setDestoryMehodName("xxx"),  如果你把setDestoryMehodName设置为"{inferred}", 那么 Spring销毁时会调用类的 close方法, 如果没有继续调用shutdown方法.
如果前面都找不到会继续判断是否有实现了DestructionAwareBeanPostProcessor的Bean. 并执行它的requiresDestruction方法.判断是否需要执行销毁逻辑.
DestructionAwareBeanPostProcessor接口中有两个方法 boolean requiresDestruction(Object bean)设置bean是否需要被销毁, void postProcessBeforeDestruction(Object bean, String beanName) 执行具体的销毁逻辑. 
InitDestroyAnnotationBeanPostProcessor是实现了DestructionAwareBeanPostProcessor的, 在初始化前时他将@PerDestory注解记录了, 在此时就会通过这个去判断是否有销毁的方法.

如果有销毁方法, 且是单例bean通过beanName和(此时Bean是DisposableBean类型)Bean存入到disposableBeans map中. 原型bean因为Spring根本没有记录, 所以不会执行销毁方法