---
title: "Java Lambda 정리"
date: YYYY-MM-DD HH:MM:SS +/-TTTT
layout:  Posts
permalink: /posts/
author_profile: true
last_modified_at: 2021-06-20
---

#인터페이스
##기본정의
- 구현 클래스에서 반드시 구현해야 할 메소드 정의
- 인터페이스의 메소드는 자동으로 public
- 인터페이스를 구현하는 클래스는 메소드를 public 선언 (미선언시 패키지 접근, 컴파일 오류)
- 인터페이스에 정의한 변수는 자동으로 public static final 정의
   ```
   public interface TestInterface {
     int UP = 1;
     int DOWN = 2;
     int RIGHT = 3;
     int LEFT = 4;
   }
   ```

## 인터페이스 확장
- 인터페이스를 상속받아 다른 인터페이스 정의
- 원래 메소드외에 추가 메소드를 정의할 시 사용
- 각각 다른 인터페이스를 상속한 클래스 구현 가능

## 정적메소드, 기본 메소드 선언
- java8 이후부터 추가
- 정적 메소드 public static 정의
- 기본 메소드에는 default  정의(예 : 인터페이스에 stream 같은 추가 기능 포함시 이전에 구현된 클래스에서 사용이 가능하도록 default 메소드를 인터페이스에 구현)
- 서로 다른 인터페이스에 동일한 기본 메소드 존재시 상속받은 클래스에서 해당 메소드를 재정의 필요

## Java 주요 인터페이스

### Comparable
- **객체 정렬**시 사용(기본 정렬방법 정의)
- public interface Comparable<T>
  ```
  public interface Comparable<T> {
    int compareTo(T other)
  }
  ```
- 구현 클래스에서 인터페이스를 상속받아 정의
   ```
   public class Employee implements Comparable<Employee> {
     public int compareTo(Employee other) {
       return Double.compare(salary, other.salary);
     }
   }
   ```

### Comparator
- 객체 정렬시 기본 정렬 방법외 기준을 정의
- public interface Comparator<T>
- 앞의 에제에서 직원의 연봉 정렬외에 다른 기준으로 정렬시 별도의 기준을 가진 정렬 메소드를 제공

   ```
   public interface Comparator<T> {
     int compare(T first, T second);
   }
   ```

   ```
   class EmployeeComparator implements Comparator<Employee> {
     public int compare(Employee first, Employee second) {
       return first.num() - second.num();
     }
   }

   Comparator<Employee> emp = new EmployeeComparator();
   if(emp.compare(  ))
   ```

### Runnable
- Thread 수행, run() 메소드 정의됨


### 사용자 인터페이스 콜백
- 이벤트가 발생했을 때 코드가 호출되는 액션
- java GUI 라이브러리 콜백 인터페이스 사용

   ```
   public interface EventHandler<T> {
     void handle(T event);
   }
   ```

   ```
   class CancleAction implements EventHandler<ActionEvent> {
     public void handle(ActionEvent event) {
       System.out.println("Oh noes!");
     }
   }

   // 사용 예
   Button cancelButton = new Button("Cancel");
   cancelButton.setOnAction(new CancelButton());
   ```

# 람다 표현식
## 문법
- 표현식을 여러 줄로 작성할 때는 {} 감싸고 명시적인 return
   ```
   (String first, String second) ->** {
     int difference = first.length() - second.length();
     if(difference < 0 ) return -1;
     else if(difference > 0) return 1;
     else return 0;
   }**
   ```
- 파라미터가 없으면 빈 괄호 선언
  ```
  Runnable task = **()** -> {for(int i = 0; i < 1000; i++) doWork();}
  ```
- 파라미터 타입 추론가능하면 파라미터 타입 생략
   ```
   Comparator<String> comp = **(first, second)** -> first.length() - second.length();
   ```

- 추론 대상의 타입 파라미터를 한 개만 받으면 괄호 생략
   ```
   EventHandler<ActionEvent> listener = event -> System.out.println("Oh noes!");
   ```

## 함수형 인터페이스
- 람다 표현식은 추상 메소드가 한개만 포함된 인터페이스(함수형 인터페이스)에만 사용
