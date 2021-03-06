# 9. 클래스와 매직 메서드
- 파이썬에는 클래스가 존재하고, 프로그래밍 언어 세계에서 클래스가 존재한다는 것은 사용자-정의 타입을 작성하여 능력을 부여할 수 있다는 것을 의미한다.
- 파이썬 클래스의 독특한 기능으로, 특정 상황에서 자동 호출되어 응답하는 매직 메서드가 있다.
<br>

## 9.1 클래스와 객체 기본 문법
- 파이썬에서 클래스를 정의하는 기본 문법은 다음과 같다.
```python
class 클래스_이름:
  문장
```
<br>

- 문장은 동일한 들여쓰기를 1개 이상의 줄로 작성한 코드로 구성되며, 연산 처리할 것이 없다는 의미로 pass 키워드를 사용할 수 있다.
- 그럼 파이썬에서 이 클래스를 가지고 뭘 할 수 있을까?
  - 간단하다. 우리는 <b>'인스턴스'</b>라고 부르는 클래스 객체를 원하는 만큼 생성할 수 있다.
<br>

- 이제 클래스에 변수 몇개를 생성해 보자. 이 변수들은 <b>'클래스 변수'</b>가 될 것이며, 모든 객체에 공유된다.
- 인스턴스는 클래스 변수에 해당 인스턴스만을 위한 값을 대입(override)할 수 있다.
```python
class Car:
  accel = 3.0
  mpg = 25
 
car1 = Car()
print(car1.accel) # 3.0을 출력
car1.accel = 5.0  # car1 객체의 accel 변수를 인스턴스 변수로 변경

```
<br>

## 9.2 인스턴스 변수에 대해 더 알아보자
- 다른 프로그래밍 언어와는 다르게, 파이썬의 인스턴스 변수는 클래스 안에서 직접 생성되지 않는다. <br> 대신 필요할 때마다 바로 생성되거나 \_\_init__ 메서드 안에서 생성된다. <br> (자바 등의 다른 언어는 인스턴스 변수도 사용 전에 멤버 변수로 미리 정의해야 하지만, 파이썬 인스턴스 변수는 미리 정의하지 않고 필요할 때 바로 값을 대입한다는 뜻)
- 인스턴스 변수를 생성하는 규칙은 다른 변수 생성 규칙과 같다. 
  - 객체.변수_이름 = 값
  - 아래 예시에서 각 변수는 생성된 인스턴스 객체만을 위한 속성이다. 다른 객체들은 동일한 속성을 가질 필요가 없다.
  - 그렇다면, 동일한 클래스의 모든 객체가 같은 속성(같은 인스턴스 변수)을 가질 수 있을까? 정답은  \_\_init__ 메서드를 활용하는 것이다!

```python
class Dog :
  pass

my_dog = Dog()  # Dog 인스턴스 생성

# 3개의 데이터 변수를 my_dog 객체에 할당
my_dog.name = 'Wonder Dog'
my_dog.breed = 'Great Dane'
my_dog.age = 5
```
<br>

## 9.3 \_\_init__ 메서드와 \_\_new__ 메서드
- \_\_init__ 메서드를 정의한 모든 클래스는 객체가 생성될 때 자동으로 \_\_init__ 메서드가 호출(invoke)된다. <br> 클래스의 모든 인스턴스가 동일한 공통 변수들을 가지면서도 각 인스턴스별로 독립적인 값도 가지게 하려고 할 때 유용한 기능이다.
- 파이썬은 사실 \_\_new__ 메서드로 객체를 생성하지만, 대부분의 초기화 작업은 \_\_init__ 메서드에서 수행된다. 

