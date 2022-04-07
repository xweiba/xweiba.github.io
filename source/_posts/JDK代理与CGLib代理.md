---
title: JDK代理与CGLib代理
date: 2021-04-02 09:16:25
tags: 
 - CGLib
 - JDK代理

---

## JDK代理

### 特点：

- 目标类必须为实现了某个接口的实例，因为他生成的 `class` 文件就是一个实现了实例接口并继承了`java.lang.reflect.Proxy`类的匿名类。
- 通过该匿名类调用`InvocationHandler`实例的 `invoke` 接口来增强，`InvocationHandler` 实例必须传入目标类实例, 他就像是对目标实例做了一层封装，并通过 `invoke` 接口钩子来实现增强
- 通过`Method.invoke(Object obj, Object... args)`来完成实例的最终执行, `obj` 为目标类实例。

### 缺点：

- 只能代理实现了接口的实例，否则无法生成对应的匿名类，并且实例中非接口的方法无法代理。
- 由于是通过反射直接执行实例的方法，在实例中调用其本类方法时，不会再次走代理，而是直接执行。

### 生成的匿名类`$Proxy0.class`：

`Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), this)` 返回的就是它的实例，他会实现目标类的全部接口。

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.sun.proxy;

import io.github.xweiba.proxy.IUserManager;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class $Proxy0 extends Proxy implements IUserManager {
    private static Method m1;
    private static Method m2;
    private static Method m4;
    private static Method m3;
    private static Method m0;
    
    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            // 接口的实现方法
            m4 = Class.forName("io.github.xweiba.proxy.IUserManager").getMethod("updata", Class.forName("java.lang.String"));
            m3 = Class.forName("io.github.xweiba.proxy.IUserManager").getMethod("add", Class.forName("java.lang.String"));
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }

    public $Proxy0(InvocationHandler var1) throws  {
        // Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces, InvocationHandler h)
        // Proxy.newProxyInstance()方法会将本类实例化并传入InvocationHandler h
        // return cons.newInstance(new Object[]{h});
        // var1 即是InvocationHandler的实例对象，
        super(var1);
    }

    public final void updata(String var1) throws  {
        try {
            // super.h === InvocationHandler的实例对象, 通过它的invoke接口实现增强功能。
            // invoke 中一般使用反射来完成目标对象的调用。 m4.invoke(targetObject, new Object[]{var1});
            super.h.invoke(this, m4, new Object[]{var1});
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }
    //....
}
```

## `CGLib` 代理

## 特点：

- `CGLib` 原理是针对目标类生成一个匿名子类，覆盖其中的所有方法。
- 所有方法的执行都会通过 `MethodInterceptor` 的 `intercept` 接口来实现增强，新的匿名子类 `class` 就像是对目标类做了一层封装，并通过 `intercept` 接口钩子来实现增强，在需要执行目标类的方法时通过调用父类的方法来完成。
- 通过`Method.invokeSuper(Object obj, Object[] args)`来完成实例的最终执行, `obj` 为匿名子类实例，注意不是`invoke`,
- 执行的是匿名子类的实例父类方法，在父类方法中调用本类方法时，又会回到匿名子类中，所有会再次走代理完成增强。
- 因为执行的就是新生成的匿名子类，效率理论上比`JDK代理`高。

### 缺点：

- 目标类和方法不能声明为final类型，否则无法生成匿名子类。

### 生成的匿名类 `UserManager$$EnhancerByCGLIB$$ddf60d63`：

`CGLib`无需传入目标类实例， 因为`enhancer.create()` 返回的就是继承自目标类的匿名子类实例对象，他会覆盖父类的所有方法;

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package io.github.xweiba.proxy.impl;

import java.lang.reflect.Method;
import net.sf.cglib.core.ReflectUtils;
import net.sf.cglib.core.Signature;
import net.sf.cglib.proxy.Callback;
import net.sf.cglib.proxy.Factory;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class UserManager$$EnhancerByCGLIB$$ddf60d63 extends UserManager implements Factory {
    private boolean CGLIB$BOUND;
    public static Object CGLIB$FACTORY_DATA;
    private static final ThreadLocal CGLIB$THREAD_CALLBACKS;
    private static final Callback[] CGLIB$STATIC_CALLBACKS;
    private MethodInterceptor CGLIB$CALLBACK_0;
    private static Object CGLIB$CALLBACK_FILTER;
    private static final Method CGLIB$add$0$Method;
    private static final MethodProxy CGLIB$add$0$Proxy;
    private static final Object[] CGLIB$emptyArgs;
    private static final Method CGLIB$noITest$1$Method;
    private static final MethodProxy CGLIB$noITest$1$Proxy;
    private static final Method CGLIB$updata$2$Method;
    private static final MethodProxy CGLIB$updata$2$Proxy;
    private static final Method CGLIB$equals$3$Method;
    private static final MethodProxy CGLIB$equals$3$Proxy;
    private static final Method CGLIB$toString$4$Method;
    private static final MethodProxy CGLIB$toString$4$Proxy;
    private static final Method CGLIB$hashCode$5$Method;
    private static final MethodProxy CGLIB$hashCode$5$Proxy;
    private static final Method CGLIB$clone$6$Method;
    private static final MethodProxy CGLIB$clone$6$Proxy;

    static void CGLIB$STATICHOOK1() {
        CGLIB$THREAD_CALLBACKS = new ThreadLocal();
        CGLIB$emptyArgs = new Object[0];
        // var0 是当前类
        Class var0 = Class.forName("io.github.xweiba.proxy.impl.UserManager$$EnhancerByCGLIB$$ddf60d63");
        Class var1;
        Method[] var10000 = ReflectUtils.findMethods(new String[]{"equals", "(Ljava/lang/Object;)Z", "toString", "()Ljava/lang/String;", "hashCode", "()I", "clone", "()Ljava/lang/Object;"}, (var1 = Class.forName("java.lang.Object")).getDeclaredMethods());
        CGLIB$equals$3$Method = var10000[0];
        CGLIB$equals$3$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/Object;)Z", "equals", "CGLIB$equals$3");
        CGLIB$toString$4$Method = var10000[1];
        CGLIB$toString$4$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/String;", "toString", "CGLIB$toString$4");
        CGLIB$hashCode$5$Method = var10000[2];
        CGLIB$hashCode$5$Proxy = MethodProxy.create(var1, var0, "()I", "hashCode", "CGLIB$hashCode$5");
        CGLIB$clone$6$Method = var10000[3];
        CGLIB$clone$6$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/Object;", "clone", "CGLIB$clone$6");
        // 注意这里是被代理的class var1 = Class.forName("io.github.xweiba.proxy.impl.UserManager")).getDeclaredMethods()
        var10000 = ReflectUtils.findMethods(new String[]{"add", "(Ljava/lang/String;)V", "noITest", "(Ljava/lang/String;)V", "updata", "(Ljava/lang/String;)V"}, (var1 = Class.forName("io.github.xweiba.proxy.impl.UserManager")).getDeclaredMethods());
        CGLIB$add$0$Method = var10000[0];
        // 传入被代理的class的方法信息和当前class，当执行MethodInterceptor的intercept接口时会传入CGLIB$add$0$Proxy，调用CGLIB$add$0$Proxy的invokeSuper
        CGLIB$add$0$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/String;)V", "add", "CGLIB$add$0");
        CGLIB$noITest$1$Method = var10000[1];
        CGLIB$noITest$1$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/String;)V", "noITest", "CGLIB$noITest$1");
        CGLIB$updata$2$Method = var10000[2];
        CGLIB$updata$2$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/String;)V", "updata", "CGLIB$updata$2");
    }

    final void CGLIB$add$0(String var1) {
        super.add(var1);
    }

    public final void add(String var1) {
        // this.CGLIB$CALLBACK_0 就是 MethodInterceptor 的实例对象，通过enhancer.setCallback(this)传入。
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) { // 通过子类实例(就是本类)调用时走MethodInterceptor的intercept接口来实现增强。
            var10000.intercept(this, CGLIB$add$0$Method, new Object[]{var1}, CGLIB$add$0$Proxy);
        } else { // 在MethodInterceptor的intercept中通过Method.invokeSuper(Object obj, Object[] args)调用时走父类的add方法。
            super.add(var1);
        }
    }

    final void CGLIB$noITest$1(String var1) {
        super.noITest(var1);
    }

    public final void noITest(String var1) {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            var10000.intercept(this, CGLIB$noITest$1$Method, new Object[]{var1}, CGLIB$noITest$1$Proxy);
        } else {
            super.noITest(var1);
        }
    }

    final void CGLIB$updata$2(String var1) {
        super.updata(var1);
    }

    public final void updata(String var1) {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            var10000.intercept(this, CGLIB$updata$2$Method, new Object[]{var1}, CGLIB$updata$2$Proxy);
        } else {
            super.updata(var1);
        }
    }

    final boolean CGLIB$equals$3(Object var1) {
        return super.equals(var1);
    }

    public final boolean equals(Object var1) {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var2 = var10000.intercept(this, CGLIB$equals$3$Method, new Object[]{var1}, CGLIB$equals$3$Proxy);
            return var2 == null ? false : (Boolean)var2;
        } else {
            return super.equals(var1);
        }
    }

    final String CGLIB$toString$4() {
        return super.toString();
    }

    public final String toString() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        return var10000 != null ? (String)var10000.intercept(this, CGLIB$toString$4$Method, CGLIB$emptyArgs, CGLIB$toString$4$Proxy) : super.toString();
    }

    final int CGLIB$hashCode$5() {
        return super.hashCode();
    }

    public final int hashCode() {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        if (var10000 != null) {
            Object var1 = var10000.intercept(this, CGLIB$hashCode$5$Method, CGLIB$emptyArgs, CGLIB$hashCode$5$Proxy);
            return var1 == null ? 0 : ((Number)var1).intValue();
        } else {
            return super.hashCode();
        }
    }

    final Object CGLIB$clone$6() throws CloneNotSupportedException {
        return super.clone();
    }

    protected final Object clone() throws CloneNotSupportedException {
        MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
        if (var10000 == null) {
            CGLIB$BIND_CALLBACKS(this);
            var10000 = this.CGLIB$CALLBACK_0;
        }

        return var10000 != null ? var10000.intercept(this, CGLIB$clone$6$Method, CGLIB$emptyArgs, CGLIB$clone$6$Proxy) : super.clone();
    }

    public static MethodProxy CGLIB$findMethodProxy(Signature var0) {
        String var10000 = var0.toString();
        switch(var10000.hashCode()) {
        case -1692737819:
            if (var10000.equals("noITest(Ljava/lang/String;)V")) {
                return CGLIB$noITest$1$Proxy;
            }
            break;
        case -1358456834:
            if (var10000.equals("add(Ljava/lang/String;)V")) {
                return CGLIB$add$0$Proxy;
            }
            break;
        case -508378822:
            if (var10000.equals("clone()Ljava/lang/Object;")) {
                return CGLIB$clone$6$Proxy;
            }
            break;
        case 1653544538:
            if (var10000.equals("updata(Ljava/lang/String;)V")) {
                return CGLIB$updata$2$Proxy;
            }
            break;
        case 1826985398:
            if (var10000.equals("equals(Ljava/lang/Object;)Z")) {
                return CGLIB$equals$3$Proxy;
            }
            break;
        case 1913648695:
            if (var10000.equals("toString()Ljava/lang/String;")) {
                return CGLIB$toString$4$Proxy;
            }
            break;
        case 1984935277:
            if (var10000.equals("hashCode()I")) {
                return CGLIB$hashCode$5$Proxy;
            }
        }

        return null;
    }

    public UserManager$$EnhancerByCGLIB$$ddf60d63() {
        CGLIB$BIND_CALLBACKS(this);
    }

    public static void CGLIB$SET_THREAD_CALLBACKS(Callback[] var0) {
        CGLIB$THREAD_CALLBACKS.set(var0);
    }

    public static void CGLIB$SET_STATIC_CALLBACKS(Callback[] var0) {
        CGLIB$STATIC_CALLBACKS = var0;
    }

    private static final void CGLIB$BIND_CALLBACKS(Object var0) {
        UserManager$$EnhancerByCGLIB$$ddf60d63 var1 = (UserManager$$EnhancerByCGLIB$$ddf60d63)var0;
        if (!var1.CGLIB$BOUND) {
            var1.CGLIB$BOUND = true;
            Object var10000 = CGLIB$THREAD_CALLBACKS.get();
            if (var10000 == null) {
                var10000 = CGLIB$STATIC_CALLBACKS;
                if (var10000 == null) {
                    return;
                }
            }

            var1.CGLIB$CALLBACK_0 = (MethodInterceptor)((Callback[])var10000)[0];
        }

    }

    public Object newInstance(Callback[] var1) {
        CGLIB$SET_THREAD_CALLBACKS(var1);
        UserManager$$EnhancerByCGLIB$$ddf60d63 var10000 = new UserManager$$EnhancerByCGLIB$$ddf60d63();
        CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
        return var10000;
    }

    public Object newInstance(Callback var1) {
        CGLIB$SET_THREAD_CALLBACKS(new Callback[]{var1});
        UserManager$$EnhancerByCGLIB$$ddf60d63 var10000 = new UserManager$$EnhancerByCGLIB$$ddf60d63();
        CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
        return var10000;
    }

    public Object newInstance(Class[] var1, Object[] var2, Callback[] var3) {
        CGLIB$SET_THREAD_CALLBACKS(var3);
        UserManager$$EnhancerByCGLIB$$ddf60d63 var10000 = new UserManager$$EnhancerByCGLIB$$ddf60d63;
        switch(var1.length) {
        case 0:
            var10000.<init>();
            CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
            return var10000;
        default:
            throw new IllegalArgumentException("Constructor not found");
        }
    }

    public Callback getCallback(int var1) {
        CGLIB$BIND_CALLBACKS(this);
        MethodInterceptor var10000;
        switch(var1) {
        case 0:
            var10000 = this.CGLIB$CALLBACK_0;
            break;
        default:
            var10000 = null;
        }

        return var10000;
    }

    public void setCallback(int var1, Callback var2) {
        switch(var1) {
        case 0:
            this.CGLIB$CALLBACK_0 = (MethodInterceptor)var2;
        default:
        }
    }

    public Callback[] getCallbacks() {
        CGLIB$BIND_CALLBACKS(this);
        return new Callback[]{this.CGLIB$CALLBACK_0};
    }

    public void setCallbacks(Callback[] var1) {
        this.CGLIB$CALLBACK_0 = (MethodInterceptor)var1[0];
    }

    static {
        CGLIB$STATICHOOK1();
    }
}
```

