---
title: "Java Lambda 정리"
date: YYYY-MM-DD HH:MM:SS +/-TT:TT
categories:
  - Java Basic
tags:
  - java
  - lambda
author_profile: true
last_modified_at: YYYY-MM-DD HH:MM:SS +/-TT:TT
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
   class LengthComparator implements Comparator<String> {
     public int compare(String first, String second) {
       return first.length() - second.length();
     }
   }

   Comparator<String> comp = new LengthComparator();
   if(comp.compare(  ))
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
- 람다 표현식은 **추상 메소드가 한개만 포함된 인터페이스(함수형 인터페이스)에만 사용**
- 설명 : Comparator 항목에서 정의한 EmployeeComparator는 compare 함수 하나만 정의.
- Arrays.sort 사용시 두번째 파라미터로 Comparator를 설정
  ```
  String[] words = {"bird","bus","peter"};
  Arrays.sort(words, new LengthComparator());
  ```
- 동일한 표현을 람다로 표현
   ```
   Arrays.sort(words, (first,second) -> first.length() - second.length());
   ```
- Comparator의 compare() 함수 내용을 람다표현식의 구현부에 정의
- Predicate 인터페이스 를 사용하는 ArrayList의 removeIf 메소드에서 사용하는 Predicate 인터페이스는 람다 표현식을 전달하기 위해 설계
   ```
   // 리스트에서 모든 null 값 제거
   list.removeIf(e -> e == null);
   ```

## 메서드 참조와 생성자 참조
### 메서드 참조
- 다른 코드에 전달하려는 액션을 수행하는 메서드
- 예제
  ```
  // 대소문자 구분없이 문자열 정렬
  Arrays.sort(strings, (x,y) -> x.compareToIgnoreCase(y));

  // 메서드 표현식 전달
  Arrays.sort(string, String::compareToIgnoreCase);
  ```
- String::compareToIgnoreCase는 람다 표현식 (x,y) -> x.compareToIgnoreCase(y))에 대응하는 메서드 참조
- 예제 2
  ```
  // null 제거, Objects 클래스는 isNull 정적 메서드 정의
  list.removeIf(Objects::isNull)

  //ArrayList읭 각 요소별 출력
  list.forEach(x -> System.out.println(x));

  // 메서드 참조
  list.forEach(System.out::println);
  ```
- :: 연산자는 메서드 이름과 클래스를 분리, 또는 메서드 이름과 객체 이름을 분리
  - 클래스::인스턴스메서드
  - 클래스::정적메서드
  - 객체::인스턴스메서드

### 생성자 참조
- Employee::new 는 Employee 생성자 참조임
- 클래스에 생성자가 두 개 이상 있을 땐 문맥을 통해 어떤 생성자를 호출할지 결정
   ```
   publi class Employee {
     // 문맥에 따라 생성자 선택
     public Employee(int employNo){

     }

     public Employee(String employeeName) {

     }
   }
   ```
- 배열타입 생성자 참조
   ```
   int[]::new

   n-> new int[n] 과 동일
   ```
- 배열 생성자 참조는 특정 class 지정 가능(Stream.toArray()는 특정 클래스를 지정하지 못하고 Object 배열 반환)
   ```
   Object[] employee = stream.toArray();

   // 배열 생성자 참조로 Employee 객체 타입으로 반환
   Employee[] employee = stream.toArray(Employee[]::new);
   ```

# 람다 표현식 처리
