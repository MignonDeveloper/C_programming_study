# 16. Preprocessor

## 전처리기 = (preprocessor)

> 전처리기(preprocessor)는 **컴파일러가 소스코드를 컴파일하기에 앞서 선행처리하는 구문**

- 모두 `#`기호가 앞에 prefix로 붙어 있다는 외형상 특징이 있다.
- 경우에 따라서는 일부 코드를 생략(`조건부 컴파일`)하거나
- 반대로 다른 코드를 합쳐(`#include`)서 컴파일을 진행하는 등 여러가지 기능이 있다.

<br>

## #include

외부 파일(보통은 `헤더파일`)을 현재 소스코드의 위쪽에 포함시켜 함께 컴파일하도록 설정

- 내가 직접 코드를 작성해서 만든 소스코드와 차이가 전혀 없는 형태!
    - `#include <stdio.h>` : 컴파일러 설정 → 시스템 디렉토리에서 찾는다.
    - `#include "header.h"` : 현재 디렉토리에서 찾는다. project 파일이 있는 위치
- `#pragma once`: 해당 전처리기를 통해서 여러 header를 include하면서 서로 중복되는 것을 방지해준다.

<br>

## 매크로= (macro)
> 외형상으로 함수의 모습을 하고 있으나, 실제로는 함수가 아니라 한 행 혹은 여러 행으로 기술할 수 있는 구문(or 항)이다.

프로그램 코드 중 불연속적으로 반복되는 것을 함수로 만들어 유지보수를 하는데 좋다. 하지만 자주 호출되는 함수인데, 길이가 짧다면 함수로 만드는 것이 잘한 것인지 다시 생각해봐야 한다. → **Overhead**
- 스택 프레임에 매개변수 메모리도 지정해야 하고
- 인수도 복사해야 하며
- 제어의 흐름도 변경해야 한다.

즉, 함수로서 수행할 코드는 얼마 없는데 함수 호출로 인한 Overhead가 더 커질수도 있다!
- **Overhead** : 함수가 함수를 Call하면 스택에 값 push, 매개변수 값 복사, 반환 값을 돌려주는 등등의 과정이 기본적으로 따라오는데 이러한 것들을 일컫는 말
- 함수를 만드는 것에는 분명한 이유가 존재하고 필요하지만 코드의 내용이 매우 간단한데 자주 반복되는 거면 함수로 만들기 부담스럽다.
- 따라서 이러한 것을 방지하기 위해서 `Macro` → `__inline`함수를 탄생시켰다
    - 매크로의 여러 단점을 보완한 것이 `__inline함수`로 어쩔 수 없는 경우를 제외하고는 무조건 __inline함수를 사용한다.

```cpp
#define ADD(a, b)	(a+b)

int main(void){
	printf(“%d\n”, ADD(3,4));
	// 위 아래의 코드는 똑같다.
    //대신 함수가 아니라서 overhead가 발생하지 않는다!
	printf(“%d\n”, (3+4));
	return 0;
}
```

- 괄호를 잘 매겨줘야 한다. 연산자 우선 순위의 문제가 발생하기 쉽다.
- **Macro**는 오로지 단순 치환만 해준다는 것을 명심!

<br>

## __inline 함수

```cpp
int Add(int a, int b){
	return a+b;
}

int main(void){
	int nInput;
	scanf_s(“%d”, &nInput, sizeof(nInput));
	printf(“%d”, Add(nInput,4));
	// 굳이 Add 함수를 호출하지 않고  nInput + 4의 계산을 하게 된다
	// 최적화 설정에 inline 함수 확장을 설정 해뒀기 때문에
	// 그래서 __inline 함수를 쓰지 않아도 최적화를 하면서 컴파일러가 자동으로 처리한다.

	return 0;
}
```

<br>

## 매크로 특수화 연산자
>오로지 매크로 내부에서만 사용할 수 있는 연산자
- `#`은 인수가 무엇이든 간에 **모두 ‘문자열'로 만들어주는 연산자**이고
- `##`은 두 매개변수를 한 덩어리로 묶어 **‘high level code’를 만들어주는 연산자**( 토큰을 받아서 하는 것 → 실제 코드를 대상으로 한다)

```cpp
#define STRING(a) #a
#define PASTER(a, b) a##b

int main(void){
	int nData = 10;
	printf("%d\n", PASTER(nDa, ta));   
  //PASTER(nDa,ta)는 그냥 nData라고 쓴 것과 같다

	printf("%d\n", nData);
	printf("%s\n", STRING(nData));
	return 0;
}
```

<br>

## 조건부 컴파일
>조건부 컴파일은 상수 정의에 따라 실제로 번역되는 소스코드가 달라지도록 구성하는 것

- `#if XXX, #elif XXX`
- `#ifdef, #ifndef`
- `#else`
- `#endif`

```cpp
#define _TEST_   // Switching 기능을 만들 수 있다

int main(void){
// #ifdef : 매크로가 정의되어 있는지 확인
#ifdef _TEST_     
	puts(“_TEST_”);
#else
	puts(“_NO_”);
#endif
	return 0;
}
```

```cpp
#define _DEBUG  // 모드설정에 맞춰 다르게 작동하기를 원할 때

int main(void){

#ifdef _DEBUG
	puts(“Debug Mode”);
#else
	puts(“Release Mode”);
#endif
	return 0;
}
```