## 反射相关方法

```java
public static MethodProxy create(Class c1, Class c2, String desc, String name1, String name2) {
    MethodProxy proxy = new MethodProxy();
    proxy.sig1 = new Signature(name1, desc);
    proxy.sig2 = new Signature(name2, desc);
    // cglib 中，c1为被代理类的class，c2为cglib生成的class(c1的子类)
    proxy.createInfo = new MethodProxy.CreateInfo(c1, c2);
    return proxy;
}
private static class CreateInfo {
    // cglib 中，c1为被代理类的class，c2为cglib生成的class
    Class c1;
    Class c2;
    NamingPolicy namingPolicy;
    GeneratorStrategy strategy;
    boolean attemptLoad;

    public CreateInfo(Class c1, Class c2) {
        this.c1 = c1;
        this.c2 = c2;
        AbstractClassGenerator fromEnhancer = AbstractClassGenerator.getCurrent();
        if (fromEnhancer != null) {
            this.namingPolicy = fromEnhancer.getNamingPolicy();
            this.strategy = fromEnhancer.getStrategy();
            this.attemptLoad = fromEnhancer.getAttemptLoad();
        }

    }
}
private void init() {
    if (this.fastClassInfo == null) {
        synchronized(this.initLock) {
            if (this.fastClassInfo == null) {
                MethodProxy.CreateInfo ci = this.createInfo;
                MethodProxy.FastClassInfo fci = new MethodProxy.FastClassInfo();
                // f1 由 c1 生成
                fci.f1 = helper(ci, ci.c1);
                // f2 由 c2 生成
                fci.f2 = helper(ci, ci.c2);
                fci.i1 = fci.f1.getIndex(this.sig1);
                fci.i2 = fci.f2.getIndex(this.sig2);
                this.fastClassInfo = fci;
                this.createInfo = null;
            }
        }
    }

}
public Object invoke(Object obj, Object[] args) throws Throwable {
        try {
            this.init();
            MethodProxy.FastClassInfo fci = this.fastClassInfo;
            // 在cglib中调用被代理类c1的方法对象的invoke方法
            return fci.f1.invoke(fci.i1, obj, args);
        } catch (InvocationTargetException var4) {
            throw var4.getTargetException();
        } catch (IllegalArgumentException var5) {
            if (this.fastClassInfo.i1 < 0) {
                throw new IllegalArgumentException("Protected method: " + this.sig1);
            } else {
                throw var5;
            }
        }
    }

public Object invokeSuper(Object obj, Object[] args) throws Throwable {
    try {
        this.init();
        MethodProxy.FastClassInfo fci = this.fastClassInfo;
        // 在cglib中调用被代理类c2(c1的子类)的方法对象的invoke方法，会通过方法的hashcode来定位最终执行的方法。
        return fci.f2.invoke(fci.i2, obj, args);
    } catch (InvocationTargetException var4) {
        throw var4.getTargetException();
    }
}
```