```python
class 클래스 이름:
  def __init__(self, args):
    문장...

# self라는 단어는 반드시 사용해야 하는 키워드는 아님
# 생성된 객체를 참조하기 위해 첫번째 인수로 배치되는 것으로, 이름은 어떤 것을 써도 상관없지만 보편적으로 self를 사용
# args는 인수가 여러 개일 수도 있다는 뜻으로, 객체를 생성할 때 넘겨줌
# 위의 Dog 클래스 정의문을 __init__메서드를 포함하게 고쳐보자

class Dog:
  def __init__(self, name, breed, age):
    self.name = name
    self.breed = breed
    self.age = age
    
# 이제 클래스 Dog 객체를 생성할 때 반드시 인수를 3개 입력해야 하고, 인수들은 __init__메서드로 전달됨
good_dog = Dog('WonderBoy', 'Collie', 11)

# 보다 일반화하여 정리하면 아래와 같음
class 클래스 이름:
  def __init__(self, 인수1, 인수2, ...):
    self.인스턴스_변수1 = 인수1
    self.인스턴스_변수2 = 인수2
    ...
```
<br>

## 9.4 클래스와 선행 참조 문제
- (당연한 이야기이지만) 클래스는 인스턴스 객체를 생성하기 전에 정의되어야 한다. <br> 아래의 몇가지 규칙을 지킨다면 선행 참조 이슈가 문제될 일은 없을 것이다.
  - 반드시 모든 클래스를 인스턴스화하기 전에 정의해야 한다.
  - 클래스 상호 간에 인스턴스화를 하거나, (신이 금기한) 스스로 인스턴스를 만드는 클래스를 극도로 주의하라.
  - 하지만 클래스가 다른 클래스를 (단방향으로) 포함하거나 클래스 인스턴스 참조를 지니는 경우는 일반적으로 문제가 되지 않는다. 상호 돔시 참조는 주의하자. 
<br>

## 9.5 메서드 기본
- 메서드는 여러 측면에서 기본적인 함수와 구분된다.
  - 첫번째로, 메서드는 클래스 정의문 안에서 정의된다.
  - 두번째로, 메서드는 항상 인스턴스를 통해 호출되며, 숨겨진 인수가 전달된다. (해당 객체의 참조인 self)
```python
class 클래스_이름:
  def 메서드_이름(self, 인수1, 인수2, 인수3, ...):
    문장
    
객체_이름 = 클래스_이름()
객체_이름.메서드_이름(인수1, 인수2, 인수3, ...)
# 메서드 정의문에는 숨겨진 첫번째 인수인 self가 있는 반면, 메서드를 호출할 때는 존재하지 않는다. 따라서 정의문에 인수가 하나 더 많다. 

# 예제
class Pretty:
  def __init__(self, prefix):
    self.prefix = prefix
  
  # 메서드 안에서 메서드가 속한 인스턴스 자체는 항상 self로 참조할 수 있으며, 인스턴스 변수는 self.변수이름으로 참조할 수 있다.
  def print_me(self, a,b,c):
    print(self.prefix, a, sep='')
    print(self.prefix, b, sep='')
    print(self.prefix, c, sep='')
```
<br>

## 9.6 전역(global) 변수/메서드와 지역(local) 변수/메서드
- 전통적으로 객체 지향 언어를 사용하는 목적 중 하나는 캡슐화이다. 캡슐화는 클래스 내부 내용이 바깥에 노출되지 않도록 한다.
- 파이썬의 철학은 이와 대치된다. 스크립팅 언어인 파이썬은 모든 것을 노출하는 경향이 있으며, 모든 것을 볼 수 있다.
  - 그러나 파이썬은 유용한 규약을 따른다.
    - 변수와 메서드 이름이 언더스코어 1개(\_)로 시작하면 내부용 이라는 뜻이다.
    - 언더스코어 2개(\_\_)로 시작하는 것은 맹글링(mangling)이라는 기법으로, 의도되지 않은 접근을 통제한다.
