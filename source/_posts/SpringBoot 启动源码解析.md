---
title: SpringBoot 启动源码解析
date: 2021-04-15 12:27:17
tags:
 - SpringBoot
 - 源码
categories:
 - SpringBoot
---

# 1.启动
```
public static void main(String[] args) {
    // 静态方法启动
    SpringApplication.run(xxx.class, args);
}
```

# 2. 初始化 `SpringApplication` 对象
```
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
	this.resourceLoader = resourceLoader;
	Assert.notNull(primarySources, "PrimarySources must not be null");
	this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
	/**
	  *设置webApplication类型, 三种类型:
	   NONE: 不是web环境
	   SERVLET:  【spring-webmvc + Servlet + Tomcat】命令式的、同步阻塞的
	   REACTIVE: 【spring-webflux + Reactor + Netty】响应式的、异步非阻塞的 https://www.cnblogs.com/cjsblog/p/12580518.html
	*/
	this.webApplicationType = WebApplicationType.deduceFromClasspath();
	/**
	  * https://www.cnblogs.com/duanxz/p/11239291.html
	  * 通过扫描 META-INF/spring.factories 文件初始化 ApplicationContextInitializer 的实现类集合, 并实例化, 通过order注解排序
	  * ApplicationContextInitializer是Spring框架原有的东西，这个接口的主要作用就是在ConfigurableApplicationContext类型(或者子类型)的ApplicationContext做refresh之前，允许我们对ConfiurableApplicationContext的实例做进一步的设置和处理。
	  */
	setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
	/**
	  * https://www.cnblogs.com/lwcode6/p/12072202.html
	  * 通过扫描 META-INF/spring.factories 文件初始化 ApplicationListener 的实现类集合, 并实例化, 通过order注解排序
	  * ApplicationContext事件机制是观察者设计模式的实现，通过ApplicationEvent类和ApplicationListener接口，可以实现ApplicationContext事件处理；
如果容器中存在ApplicationListener的Bean，当ApplicationContext调用publishEvent方法时，对应的Bean会被触发。
      */
	setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
	// 创建Main方法所在的对象实例
	this.mainApplicationClass = deduceMainApplicationClass();
}
```

## 2.1 执行`run`方法
```
public ConfigurableApplicationContext run(String... args) {
	StopWatch stopWatch = new StopWatch();
	stopWatch.start();
	ConfigurableApplicationContext context = null;
	Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
	/**
	  * 设置配置模式 https://blog.csdn.net/wodeyuer125/article/details/50502914
	  * 这里直接设置的java.awt.headless 模式
	  * 当我们写的java程序本身不许要显示awt界面，例如命令行程序，后端程序。为了提高计算效率和适配性我们可以使用这种模式，关闭图形显示等功能可以大大节省设备的计算能力，而且对一些本身没有相关显示设备的机器也能适配，程序也可以正常运行。
	  */
	configureHeadlessProperty();
	/**
	  * https://www.cnblogs.com/duanxz/p/11239291.html
	  * 通过扫描 META-INF/spring.factories 文件初始化 SpringApplicationRunListener 的实现类集合, 并实例化, 通过order注解排序
	  * SpringApplicationRunListener 接口的作用主要就是在Spring Boot 启动初始化的过程中可以通过SpringApplicationRunListener接口回调来让用户在启动的各个流程中可以加入自己的逻辑。
	  */
	SpringApplicationRunListeners listeners = getRunListeners(args);
	// 执行所有的SpringApplicationRunListener实例的starting生命周期方法
	listeners.starting();
	try {
	    // 启动参数
		ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
		/**
		  * 1. getOrCreateEnvironment 根据webApplication类型初始化不同配置
		  *  1.1 SERVLET
		  *   1.1.1 初始化:servletConfigInitParams, servletContextInitParams
		  *   1.1.2 通过spring.jndi.ignore配置判断是否初始化 jndiProperties
		  *   1.1.3 通过super.customizePropertySources(propertySources); 初始化 systemProperties(JVM环境变量), systemEnvironment(系统环境变量)
		  *  1.2 REACTIVE和NONE一致
		  *   1.2.1 通过super.customizePropertySources(propertySources); 初始化 systemProperties(JVM环境变量), systemEnvironment(系统环境变量)
		  * 2. configureEnvironment 初始化参数
		  *  2.1 configurePropertySources 初始化命令行传入和方法的参数
		  *  2.2 configureProfiles 以spring.profiles.active为key从getOrCreateEnvironment初始化的环境变量对象来初始化ActiveProfiles
		  * 3. 通过SpringApplicationRunListener实例的后置处理程序environmentPrepared加载配置文件
		  *  3.1 SpringCloud BootstrapApplicationListener 初始化configName, ${spring.cloud.bootstrap.name:bootstrap}, 并读入配置文件
		  */
		ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
		configureIgnoreBeanInfo(environment);
		Banner printedBanner = printBanner(environment);
		context = createApplicationContext();
		exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
				new Class[] { ConfigurableApplicationContext.class }, context);
		prepareContext(context, environment, listeners, applicationArguments, printedBanner);
		refreshContext(context);
		afterRefresh(context, applicationArguments);
		stopWatch.stop();
		if (this.logStartupInfo) {
			new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
		}
		listeners.started(context);
		callRunners(context, applicationArguments);
	}
	catch (Throwable ex) {
		handleRunFailure(context, ex, exceptionReporters, listeners);
		throw new IllegalStateException(ex);
	}

	try {
		listeners.running(context);
	}
	catch (Throwable ex) {
		handleRunFailure(context, ex, exceptionReporters, null);
		throw new IllegalStateException(ex);
	}
	return context;
}
```