## 测试Demo:

`IUserManager`:

```java
package io.github.xweiba.proxy;

/**
 * 测试接口
 *
 * @author caleb_L
 * @date 2021/04/02
 **/
public interface IUserManager {

    void add(String user);

    void updata(String user);

}
```

`UserManager`:

```java
package io.github.xweiba.proxy.impl;

import io.github.xweiba.proxy.IUserManager;

/**
 * 实现类
 *
 * @author caleb_L
 * @date 2021/04/02
 **/

public class UserManager implements IUserManager {

    public void add(String user) {
        System.out.println("add: " + user);
        updata(user);
    }

    public void updata(String user) {
        System.out.println("updata: " + user);
    }

    public void noITest(String user){
        System.out.println("noITest: " + user);
    }
}
```

 `AbstractProxy.java`:

```java
package io.github.xweiba.proxy;

/**
 * 代理测试抽象类
 *
 * @author caleb_L
 * @date 2021/04/02
 **/

public abstract class AbstractProxy<T> implements IProxy<T> {

    /**
     * 代理执行方法
     *
     * @date 2022/04/02 11:30
     * @author caleb_L
     * @param objects : 代理执行参数
     * @return java.lang.Object
     */
    public abstract Object proxyExecute(Object ...objects) throws Throwable;

    public abstract String getProxyType();

    /**
     * 代理增强钩子
     *
     * @date 2022/04/02 11:30
     * @author caleb_L
     * @param objects : 代理执行参数
     * @return java.lang.Object
     */
    public Object proxyEnhance(Object... objects) throws Throwable {
        System.out.println(getProxyType() + "代理开始");
        Object execute = this.proxyExecute(objects);
        System.out.println(getProxyType() + "代理结束");
        return execute;
    }

}
```

 `JdkProxyHandler.java`:

