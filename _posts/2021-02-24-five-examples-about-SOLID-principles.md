---
layout: post
title: Five code examples about SOLID principles
tags: [OO Design, SOLID]
---

This example illustrates the importance using OOD tricks in your project. The article was migrate from my previous blog site tianjianxia.github.io/profile.

***

- Single responsibility

  - Each method or each class only process the work or a batch of work that have a atomic logic.

  ```java
      // Wrong example
      public < T > List<T> listSorting(List<T> l1, List<T> l2){
          l1.addAll(l2);
          l1.sort();
          return l1;
      }
  
      // Right example
      public < T > List<T> mergeList(List<T> l1, List<T> l2){
          l1.addAll(l2);
          return l1;
      }
  
      public < T > List<T> sortList(List<T> l){
          l.sort();
          return l;
      }
  ```

- Open and Close

  - A class or a feature should be designed to be extended easily
  - Every time add a new feature, we should add a new method
  - Every time modify an old feature, we should modify the method.

  ```java
      // Wrong example
      public String voice(Animal animal){
          // Everytime we add a type of animal into the system, we had to modify this method
          // And add a line with judgement
          if(animal instanceOf Dog) return "bark";
          if(animal instanceOf Cat) return "meow";
          return "";
      }
  
      // Right example
      // Only need to add new methods for new features
      public String voice(Dog dog){
          return "bark";
      }
  
      public String voice(Cat cat){
          return "meow";
      }
  ```

- Liskov Substitution

  - A child class can substitue the father class and be used, but has to be logically right.
  - The inheritance should not cause program logic misunderstanding or complier misunderstanding

  ```java
      // Wrong example
      class Rectangle {
          int width;
          int height;
          void setWidth(int width) {
              this.width = width;
          }
  
          void setHeight(int height) {
              this.height = height;
          }
  
          int area(){
              return width * height;
          }
      }
  
      class Square extends Rectangle {
          void setWidth(int width) {
              this.width = width;
              this.height = width;
          }
  
          void setHeight(int height) {
              this.width = height;
              this.height = height;
          }
      }
  
      void assertArea(Rectangle r){
          // When pass a Square as parameter, we get an unexpected area 16
          // So this Rectangle and Square inheritance is not valid
          r.setWidth(5);
          r.setHeight(4);
          assert(r.area() == 20);
      }
  
      void increaseWidth(Rectangle r){
          // When pass a Square as parameter, something horrible happens and cause a dead loop
          while(r.width < r.height){
              r.setWidth(r.width + 1);
          }
      }
  
      Rectangle r = new Rectangle();
      assertArea(r); // Answer is 20;
      Square s = new Square();
      assertArea(s); // Answer is 16;
  
      // Right example
      interface Quadrangle {
          void setWidth();
          void setHeight();
          int area();
      }
  
      class Rectangle extends Quadrangle {}
      class Square extends Quadrangle {}
  ```

- Interface Segregation

  - We need a "slim" interface instead of a "fat" interface which not depends on some methods not needed.
  - When design an interface, think about this: Someone who uses this interface is actually the consumer, not the implemented instances or passed parameters themselves.
  - In a nutshell, thinking about the consumer of the interface when design, they don't need some never used method.

  ```java
      // Wrong example
      interface Book {
          void read();
          void sell(); // The consumer(Readers) don't have any requirement to sell a book, since we need this sell book feature.
      }
  
      // Right example
      interface readBook {
          void read();
      }
  
      interface sellBook {
          void sell();
      }
  ```

- Dependence Inversion

  - A high-level consumer feature should not depend on a specific implemented instance.
  - Instead, it should easily handle different types of input.

  ```java
      // Wrong example
  
      // What if we want the Button not only controll Lamp, but TV or Microwave?
      // Of course we should not define an implemented instance in a higer level consumer
      class Button {
          Lamp lamp;
          void push(){
              if(lamp.isOn){
                  lamp.turnOff();
              } else {
                  lamp.turnOn();
              }
          }
      }
  
      class Lamp {
          boolean isOn;
          void turnOff(){
              isOn = false;
          }
          void turnOn(){
              isOn = true;
          }
      }
  
      // Right example
      class Button {
          ButtonServiceConsumer bsc;
          void push(){
              if(bsc.isOn){
                  bsc.turnOff();
              } else {
                  bsc.turnOn();
              }
          }
      }
  
      interface ButtonServiceConsumer {
          void turnOn();
          void turnOff();
      }
  
      class TV implements ButtonServiceConsumer {}
      class Lamp implements ButtonServiceConsumer {}
      class Microwave implements ButtonServiceConsumer {}
  ```

