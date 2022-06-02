# 10. Decimal, Money, 그리고 기타 클래스
- 어떤 통화라도 오류없이 정확하게 기입할 수 있는, 금융분야에 특화된 데이터 활용방법을 알아보자.
- 이 장에서는 Decimal 클래스와 우리가 직접 구축할 Money 클래스를 소개할 것이다. 
<br><br>


## 10.1 숫자 클래스의 개요
- 이 책에서는 대부분 두가지의 숫자데이터를 다룸 (정수, 부동소수점)
  - 이 두가지 데이터타입만으로 대부분의 기능을 구현할 수 있긴 하지만, 모든 기능을 완벽히 구현할 수 있는 것은 아님
  - 정수는 소수점이 없어서 갖는 제약이 있고, 부동소수점은 반올림 오차로 인한 문제가 있음
<br>

- 이 장에서는 아래의 클래스를 소개한다.
  - Decimal 클래스 : 정확하고 오차없이 10진수 소수점을 가질 수 있는 '고정-포인트' 데이터 타입
  - Money 클래스 : 직접 개발해볼 것
  - Fraction 클래스 : 1/3과 같은 분수를 반올림 오차 없이 저장할 수 있음
  - complex 클래스 : 고등수학의 복소수 숫자를 나타냄. 실수와 허수로 나뉨. int, float과 같이 내장된 클래스
<br> <br>

## 10.2 부동소수점 포맷의 제약 사항
- float 값의 문제점 : 10진수 포맷으로 출력되지만 내부적으로는 이진수로 저장됨
  - 예를 들어, 1/10(0.1)은 1/2, 1/4처럼 2의 제곱이 아니라서 정확한 이진수 포맷으로 저장할 수 없음 -> 반올림한 값은 정확한 값과의 괴리가 발생
  - 일반적인 프로그래밍에서는 이러한 부동소수점 오차를 허용하지만, 금융 애플리케이션에서는 상황이 다름
    - 작은 오류도 시간이 지남에 따라 누적된다면 큰 오류가 될 수 있는 것
    - 이러한 문제를 파이썬에서는 Decimal 클래스를 사용하여 해결할 수 있음
<br> <br>

## 10.3 Decimal 클래스 소개
- 아래와 같이 Decimal 클래스를 import하고 인스턴스를 정의할 수 있음
  - 반올림 에러가 없는 값을 저장하기 위해서는 텍스트 문자열 값을 사용하여 초기화해야 함

```python
from decimal import Decimal

my_dec = Decimal()
print(my_dec)
# 0을 반환

d = Decimal('0.1')
print(d+d+d)
# 0.3을 정확히 반환 (부동소수점 오차 없음!)

d = Decimal(0.1)
print(d)
# 0.100000000000005511151
# 부동소수점 오차가 해결되지 않음 -> 이미 반올림 에러를 포함한 값을 넘겼기 때문!
# 이래서 문자열 값을 넘기는 것임
```

- Decimal 클래스의 특성
  - 연산결과 자리수를 보존함 (소수점 이하 자리수)
  - 두 객체를 곱할 경우, 소수점 이하 자리수는 증가함 -> round 함수를 사용하여 조정
  - 정수는 Decimal 객체와 자유롭게 더하거나 곱할 수 있고, 정수로 Decimal 객체를 정확히 초기화할 수 있음(텍스트 문자열처럼)
  - 부동소수점 값을 Decimal 객체와 연산하려고 하면 에러 발생
  - Decimal 객체를 생성하는 것은 부동소수점 객체를 생성하는 것보다 30배 더 많은 시간이 걸리며, 연산처리 속도는 60배 더 걸림
    - 정확성과 속도의 트레이드오프로, 정확성이 중요한 상황이 아니라면 부동소수점을 사용하는 것이 이득이다. 

<br> <br>

## 10.4 Decimal 객체를 위한 특수 연산
- Decimal 객체를 생성하여 도움말을 호출하면 수많은 연산과 메서드를 확인 가능
  - help(Decimal)
  - 예시로 아래와 같은 메서드들이 있음
    - normalize : 소수점 이하의 0을 제거, 객체가 가져야할 최소한의 소수만을 갖게 함 (다만, 이때 숫자상으로는 같아도(==), 객체는 신규객체 생성(is))
    - as_tuple : 객체의 내부 정보를 확인할 수 있는 주요 정보 제공, tuple로 decimal 표현 가능
    - getcontext : 최대 소수 자릿수, 올림하는 숫자 등의 정보 제공

<br> <br>