```java
package io.github.xweiba.proxy;

import java.lang.reflect.Field;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * JDK 动态代理Demo
 *
 * @author caleb_L
 * @date 2021/04/02
 **/

public class JdkProxyHandler extends AbstractProxy<Object> implements InvocationHandler {

    public Object target;

    public Object invoke(Object proxyClassObject, Method method, Object[] args) throws Throwable {
        // proxyClassObject对象是JDK生成的代理class实例
        System.out.println("jdkProxy invoke: proxyClassObject.super.h == this -> " + (getFieldValue(proxyClassObject, "h") == this));
        return this.proxyEnhance(proxyClassObject, method, args);
    }


    public Object getProxy(Object object) {
        this.target = object;
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), this);
    }

    public Object proxyExecute(Object... objects) throws Throwable {
        // objects = Object proxy, Method method, Object[] args
        // proxy对象是JDK代理生成的class的实例，这里invoke必须使用原对象。
        return ((Method)objects[1]).invoke(this.target, (Object[])objects[2]);
    }

    public String getProxyType() {
        return "JDK";
    }

    public Object getFieldValue(Class clazz, Object person, String name) {

        Object o = null;
        // 获取类中声明的字段
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            if (field.getName().equals(name)) {
                // 避免 can not access a member of class com.java.test.Person with modifiers "private"
                field.setAccessible(true);
                try {
                    o = field.get(person);
                    System.out.println(field.getName() + ":"+ field.get(person));
                    break;
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        }
        if (o == null) {
            o = getFieldValue(person.getClass().getSuperclass(), person, name);
        }

        return o;
    }

    public Object getFieldValue(Object person, String name) {
        return getFieldValue(person.getClass(), person, name);
    }
}
```

