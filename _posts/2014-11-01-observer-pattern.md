---
layout: post
title: 观察者模式
category: 设计模式
date: 2014-11-01
---
观察者模式（Observer Pattern）在软件设计中应用得非常频繁。定义如下：  

> 它定义对象之间一种一对多依赖关系，使得每当一个对象状态（被观察者，一）发生改变时，其相关依赖对象（观察者，多）皆得到通知并被自动更新。该模式也被称作发布-订阅（Publish-Subscribe）模式、模型-视图（Model-View）模式、源-监听器（Source-Listener）模式。观察者模式是一种对象行为型模式。  

其UML类图结构如图所示：  
![](http://iofisher.qiniudn.com/@/blog/观察者模式.png)  
包含观察者（Observer）和被观察者（Subject）两类角色。观察者一般而言提供一个update()方法，被观察者维护一个观察者列表，通过register()方法和unregister()方法添加或删除观察者对象，而notify()方法则在于触发对观察者update()方法的调用。  
Subject类代码：  
{% highlight java %}
public abstract class Subject {
    //维护一个观察者列表
    protected ArrayList<Observer> observers = new ArrayList<Observer>();

    //绑定观察者
    public synchronized void register( Observer observer ) {
        if (observer==null) {
            throw new NullPointerException();
        }
        if (!observers.contains(observer)) {
            observers.add(observer);
        }
    }

    //解绑观察者
    public synchronized void unregister( Observer observer ) {
        observers.remove(observer);
    }

    public abstract void toNotify();
}
{% endhighlight %}  

Observer类代码：  
{% highlight java %}
public interface Observer {
    public void update();
}
{% endhighlight %}  

具体被观察者类代码：  
{% highlight java %}
public class ConcreteSubject extends Subject {

    @Override
    public void toNotify() {
        for (Observer observer:observers) {
            observer.update();
        }
    }
}
{% endhighlight %}  

具体被观察者类代码：  
{% highlight java %}
public class ConcreteObserver implements Observer {

    private String name;

    public ConcreteObserver(String name) {
        this.name = name;
    }

    @Override
    public void update() {
        System.out.println("concrete observer: " + this.name);
    }
}
{% endhighlight %}

测试类Main方法：  
{% highlight java %}
public static void main(String[] args) {
        ConcreteSubject subject = new ConcreteSubject();
        subject.register( new ConcreteObserver("name1") );
        subject.register( new ConcreteObserver("name2") );
        subject.toNotify();
    }
{% endhighlight %}  
  
  

----------  
  

----------

----------

在java.util中，存在观察者模式设计类，即Observer和Observable,这里面的Observable直接设计成普通类了，编码上比较简单明了，不过在复杂的情况下缺少灵活性。  
java.util.Observer：  
{% highlight java %}
package java.util;

public interface Observer {
	//当被观察者对象改变时，调用该方法。
	//在被观察者对象Obserable中调用notifyObservers方法来通知所有观察者处理更新。
    void update(Observable o, Object arg);
}
{% endhighlight %}  
  
java.util.Observable:  
{% highlight java %}
package java.util;

public class Observable {
	//设置一个状态值
    private boolean changed = false;
	//维护一个列表
    private Vector<Observer> obs;

    /** Construct an Observable with zero Observers. */
    public Observable() {
        obs = new Vector<>();
    }

	//添加观察者（加锁）
    public synchronized void addObserver(Observer o) {
        if (o == null)
            throw new NullPointerException();
        if (!obs.contains(o)) {
            obs.addElement(o);
        }
    }

	//删除某个观察者（加锁）
    public synchronized void deleteObserver(Observer o) {
        obs.removeElement(o);
    }

	//通知所有观察者（不依赖于注册顺序，可在子类中重新设计通知顺序）
	//如果hasChanged为true，则通知所有观察者，之后调用clearChanged方法，表示这个对象不再改变
    public void notifyObservers() {
        notifyObservers(null);
    }

    //通知所有观察者（不依赖于注册顺序，可在子类中重新设计通知顺序）
	//如果hasChanged为true，则通知所有观察者，之后调用clearChanged方法，表示这个对象不再改变
    public void notifyObservers(Object arg) {
        /*
         * a temporary array buffer, used as a snapshot of the state of
         * current Observers.
         */
        Object[] arrLocal;

        synchronized (this) {
            /* We don't want the Observer doing callbacks into
             * arbitrary code while holding its own Monitor.
             * The code where we extract each Observable from
             * the Vector and store the state of the Observer
             * needs synchronization, but notifying observers
             * does not (should not).  The worst result of any
             * potential race-condition here is that:
             * 1) a newly-added Observer will miss a
             *   notification in progress
             * 2) a recently unregistered Observer will be
             *   wrongly notified when it doesn't care
             */
            if (!changed)
                return;
            arrLocal = obs.toArray();
            clearChanged();
        }

        for (int i = arrLocal.length-1; i>=0; i--)
            ((Observer)arrLocal[i]).update(this, arg);
    }

    //删除所有观察者
    public synchronized void deleteObservers() {
        obs.removeAllElements();
    }

    //被观察者改变状态（加锁）
    protected synchronized void setChanged() {
        changed = true;
    }

    //被观察者清除状态（加锁）
    protected synchronized void clearChanged() {
        changed = false;
    }

    //获得状态（加锁）
    public synchronized boolean hasChanged() {
        return changed;
    }

    //获得观察者数量（加锁）
    public synchronized int countObservers() {
        return obs.size();
    }
}
{% endhighlight %}  
想要进行通知，须调用Observable类的setChanged方法（注意方法为protected）。

参考资料：  
【1】[jdk自带的观察者模式](http://blog.csdn.net/a19881029/article/details/8975962)
