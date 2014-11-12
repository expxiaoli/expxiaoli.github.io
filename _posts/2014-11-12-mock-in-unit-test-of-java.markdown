---
layout: post
title:  "Mock All Instances of a class in Java"
date:   2014-11-12 06:02:10
categories: java
---

mockito能满足java中unit test对mock的大部分需求。但要mock某个类的所有对象的行为，mockito就不能要求了，这时PowerMockito就派上用场了。方法是调用PowerMockito的`whenNew`方法

这种做法主要有两个应用场合：
1）需要mock许多对象，mock后对象的行为具有相同的规律。
2）需要mock的对象以private变量的方式存在于被测类中。普通的mock方式无法touch到这些变量，

实例代码见下：
{% highlight java %}
@RunWith(PowerMockRunner.class)
@PrepareForTest({ Inner.class, Outer.class})
public class AppTest 
{
    @Test
    public void testMockAllInstances() throws Exception {        
        Inner inner = Mockito.mock(Inner.class);
        Mockito.when(inner.getNumber()).thenReturn(new Integer(15));
        PowerMockito.whenNew(Inner.class).withAnyArguments().thenReturn(inner);
    
        Outer outer = new Outer(10);
        Assert.assertEquals(outer.getNumber(), "15"); //mocking private variable takes effect
    }
    
    @Test
    public void testMockSingleInstance() {
        Inner inner = Mockito.mock(Inner.class);
        Mockito.when(inner.getNumber()).thenReturn(new Integer(15));     
        
        Assert.assertEquals(inner.getNumber(), new Integer(15));
        Outer outer = new Outer(10);
        Assert.assertEquals(outer.getNumber(), "10");//private variable is not mocked
    }
}
{% endhighlight %}

stackoverflow上有个类似的[问答][mock-all-instances]

当然，mock private变量也有另外的方式，比如用[@Mock (name = "privateVariableNameHere")][mock-private-variable]

[mock-all-instances]:    http://stackoverflow.com/questions/20797396/mock-all-instances-of-a-class
[mock-private-variable]: http://stackoverflow.com/questions/19521220/mocking-a-private-variable-that-is-assumed-to-exist