```python
class Odd:
  def __init__(self):
    self.x = 10
    self.y = 20
    self.__z = 30
    
  def pr(self):
    print('__z = ', self.__z)
    
o = Odd()
o.x
o.y
o.__z # 에러!
# 마지막의 o.__z에서 에러가 발생하는 이유는 파이썬이 변수 __z를 클래스 이름과 변수 이름의 조합으로 훼손한(mangled) 이름으로 교체하기 때문
# 하지만 __z는 여전히 동일한 클래스의 메서드 정의문 안에서 맹글링 없이 접근 가능함 -> 메서드 pr은 문제없이 동작
```
<br>

## 9.7 상속
- 파이썬은 하위 클래스 만들기(subclassing)로 상속(inheritance)을 지원한다. <br> 하나의 상위(base) 클래스를 단일 상속하는 문법부터 보자.
```python
class 클래스_이름(상위_클래스):
  문장
  
# 이렇게 만들어진 클래스는 상위_클래스의 모든 클래스 변수와 메서드를 상속받는다
# 물론 신규 변수나 메서드 정의문을 추가하거나 기존 정의문을 재정의할 수 있다
```
<br>

- 파이썬의 모든 변수와 메서드 이름은 다형적(polymorphic)이다. 이름은 실행 전까지 확정되지 않기 때문에, 어떤 객체의 어떤 메서드라도 호출가능하며 코드가 실행될 때 변수와 메서드의 이름이 확정될 것이다.
- 아래 예시에서는 Mammal이라는 상위 클래스와 Dog, Cat이라는 2개의 하위 클래스가 있다. <br> 하위 클래스는 Mammal의 \_\_init__메서드와 call_out 메서드를 상속받지만, 각 구현체는 각자의 speak 메서드를 가진다.  
```python
class Mammal:
  def __init__(self, name, size):
    self.name = name
    self.size = size
    
  def speak(self):
    print('My name is', name)
   
  def call_out(self):
    self.speak()
    self.speak()
    self.speak()
    
class Dog(Mammal):
  def speak(self):
    print('ARF!!')
    
class Cat(Mammal):
  def speak(self):
    print('Purrrrrrr!!')
    
my_cat = Cat('Precious', 17)
my_cat.call_out()  # 'Purrrrrrr!!'이 print될 것
# speak 메서드를 override 했기 때문!

# Dog와 Cat 클래스는 __init__메서드를 상속받음 -> 상위 클래스의 __init__을 최대한 활용하면서 추가적인 초기화 작업이 필요하다면?
# 하위 클래스에서 새로운 __init__메서드 정의문을 추가하되, 상위 클래스 버전의 __init__메서드를 호출 -> super().__init__
# 아래 예시에서 Dog.__init__ 정의문은 변수 breed를 스스로 초기화하고 super().__init__을 호출해 초기화의 나머지 부분을 수행함

class Dog(Mammal):
  def speak(self):
    print('ARF!!')
  def __init__(self, name, size, breed):
    super().__init__(name, size)
    self.breed = breed

```
<br>

## 9.8 다중 상속
- 파이썬의 유연한 문법은 다중 상속을 지원한다. (클래스를 생성할 때 2개 이상의 상위 클래스를 상속받을 수 있음)
- 다중 상속을 사용하면 충돌이 발생할 수 있다. (상위 클래스 간에 동일한 메서드나 클래스 변수 이름을 사용한다면 문제 발생)
```python
class Dog(Mammal, Pet, Carnivore):
  def speak(self):
    print('ARF!!')
    
  def __init__(self, name, size, breed):
    Mammal.__init__(self, name, size)
    self.breed = breed
```
<br>

## 9.9 매직 메서드 개요
- 미리 정의된 이름을 사용하는 메서드를 **매직 메서드**라고 한다.
  - 의미를 미리 정의한 메서드 이름은 언더스코어 2개로 시작하고 끝나므로, 이러한 규칙을 사용하지 않는다면 해당 메서드들과 충돌할 일이 없다.