## 10.5 Decimal 클래스 애플리케이션
- 사용자가 입력하는 값을 더해주는 애플리케이션 
  - 매 값마다 반올림을 할지, 모든 값을 더한 이후에 반올림을 할지에 따라 소수점 끝값이 달라짐
  - 이런 작은 오차를 잘 다루어야 함 

<br> <br>

## 10.6 Money 클래스 설계하기
- 프로그래밍을 할 때 돈을 다루는 것은 굉장히 까다로우므로, Money 클래스를 직접 만들어보자.
  - 통화 단위를 표현하는 units과 함께 10진수 수치를 저장하면 유용할 것
    - unit은 'USD', 'EUR', 'CAD' 3개를 사용
  - 포함 vs 상속
    - 포함 : Money 객체를 컨테이너로 간주, 이 컨테이너에 단위 필드와 Decimal 객체를 함께 저장 (단점은 별도의 매직 메서드 작성 필요)
    - 상속 : Money 객체에 단위 필드 속성을 추가, 한 종류의 특별한 Decimal 객체로 간주
    - 둘 중 하나를 고르자면 상속이 더 나은 방법일 것(A는 B의 한 종류이지만, 특화 기능을 추가), 그러나 파이썬 언어는 이런 상황에서 상속을 사용하기 어려움
    - 그렇기 때문에 Decimal 클래스를 사용하여 Money 클래스를 만드는 식의 객체 포함 방법을 사용할 것 (상속을 사용하는 방법은 10.12에서)
      - Money 객체는 2개의 파트로 나뉘며, Decimal 객체인 dec_amt와 units로 불리는 문자열(str)로 구성될 것     

<br> <br>

## 10.7 기본 Money 클래스 작성하기 (포함 방식으로)
- 간단한 클래스 정의문으로 Money 객체를 만들고 속성 값들을 출력할 수 있음
- 여기에 Money 객체를 알아보기 쉽게 자동출력하는 기능도 추가할 것

```python
from decimal import Decimal

class Money():
  def __init__(self, v='0', units='USD'):
    self.dec_amt = Decimal(v)
    self.units = units

m1 = Money('0.10')
print(m1.dec_amt, m1.units)
# 0.10 USD
print(m1)
# <__main__.Money object at 0x103cc6f60>
```

<br> <br>

## 10.8 Money 객체 출력하기 ("\_\_str__", "\_\_repr__" )
- Money 객체의 출력을 지정하려면 이 클래스의 \_\_str__ 메서드를 작성해야 함
- 클래스의 기준 표현 방식도 설정하고 싶다면 \_\_repr__ 메서드를 별도로 추가하면 됨

```python
from decimal import Decimal

class Money():
  def __init__(self, v='0', units='USD'):
    self.dec_amt = Decimal(v)
    self.units = units
    
  def __str__(self):
    s = str(self.dec_amt) + ' ' + self.units
    return s
  
  def __repr__(self):
    s = ('Money(' + str(slef.dec_amt) + ' ' + self.units + ')')
    return s
    
m2 = Money('0.10')
print(m2)
# 0.10 USD
m2
# Money(0.10 USD)
```

<br> <br>

## 10.9 기타 Money용 연산
- 예시로 Money 객체간 덧셈 연산을 추가해보자.

```python
from decimal import Decimal

class Money():
  '''Money 클래스
  Decimal 값과 통화 단위를 함께 저장한다.
  객체를 더할 때, 통화 단위가 다르면 환율을 반영하여 더한다.
  '''
  #환율을 딕셔너리로 미리 저장해둠 -> 특정 테이블에서 읽어오는 방식으로 변환하면 더 좋을 것
  exch_dict = {
    'USDCAD' : Decimal('0.75'), 'USDEUR' : Decimal('1.16'),
    'CADUSD' : Decimal('1.33'), 'CADEUR' : Decimal('1.54'),
    'EURUSD' : Decimal('0.86'), 'EURCAD' : Decimal('0.65')
    }

  def __init__(self, v='0', units='USD'):
    self.dec_amt = Decimal(v)
    self.units = units
    
  def __str__(self):
    s = str(self.dec_amt) + ' ' + self.units
    return s
  
  def __repr__(self):
    s = ('Money(' + str(slef.dec_amt) + ' ' + self.units + ')')
    return s
  
  
  # 추가된 덧셈 함수
  
  def __add__(self, other):
    '''통화 더하기 함수
    2개의 Money 객체를 더한다.
    두번째 통화가 다른 통화 단위를 갖고 있다면
    두 값을 더하기 전에 환율이 반영되어야 한다.
    소수 두번째 자리로 반올림을 한다.
    '''
    
    if self.units != other.units:
      # 두 개체의 통화 단위로 key를 만들어서 원하는 환율을 찾음 (others를 self의 통화단위로 변환)
      r = Money.exch_dict[self.units + other.units]
      m1 = self.dec_amt
      m2 = other.dec_amt * r
      m = Money(m1+m2, self.units)
      
    else:
      m = Money(self.dec_amt + other.dec_amt, self.units)
      
    m.dec_amt = round(m.dec_amt, 2)
    return m
```

