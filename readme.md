# Garbage Collector

Garbage Collector, 가비지 컬렉터(이하 GC) 는 메모리 관리에 대한 책임을 개발자가 아닌 런타임에 위임을 하여 개발자의 메모리 관리 부담을 줄일 수 있는 시스템이다.

## GC의 장점
1. 메모리 관리의 단순화

개발자들이 메모리를 수동으로 할당 및 해제하는 과정에서 오류를 범하는 가능성이 존재한다. 가비지 컬렉터는 무효화된 객체를 자동으로 회수함으로써 개발자들이 메모리 관리에 신경을 쓸 필요가 없게 한다.

2. 메모리 누수의 예방

프로그램에서 더 이상 사용되지 않는 객체가 메모리에 지속적으로 남아 있을 경우, 메모리 누수 현상이 발생한다. 가비지 컬렉터는 참조되지 않는 객체를 파악하고 제거하여 메모리 누수를 막아준다.

3. 프로그램의 안정성 증진

메모리 부족으로 인하여 프로그램이 중단되는 문제를 회피할 수 있다. 가비지 컬렉터가 메모리를 효율적으로 관리함으로써, 시스템의 안정성이 유지되는데 기여한다.

4. 코드 유지 및 관리의 개선

가비지 컬렉터를 활용함으로써, 개발자들은 메모리의 할당 및 해제에 관한 코드를 작성할 필요가 없어져 전체적인 코드가 간결해지며 유지 및 관리에 이점을 가져온다.

## GC의 동작 순서
![img](https://github.com/vernonKwon/big-b-vernon_middle_assignment2/assets/29453831/6f8e4804-aa78-46e3-b755-0d22911cd5ed)

1. gc가 stack의 모든 변수를 순회, 스캔하면서 각각 어떤 객체가 어떤 객체를 참조하고 있는지 확인, 마킹한다.

2. (개념적으로) 유효한 참조라면 Reachable, 유효하지 않은 참조라면 Unreachable로 분류한다.

3. 마킹되지 않은 객체를 Heap에서 제거한다.

## GC가 있음에도 메모리릭이 발생하는 패턴

``` java 
public class Adder {
       public long addIncremental(long l)
       {
            Long sum = 0L;
            sum = sum+l;
            return sum;
       }
       public static void main(String[] args) {
            Adder adder = new Adder();
            for(long i = 0; i < 1000; i++) {
                adder.addIncremental(i);
            }
       }
}
```

long 대신 Long를 사용함으로써, auto boxing 으로 인하여 ```sum = sum + i;``` 에서 매 반복마다 새 객체를 생성한다. 그 과정에서 1000개의 불필요한 객체가 생성된다.

2. 스트림 객체를 사용하지 않고 닫는 경우
``` java
try
{
    Connection con = DriverManager.getConnection();
    // 에러 발생
    con.close();
} catch(exception ex) {

}
```

try 블록에서 연결 리소스를 닫는 구조이므로 ```close()``` 함수 호출 전에 예외가 발생 시 연결을 닫을 방법이 없다. 따라서 이 풀로 돌아오지 않기때문에 메모리 누수가 발생한다. 이는 곧 데드락 발생 위험이 커진다.

finally 블록에 ```close()``` 넣거나 Try-With-Resource 문법을 사용하여 처리하는 방식으로 대응할 수 있다.

3. 정적(static) 컬렉션에 객체가 계속 쌓이는 경우
```java
public class MemoryLeakExample {
    private static List<Object> list = new ArrayList<>();

    public void addObjectToList(Object object) {
        list.add(object);
    }
    // ...
}
```
addObjectToList 메소드를 호출하여 객체를 정적 리스트에 추가하면, 해당 객체들은 프로그램이 종료되기 전까지 메모리에서 제거되지 않는다. 이로 인해 메모리 누수가 발생할 수 있다.

4. etc

이 외에도 다양한 케이스가 있으나 복잡한 참조관계로 인하여 메모리 해제가 불가능한 패턴일 경우가 대부분이다.

필요하지 않은 객체의 참조를 정리할 수 있도록 코드 패턴을 달리하거나 프레임워크, 언어 차원에서 제공하는 생명주기를 활용하여 객체의 해제를 고려할 수 있다.


***
javascript 의 경우 Lexical Scope, Swift의 경우 Automatic Reference Counting 과 같은 다양한 키워드로 설명이 가능한 개념이다. 기술적으로 깊게 들어가면 매우 많이 다른 기술들이지만 근본적으로 객체 참조가 제대로 관리되어야 한다는 점은 동일하다.