- 매직 메서드 소개
  - \_\_init__ 메서드와 \_\_new__ 메서드는 객체가 초기화되고 생성될 때 자동으로 호출
  - 객체 표현 메서드 (\_\_format__, \_\_str__, \_\_repr__)
  - 비교 메서드 (\_\_eq__(일치여부 확인), \_\_gt__(greater than), \_\_lt__(less than) 등)
  - 산술 연산자 메서드 (\_\_add__, \_\_sub__, ...)
  - 단항 산술 연산자 메서드 (\_\_pos__, \_\_neg__, ...)
  - 비트 연산자 메서드 (\_\_and__, \_\_or__, ...) : 대부분 사용하지 않으므로 이 책에서 다루지 않음
  - 리플렉션 메서드 (\_\_radd__, \_\_rsub__, ...) : r로 시작하는 메서드, A클래스가 B클래스와 연산처리를 할 때, B클래스가 A클래스를 알지 못할 때 필요하다.
  - 교체 대입-연산 메서드 (\_\_iadd__, \_\_isub__, ...) : i로 시작하는 메서드, +=와 같은 대입 연산 처리를 지원
  - 변환 메서드 (\_\_int__, \_\_float__, ...)
  - 컬렉션-클래스 메서드 (\_\_len__, \_\_getitem__, ...) : 자체 컬렉션을 생성할 수 있게 해준다.
  - 문맥과 피클링 메서드 (\_\_getstate__, \_\_setstate__) : 이 책의 범위를 벗어나므로 추가로 설명하지 않음
  - \_\_call__ 메서드 : 클래스의 인스턴스를 함수처럼 호출할 수 있게 만들 때 사용한다.
<br>

## 9.10 매직 메서드 상세

### 9.10.1 파이썬 클래스의 문자열 표현
- \_\_format__, \_\_str__, \_\_repr__를 포함한 몇 가지 메서드로 클래스 자체를 표현할 수 있다.
  - 포맷 함수는 포맷 규약을 출력 대상 객체에 전달한다. 올바른 결과는 해당 규약으로 표현한 문자열이 반환되는 것이다. 
  - 예시로, format(6, 'b')는 6의 바이너리 표현을 반환한다. ('110')
  - 문자열을 표현할 때의 일반적인 내부 흐름을 요약하면 아래와 같다.
    - format 함수는 객체의 \_\_format__ 메서드 호출을 시도하며, 필요시 규약을 전달한다. <br> 이 메서드를 구현한다는 것은 클래스가 새로 정의한 포맷으로 표현한 문자열을 반환한다는 뜻이다. <br> 기본동작방식은 \_\_str__ 메서드를 호출한다.
    - print 함수는 객체 클래스의 \_\_str__ 메서드를 호출한다.
    - \_\_repr__ 메서드는 객체 표준 표현 방식의 문자열을 반환한다.  
    
<br>

### 9.10.2 객체 표현 메서드
- 객체 표현 메서드와 설명을 아래에 정리한다.
  - \_\_format__(self, spec) : format 함수에 객체를 직접 전달했을 때 호출, 반드시 포매팅된 문자열을 반환
  - \_\_str__(self) : 사용자가 원하는 형태로 객체 데이터를 담은 문자열을 반환, 구현하지 않았다면 기본적으로 \_\_repr__ 메서드를 호출
  - \_\_repr__(self) : 객체 표준 표현 방식으로 문자열을 반환
  - \_\_hash__(self) : hash 함수에 객체가 인수로 주어졌을 때 호출, 해시 코드를 생성하여 해당 객체 타입을 데이터 딕셔너리에서 키로 사용할 수 있게 해줌, 반드시 정수를 반환
  - \_\_bool__(self) : 불리언 변환 메서드, 모든 bool 함수를 호출하면 항상 호출됨, 제대로 동작하면 True 혹은 False를 반환
 