`CGLibProxyMethodInterceptor`:

```java
package io.github.xweiba.proxy;

import io.github.xweiba.proxy.impl.UserManager;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * CGLib 动态代理Demo
 *
 * @author caleb_L
 * @date 2022/04/02
 **/

public class CGLibProxyMethodInterceptor extends AbstractProxy<Class> implements MethodInterceptor  {

    private Object target;

    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        return this.proxyEnhance(proxy, method, args, methodProxy);
    }

    public Object getProxy(Class superclass) {
        target = new UserManager();
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(superclass);
        enhancer.setCallback(this);
        return enhancer.create();
    }

    public Object proxyExecute(Object... objects) throws Throwable{
        // objects = Object proxy, Method method, Object[] args, MethodProxy methodProxy
        // 使用 invoke 的话，在方法内调用本类方法不会走父类方法，传入代理对象会死循环，使用target的话就跟JDK代理一样了。
        // return ((MethodProxy)objects[3]).invoke(target, (Object[])objects[2]);
        // 使用 invokeSuper 来调用父类方法，父类即为目标类。
        return ((MethodProxy)objects[3]).invokeSuper(objects[0], (Object[])objects[2]);
    }

    public String getProxyType() {
        return "CGLib";
    }
}

```

`ProxyTestDemo`:

