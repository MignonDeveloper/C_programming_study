# 11. Memory & Pointer (4) - 포인터의 배열


## 개발자가 잘 지켜야할 것

> **대상의 유효성 확인(검증)을 개발자 스스로**가 해줘야하는 책임이 있다.

**`int *pList = aList;`**

- 포인터 변수 pList에는 주소가 가리키는 메모리의 **자료형식**이 무엇인지에 대한 정보는 있지만, **대상의 개수**는 안 나와있다.
    - 즉, pList 포인터는 그냥 `int` 자료 하나를 가리킬 수도 있고 `int[3]의 자료형의 0번째 주소`를 가리키는 것일 수도 있다.
    - 따라서 개발자가 스스로 검증하는 과정을 꼭 해줘야 한다.

<br>

## 포인터 배열

> **배열의 구성요소가 포인터**인 것

**`int *apList[5];`**

- apList 배열의 각 요소는 int자료형을 가리키는 포인터
- 해석의 순서 : `int * (apList[5])`
    - **배열먼저 인식하고, 다음에 * 을 인식**한다.
- `int * (*papList) = apList;` 로 담을 수 있다.

**`char *apstrList[3] = {“Hello”, “World”, “String”};`**

해석

1. **3개의 요소를 가지는 배열**
2. **배열의 요소는 주소 상수를 저장**
3. 주소가 가리키는 **메모리가 가지는 데이터의 자료형은 char**
    - `apstrList[0]`에 저장되어 있는 주소를 따라가면, “Hello” 문자열의 첫번째 요소를 가리킨다.

<br>

## 포인터 배열 접근

**`char *apstrList[3] = {“Hello”, “World”, “String”};`**

- apstrList의 offset = 기준 = **배열의 요소의 offset** = `Char Pointer` 주소 상수 = 4byte
- apstrList[index]의 offset = 기준 = **Char Pointer의 offset** = `Char` 자료형 = 1byte

```cpp
printf("%s\n", apstrList[0]);		  	// Hello
printf("%s\n", apstrList[1]);		  	// World
printf("%s\n", apstrList[2]);	  		// String

printf("%s\n", apstrList[0] + 1);		// ello
printf("%s\n", apstrList[1] + 2);		// rld
printf("%s\n", apstrList[2] + 3);		// ing

printf("%c\n", apstrList[0][3]);		// l
printf("%c\n", apstrList[1][3]);		// l
printf("%c\n", apstrList[2][3]);		// i
```

<br>

## 다중 포인터

> 일반 포인터와 같다. 단순히 **포인터가 가리키는 것이 포인터 변수**일 뿐!

```cpp
int main(int argc, char* argv[]){
	char ch = ‘A’;
	char *pData = &ch;
	char* *ppData = &pData;
	char** *pppData = &ppData;

	printf(“%c\n”, ch);
	printf(“%c\n”, *pData);
	printf(“%c\n”, **ppData);
	printf(“%c\n”, ***pppData);

	return 0;
}
```

<br>

## 다차원 배열 포인터

다차원 배열은 "배열의 배열"을 의미한다. 예를들어, `char[4][12]`는 `char[12]` 자료형이 4개가 있는 것을 의미한다. 이를 포인터로 표현하는 방법은? → **`char (*pName)[12]`**

- **pName은 포인터 변수를 의미**하고, 해당 변수안에 있는 주소상수가 가리키는 메모리의 자료형은 char[12]를 의미한다.
- <u>단, 해당 자료형 char[12]가 몇개 있는지에 대한 정보는 모른다.</u> → 포인터 변수는 자료형이 무엇인지만 주어진다.

```cpp
#include <stdio.h>
int main(int argc, char* argv[]){
	char astrList[3][12] = {"Hello", "World", "String"};
	char (*pastrList)[12] = astrList;              	// paListUser의 offset = char[12]에 대한 포인터 변수 = 12byte

	puts(apstrList[0]);                             // Hello
	puts(apstrList[1]);                             // World
	puts(apstrList[2]);                             // String
	return 0;
}
```

<br>

## 변수와 메모리

**변수의 본질은 메모리** (=**주기억장치 메모리**)
- 이중에 우리가 사용할 수 있는 메모리는 **스택, 힙, 텍스트, 데이터 영역**의 메모리이다.
- 사실 변수를 선언할 때는 어떤 메모리를 쓸 것인지 명시해야 한다.
    - 아무런 언급이 없는 지역변수는 알아서 자동변수로 처리해서 스택에 저장
    - **메모리의 종류 = 기억부류** (=storage-class)
    - 따라서, 변수앞에 기억부류를 명시하는 예약어를 **기억부류 지정자** 라고 한다.
- C언어의 기억부류 지정자
    - `extern`, `auto`, `static`, `register`
    - 주의) auto의 의미가 달라졌으므로, auto는 앞으로도 쭉 쓰지 말 것!

<br>

## 정적변수 static

```cpp
void TestFunc(void){
	int nData = 10;
	printf("%d", nData++);
	return;
}

int main(void){
	TestFunc();
	TestFunc();
	TestFunc();
	return 0;
}
```

- 계속 nData++ 작업을 해줘도, 스코프가 열리고 닫힘에 따라 변수가 새로 선언되고 사라지므로 10,10,10이 출력된다.
**`static int nData = 10;`** 로 쓰면 10,11,12로 나온다.
    - 정적변수의 역할을 한다.
    - 물론 함수 밖에 선언하면 전역변수로도 같은 기능을 구현할 수 있다.
- **전역변수나, 정적변수는 모두 동시성(concurrency)를 지원하기 어렵다는 문제**가 있다.
    - Multi-thread를 넘어서 물리적인 CPU 코어의 개수가 여러 개인 지금, 동시성은 병렬처리(parallel processing)와 직결되고
    병렬처리는 다시 성능과 직결된다. 따라서 **정적변수, 전역변수를 사용하는 일은 신중히 결정**

<br>

## 레지스터 변수 Register

> 레지스터 변수는 일반 메모리가 아닌 **CPU의 레지스터를 사용**하기 위한 것, 과거 주기억장치의 속도가 비교적 느릴 시절에 사용.

지금은 레지스터 변수를 다루는 일이 PC에서는 큰 의미가 없어짐.
- 단, Register 변수는 **주소가 아닌 고유이름으로 식별**한다는 것만 주의!