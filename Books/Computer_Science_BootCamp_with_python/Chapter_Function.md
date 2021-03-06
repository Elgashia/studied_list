# 함수(Function)

``` markdown
이 챕터는 함수의 **작동방식**을 이해하는 것이 목적입니다.
 그러기 위해 *전역변수, 지역변수, 스택 프레임*에 대해 알아야합니다.
그 후 함수에 인자를 전달하는 방식에 따라 실행결과가 어떻게 달라지는지 알아보자.
```

**전역 변수**
* 전역 변수는 말그대로 전체 영역의 변수다. \
즉, 전체 영역에서 사용 가능한 또는 접근 가능한 변수이다.

**지역 변수**
* 지역 변수는 전역 변수와는 다르게 특정 지역에서만 접근 가능한 변수이다.

지금 이 설명만으로는 무슨 소리인지 모르겠다!?

**그렇다면, 아래의 코드와 주석을 참고하자.**

``` python
# 전역 변수 var에 값 5 설정.
var = 5

def func(var):
    # 지역 변수 var에 값 10 설정.
    var = 10 # 1
    print('var is {}'.format(var))

func(var)
print('var is {}'.format(var))
```

> **result :** \
> var is 10 \
> var is 5

자, 코드를 살펴보면 전역 변수 var와 함수 func를 선언한 후 func에 전역변수(var)를 넘겨주는 모습을 볼 수 있다.

그러나 여기서 놀라운 점이 하나있다. \
 ~~물론, 전역/지역 변수에 대해 알고 온 분들은 그렇지 않겠지만..~~

바로 func함수 안에서 출력한 var만 값이 10으로 바뀌었고, 함수 실행이 끝난 후 var를 출력해보았을때에는 결과값이 5라는 점이다.

이게 바로 **전역 변수**와 **지역 변수**와의 차이점이다.

다시 코드를 보도록하자.

전역 변수 var를 함수 func에 var를 전달하고 함수안에서는 var를 10으로 다시 선언했다.

이때, var는 전역 변수가 아니라 함수 func의 지역 변수 var를 새로 선언했던 것일뿐이다.
전역 변수의 값을 참조하고 있는 상태가 아니었다는 것이다.

물론, #1의 줄을 제거한다면 func함수는 var라는 지역변수가 없으므로 전역 변수 var를 참조하여 5를 출력하게 된다.

## 객체 참조에 의한 전달(Call By Object Refrence)
**스택 프레임(stack frame)**
* 하노이의 탑을 아는가? *만약 모른다면 이 [링크](https://ko.wikipedia.org/wiki/%ED%95%98%EB%85%B8%EC%9D%B4%EC%9D%98_%ED%83%91)를 참고하자.* \
하노이 타워는 가장 위에 있는 원판을 가장 먼저 뺄 수가 있고 가장 먼저 들어간 원판은 가장 늦게 뺼 수 있다. \
 이 구조가 바로 스택이다.
* 프레임은 하노이의 탑에서 **원판**을 의미한다.

``` python
def func(var):
    # 지역 변수 var에 값 10 설정.
    var = 10
    print('var is {}'.format(var))

if __name__ == "__main__":
    var = 5
    func(var)
    print('var is {}'.format(var))
```

이 코드를 다시 한번 더 인용 하도록 하겠다. \
 그리고 아래 그림에서 전역을 global으로 부르겠다.
<pre>
┃┌─────────┐┃← func
┃│   var   │┃ →
┃└─────────┘┃  ↘    5
┃┌─────────┐┃   10↗
┃│   var   │┃ → ↗
┃└─────────┘┃← global
┗━━━━━━━┛
</pre>
바로 위 그림이 코드의 스택 프레임 모습이다.

함수가 호출될 때는 위 처럼 스택프레임이 생성이 됩니다.

global에서 var 변수를 생성하고 5의 값을 참조하고 있다.
반대로 func함수를 실행하고 스택프레임이 생성되면서 지역 변수 var는 10을 참조한다.

하지만 여기서 func의 값을 5로 다시 변경하면 어떻게 될까?
``` python
def func(var):
    # 지역 변수 var에 값 10 설정.
    var = 10
    print('var is {}'.format(var))
    var = 5 #1
    print('var is {}'.format(var))

if __name__ == "__main__":
    var = 5
    func(var)
    print('var is {}'.format(var))
```

위 코드에서 #1의 이전 까지는 위의 설명과 동일하다.
하지만 #1의 코드를 실행한 이후로는 스택 프레임이 아래와같이 변한다.

<pre>
┃┌─────────┐┃← func
┃│   var   │┃ → → ↘
┃└─────────┘┃       5
┃┌─────────┐┃  10 ↗
┃│   var   │┃ → ↗
┃└─────────┘┃← global
┗━━━━━━━┛
</pre>

상수 객체 10을 가리키는 변수가 없어졌다.
이때 상수 객체 10의 **레퍼런스 카운트**는 0이 되면서 메모리에서 사라집니다.

갑자기 10을 '상수 객체'라고 칭해서 놀란 분들이 있으리라 생각합니다.
> 이것은 파이썬의 특징인데, 파이썬에서는 **변수**라는 메모리 공간에  값을 직접 저장하는 것이 아니라 변수 이름이 **값 객체**를 가리킵니다.

다시 스택 프레임 그림으로 돌아가서 설명을 이어서하면.
func 함수에서 지역 변수 var를 5로 다시 설정한 후 출력하면 결과는 당연히 'var is 5'가 나올 것이다.
 이렇게 func함수가 종료가 되면 메모리에서는 이 함수를 게속 가지고 있을 이유가 없으니 스택 프레임에서 사라집니다.

<pre>
                     5
┃┌─────────┐┃     ↗
┃│   var   │┃ → ↗
┃└─────────┘┃← global
┗━━━━━━━┛
</pre>
이처럼 '객체 참조에 의한 전달'은 값 객체와 같은 객체를 변수 이름이 참조를 하면서 함수에 값을 전달하며 코드가 동작하는 모습을 의미합니다.

### 람다(lambda) 함수

람다 함수는 이름이 없는 함수를 의미한다. \
그래서 함수의 특징 중 하나인 재사용을 람다 함수는 이용할 수 없다. \
하지만 이를 변수에 할당한다면 일반 함수처럼 이용할 수 있다.

간단한 예제 하나만 보고 함수편을 마치도록 하자.

``` python
v = lambda x: x ** 2

v(3)
v(4)
```
> 9 \
  16

위와 같이 받은 값의 제곱을 돌려주는 람다 함수는

``` python
def double(var):
    return var ** 2

double(3)
```

이 코드처럼 함수를 따로 정의하지 않아도 이용할 수 있다는 특징이 있다.

주의사항으로는 람다함수에서는 반드시 식이 들어가야한다.

> v = lambda x: x = 2

이 코드 처럼 할당문이 들어가면 에러가 나므로 주의하자.