<br> <br>

## 10.10 데모: Money 계산기
- 사용자 입력을 보다 간편하게 만들 수 있는 Money 더하기 계산기를 만들어보자.

```python
from decimal import Decimal

# Money 클래스 정의문을 여기에 넣거나 import해야 함

def money_calc():
  ''' Money 더하기 계산기
  빈 문자열을 입력하기 전까지, Money 객체 나열을 입력받는다.
  빈 문자열이 입력되면 전체 합계를 출력한다.
  ```
  
  n = 0
  while True:
    s = input('Enter money value: ')
    s = s.strip()
    
    # 빈 문자열이 들어오면 break
    if not s:
      break
      
    a_list = s.split()
    d = a_list[0]
    
    if len(a_list) > 1:
      m = Money(d, a_list[1])
    else:
      m = Money(d) # 통화단위를 입력하지 않으면 USD
    
    # 최초 값의 통화단위를 기준으로 더하기를 시행함
    # 기본 통화단위는 USD, 만약 최초 값의 통화단위가 USD가 아니라면 이후의 값들은 USD라도 USD를 명시해야 함
    if n == 0:
      amt = m
    else:
      amt += m
      
    n += 1
  
  print('Total is', amt)
  
money_calc()
      
```

<br> <br>

## 10.11 기본 통화 설정하기
- 사용자가 직접 기본단위를 설정할 수 있도록 Money 클래스를 변경해보자.

```python

class Money():

  default_curr = 'USD'

  def __init__(self, v='0', units=''):
    self.dec_amt = Decimal(v)
    if not units:
      self.units = Money.default_curr
    else:
      self.units = units

```

<br><br>

## 10.12 Money와 상속
- Money 클래스를 만드는 최선의 방법은 상속으로, Decimal의 하위 클래스를 만드는 것이다.
- 문제는 Decimal 타입이 불변이라는 것이지만, 몇 줄의 코드로 해결할 수 있다.

<br>

- 일반적인 상속은 구현하기 쉽다. 
  - Money 클래스의 상위 클래스 이름을 Thingie라고 하고, 가변타입이라고 가정해보자.
  - 이러한 경우에는 아래와 같이 쉽게 작성할 수 있는 코드를 사용한다.

```python
class Money(Thingie):
  
  def __init__(self, v, units='USD'):
  
    # 상위 클래스인 Thingie의 __init__ 메서드를 실행 (부모 클래스의 메서드 호출)
    # Decimal은 불변 타입이기 때문에 사용할 수 없음
    super().__init__(v)
    # 두번째 인수는 직접 초기화
    self.units = units
```

<br>

- Money 클래스에서 상쇽받을 Decimal 클래스의 유산은 \_\_new__ 메서드에 의해 제어된다.
```python
from decimal import Decimal

class Money(Decimal):
  
  # 여기서의 cls는 숨겨진 파라미터(인자)로, 클래스 자신을 뜻함
  # 상위 클래스 안에서 유래한 클래스를 초기화 (이 경우에는 v)
  # 상위 클래서의 __new__가 반환하는 값을 그대로 반환해야 함
  def __new__(cls, v, units='USD'):
    return super(Money, cls).__new__(cls, v)
    
  def __init__(self, v, units='USD'):
    self.units = units

m = Money('0.11', 'USD')
print(m, m.units)
# 0.11 USD 출력

```

<br>

- 이 코드는 다음과 같이 좀 더 일반화할 수 있다.
  - d는 상위 클래스의 데이터, other_data는 \_\_init__에서 반드시 초기화되어야 하는 하위 클래스의 데이터이다.

```python
class MyClass(MySuperClass):
  def __new__(cls, d, other_data):
    return super(MyClass, cls).__new__(cls, d)
```

<br>

- 파이썬이 이러한 상속을 어렵게 만든 이유가 몇가지 있다. 한 가지로는 부모 클래스가 불변이라는 것은 한번 생선된 데이터는 절대로 변경되지 않는다는 것을 의미한다. 또한, 일부 내장 클래스는 추가로 \_\_new__ 함수를 사용하여 값을 초기화하며, 부모 클래스의 \_\_init__ 함수로는 부족하다는 것을 의미한다. 

<br><br>

## 10.13 Fraction 클래스
- 














