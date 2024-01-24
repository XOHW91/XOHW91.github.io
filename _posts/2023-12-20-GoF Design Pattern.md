---
layout: single
title:  "[CS] GoF Design Pattern"
---



>**디자인 패턴이란?**
>
>모듈의 세분화된 역할이나 모듈들 간의 인터페이스 구현 방식을 설계할 때 참조할 수 있는 전형적인 해결방식을 말한다.
>
>즉, 프로그램을 개발하는 과정에서 빈번하게 발생하는 디자인 문제를 정리해서 상황에 따라 간편하게 적용할 수 있도록 정리한 것.



# GoF(Gang of Four) 디자인 패턴

네 명의 저자가 시스템 프로그래밍 한 경험을 통해 일반적인 패턴이 나타나는 것을 발견하여 그 중 대표적인 23개의 패턴을 정리한 것이다. GoF 디자인 패턴은 **목적**에 따라 분류하면 크게 생성, 구조, 행동 3가지로 나누어 볼 수 있다.

|                             생성                             |                             구조                             |                             행위                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| Singleton<br />Prototype<br />Abstract Factory<br />Builder<br />**Factory Method** | <br />Bridge<br />Proxy<br />Decorator<br />Facade<br />Flyweight<br />Composite<br />**Adapter**<br /> | <br />Chain of Responsibility<br />Command<br />Iterator<br />Mediator<br />Memento<br />Observer<br />State<br />Strategy<br />Visitor<br />**InterPreter**<br />**Template Method**<br /> |

패턴을 클래스에 적용하는지, 객체에 적용하는 지 **범위**에 따라 구분을 하게 될 경우 위의 표에서 볼드체는 클래스, 나머지는 객체에 적용된다.