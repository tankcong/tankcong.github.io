---
layout: post
title: Clean Code (Objects, DataStructure)
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Notes
    - Clean Code
---

# Clean Code (Objects, DataStructure)

## Objects, Data Structure
尽可能的**隐藏实现**，以抽象形态表述数据。避免盲目增加Getter/Setter暴露实现细节。  
数据结构：

```
public class Point {
    public double x;
    public doiuble y;
}

public interface Vehicle {
    double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();
}
```

对象：

```
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    double setPolar(double r, double theta);
}

public interface Vehicle {
    double getPercentFuelRemaining();
}
```

注意对象与数据结构的差异。**对象把数据隐藏于抽象之后，暴露操作数据的函数。数据结构
暴露其数据，没有提供有意义的函数。**

## Data/Object Anti-Symmetry
考虑下面两段代码：  
Procedural code(Data Structure)

```
public class Square {
  public Point topLeft;
  public double side;
}

public class Rectangle {
  public Point topLeft;
  public double height;
  public double width;
}

public class Circle {
  public Point center;
  public double radius;
}

public class Geometry {
  public final double PI = 3.141592653589793;

  public double area(Object shape) throws NoSuchShapeException
  {
    if (shape instanceof Square) {
      Square s = (Square)shape;
      return s.side * s.side;
    }

    else if (shape instanceof Rectangle) {
      Rectangle r = (Rectangle)shape;
      return r.height * r.width;
    }
    else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius;
    }
    throw new NoSuchShapeException();
  }
}
```

Object oriented code(OO)

```
public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side*side;
  }
}

public class Rectangle implements Shape {
  private Point topLeft;
  private double height;
  private double width;

  public double area() {
    return height * width;
  }
}
public class Circle implements Shape {
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  }
}
```
`Geometry`类添加任何函数都不会对已有形状产生影响，但如果新增另一种形状，就得
修改`Geometry`中的所有函数来处理它。  
`area()`方法是多态的，新增形状对现有函数不产生影响。但若`Shape`添加新函数，
则所有形状都得做修改。

> **过程式代码（使用数据结构的代码）便于在不改动既有数据结构的前提下添加新函数。
> 面向对象代码便于在不改动既有函数的前提下添加新类。**

反过来讲

> **过程式代码难以添加新数据结构，因为必须修改所有函数。面向对象代码难以添加新函数，
> 因为必须修改所有类。**

## The Law of Demeter

参考之前写的[Law of Demeter](https://github.com/tankcong/tankcong.github.io/blob/master/Law%20of%20Demeter.md)