- 아래 예제는 Point 클래스에서 \_\_str__과 \_\_repr__ 메서드를 \_\_init__과 함께 작성하는 이론적인 방법을 보여준다. 
```python

class Point:
  big_prime_1 = 1200556037
  big_prime_2 = 2444555677
  
  def __init__(self, x=0, y=0):
    self.x = x
    self.y = y
    
  def __str__(self):
    s = str(self.x) + ', '
    s += str(self.y)
    return s
    
  def __repr__(self):
    s = 'Point(' + str(self.x) + ', '
    s += str(self.y) + ')'
    return s
    
  def __hash__(self):
    n = self.x * big_prime_1
    return (n + self.y) % big_prime_2
    
  def __bool__(self):
    return x and y

pt = Point(3,4)
pt
# Point(3,4) 가 출력됨 (repr)
print(pt)
# 3,4가 출력됨 (str)

```

<br>

### 9.10.3 비교 메서드
- 비교 메서드는 클래스 객체를 ==(같다), !=(같지않다), 부등식(>,<,>=,<=)을 사용하여 서로 비교하게 해준다.
  - 최소한 객체가 서로 동일하다는 것을 테스트하는 메서드를 직접 구현하는 것이 좋은데, 그렇지 않으면 ==의 기본 동작은 is 연산이 되어 객체의 값이 아닌 주소를 비교하기 때문에 값이 같더라도 False가 반환되는 사례가 발생할 수 있다. 
  - 다른 타입 간 비교에 대해서도, if 문을 사용하여 타입이 다른 경우를 정의하면 비교가 가능한 객체를 만들 수 있다. 

<br>

### 9.10.4 산술 연산자 메서드
- 클래스 인스턴스가 연산자의 앞 또는 뒤에 있을 때에 호출되는 메서드 (덧셈, 뺄셈, 나눗셈 등)

<br>

### 9.10.5 단항 산술 연산자
- 양수, 음수, 절대값, 불리언 변환, 반올림, 올림, 내림 등의 단항 산술 연산자

<br>

### 9.10.6 리플렉션(역방향) 메서드
- 객체가 표현식에서 연산자의 우측에 있거나 두번째 인수일 때 호출되는 메서드로, 주로 앞에 r이 붙는다.

<br>

### 9.10.7 교체 연산자 메서드
- 산술 연산자와 대입 연산자의 조합 기능을 제공하는 매직 메서드로, 주로 앞에 i가 붙는다. (+=, -=, \*= 등)
- 여기서의 i는 in place를 의미한다. (구현하지 않아도 파이썬은 대입 연산을 지원하지만, 그 때에는 메모리의 값을 inplace하지 않는다. 대신 신규 객체를 생성하여 변수에 다시 대입한다.)

<br>

### 9.10.8 변환 메서드
- 객체를 다른 타입으로 변환시키려고 할 때 호출되는 메서드
  - 예를 들어, 조건문에서 객체를 사용할 때, 파이썬은 암시적으로 True 혹은 False 값을 갖기 위해 bool 변환 메서드를 호출한다. 
  - 조건문에 클래스의 객체가 주어졌을 때, 어떤 값을 반환할지 결정하는 기준 

<br>

### 9.10.9 컬렉션 클래스 메서드
- 원하는 방식대로 저장되는 자체 컬렉션을 구현할 수 있다.

<br>

### 9.10.10 \_\_iter__와 \_\_next__ 구현하기
- for loop과 같은 특별한 상황에 사용할 수 있는 클래스 객체의 제너레이터(이터레이터)를 생성한다.
  - 이터러블(iterable) : 여러 항목을 한번에 한 항목씩 접근하여 step through할 수 있는 객체로, 객체가 이터러블이 되기 위해 \_\_iter__메서드는 반드시 객체를 반환해야 함
  - 이터레이터(iterator) : \_\_iter__메서드가 반환해야 하는 객체

<br>

## 9.11 다중 인수 타입 지원
- 하나 이상의 인수를 가지는 함수 또는 메서드의 작성 방법
- 다른 타입 간 연산 또한 if 문으로 각 경우마다의 연산을 정의하면 가능하다. 

<br>

## 9.12 동적 속성 설정 및 조회
- setattr(객체_이름, 문자열_이름, 값)
- getattr(객체, 문자열_이름, \[, 기본값])