```java
package io.github.xweiba.proxy;

import io.github.xweiba.proxy.impl.UserManager;
import net.sf.cglib.core.DebuggingClassWriter;

/**
 * 代理测试Demo
 *
 * @author caleb_L
 * @date 2022/04/02
 **/

public class ProxyTestDemo {

    public static void main(String[] args) {

        // 保存JDK代理生成的class文件
        System.setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
        // 开启CGLib debug模式, 保存CGLib代理生成的class文件
        System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, System.getProperty("user.dir") + "\\cglib");

        // cgLib 生成的是代理类的子类，并通过MethodInterceptor的intercept接口增强
        // cglib 不需要new实例，只需要class，因为他生成的是class的子类，返回子类的实例。
        UserManager cgLibProxy = (UserManager)new CGLibProxyMethodInterceptor().getProxy(UserManager.class);
        // 真正调用的是父类的add方法，父类add方法调用时，调用
        cgLibProxy.add("test");
        System.out.println("\n\n");
        cgLibProxy.noITest("test");
        System.out.println("\n\n");

        // jdk 生成的是接口的实现类，所以只能代理接口，并通过InvocationHandler的invoke接口增强
        // Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
        // (super.h.invoke(this, m3, new Object[]{var1});)，  super.h === jdkProxy
        // jdk代理执行时必须传入代理对象，因为他最终执行的还是真正的实例, 代理类只是用来增强的。
        IUserManager jdkProxy = (IUserManager)new JdkProxyHandler().getProxy(new UserManager());
        jdkProxy.add("test");
        System.out.println("\n\n");
        // 会抛出异常，因为jdk代理只会代理接口，他是接口的实现。
        ((UserManager)jdkProxy).noITest("test");
    }

}
```

输入日志：

```
CGLIB debugging enabled, writing to 'D:\resouces\code\java\note\cglib'
CGLib代理开始
add: test
CGLib代理开始
updata: test
CGLib代理结束
CGLib代理结束



CGLib代理开始
noITest: test
CGLib代理结束



h:io.github.xweiba.proxy.JdkProxyHandler@72b6cbcc
jdkProxy invoke: proxyClassObject.super.h == this -> true
JDK代理开始
add: test
updata: test
JDK代理结束



Exception in thread "main" java.lang.ClassCastException: com.sun.proxy.$Proxy0 cannot be cast to io.github.xweiba.proxy.impl.UserManager
	at io.github.xweiba.proxy.ProxyTestDemo.main(ProxyTestDemo.java:41)
```
