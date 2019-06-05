## 1.概述

在本教程中，我们将了解适配器模式及其Java实现。

## 2.适配器模式

适配器模式充当两个不兼容接口之间的连接器，否则无法直接连接。适配器使用新接口包装现有类，以使其与客户端的接口兼容。

使用此模式的主要动机是将现有接口转换为客户端期望的另一个接口。它通常在应用程序设计完成后实现。

### 2.1 适配器模式示例

考虑一种情况，其中有一款在美国开发的应用程序，以每小时英里数（MPH）返回豪华车的最高速度。现在我们需要为英国的客户使用相同的应用程序，这些应用程序需要相同的结果，但以公里/小时（km / h）为单位。

为了解决这个问题，我们将创建一个适配器，它将进行值转换并返回我们所需的结果：

首先，我们将创建原始接口Movable，目的是以每小时英里数的速度返回一些豪华车的速度：

```
public interface Movable {
    // returns speed in MPH 
    double getSpeed();
}
```
我们现在将创建此接口的一个具体实现：

```
public class BugattiVeyron implements Movable {
    @Override
    public double getSpeed() {
        return 268;
    }
}
```

现在我们将创建一个适配器接口MovableAdapter，它将基于同一个Movable类。它可以稍作修改，以在不同的场景中产生不同的结果：

```
public interface MovableAdapter {
    // returns speed in KM/H 
    double getSpeed();
}

```

此接口的实现将由用于转换的私有方法convertmphtokmph()组成：

```
public class MovableAdapterImpl implements MovableAdapter {
    private Movable luxuryCars;

    // standard constructors
    @Override
    public double getSpeed() {
        return convertMPHtoKMPH(luxuryCars.getSpeed());
    }

    private double convertMPHtoKMPH(double mph) {
        return mph * 1.60934;
    }
}

```
现在我们只使用适配器中定义的方法，我们将获得转换后的速度。在这种情况下，以下断言将成立：

```
@Test
public void whenConvertingMPHToKMPH_thenSuccessfullyConverted(){
	Movable bugattiVeyron = new BugattiVeyron();  
	MovableAdapter bugattiVeyronAdapter = new MovableAdapterImpl(bugattiVeyron);
	assertEquals(bugattiVeyronAdapter.getSpeed(),431.30312,0.00001);
}
```
我们在这里可以注意到，对于这种特殊情况，我们的适配器将268英里/小时转换为431公里/小时。

### 2.2 何时使用适配器模式

当外部组件提供我们想要重用的迷人功能时，它与我们当前的应用程序不兼容。可以开发合适的适配器以使它们彼此兼容
当我们的应用程序与我们的客户期望的接口不兼容时
当我们想要在我们的应用程序中重用遗留代码而不对原始代码进行任何修改时

## 3.结论

在本文中，我们了解了Java中的适配器r设计模式。

GitHub上提供了此示例的完整源代码。

原文链接：https://www.baeldung.com/java-adapter-pattern

