# 8. Memory & Pointer (1) - 포인터, 지정, 배열

## Basic

C언어는 메모리를 직접 다루기 때문에, 운영체제나 하드웨어를 직접 제어하기 위한 도구로 유용하다.

- **Memory Cell은 1byte**로, 영문 한 글자를 담을 수 있는 크기(8bit).
- 각 Cell에는 일련번호가 붙어있고 이를 주소라고 부른다. **주소는 메모리의 위치**를 의미한다.

<br>

## 직접지정 = 변수 선언

- 변수를 선언하면 메모리가 확보되는데, 이 메모리의 정확한 위치는 Compiler만 알고있고, 컴파일 할 때 마다 바뀜으로 정확하게 알기 힘들다는 특징을 가진다.
- `int nData = 300;`
    - Cell(주소 → 0x0043FC44) 부터 총 4개의 Cell 메모리가 할당되는데, 내가 지정하는 것이 아니라 컴퓨터가 4개의 Cell을 지정해서 int로 사용
    - 300 = 0x12C = 0x00/00/01/2C 이지만, 메모리에 저장될 때는 순서가 뒤바껴서 2C/01/00/00 이런 Byte Order = **Little Endian** (Intel의 x86)
    - 반대로 큰 순서 Byte Order로 메모리에 저장하는 방법 = **Big Endian**

**`var`** : **단항 주소 연산자**
 - Compile time 연산자라서 Compile 할 때 한 번만 연산하고 끝!

**`%p`** : **포인터**(주소)를 의미하는 자료형
 - **포인터 변수**: 메모리의 주소 값을 저장하기 위한 전용 변수

<br>

## 간접지정 = 포인터 변수화

```cpp
int nData = 300;
int *pnData = &nData;
```

- **`&nData`** = nData의 주소 = 0x0018FF28
  - 해당 메모리는 4byte(**int형**)를 할당 받고, int 자료형으로 지정되며 4byte의 각각의 Cell에 [2C/01/00/00 (300)]의 값이 들어간다.

- **`&pnData`** = pnData의 주소 = 0x0018FF1C
  - 해당 메모리는 4byte(**포인터형**)을 할당 받고(만약 64bit 주소체계라면 8byte를 차지하겠죠?), pointer 자료형으로 지정되며, 4byte의 각각의 Cell에 [28/FF/18/00 (&nData)]의 값이 들어간다.

**`int *`의 의미**
- `*` : 저장된 데이터를 **메모리의 주소로 인식**하겠다.
- `int` : 메모리 주소에 저장된 **데이터의 자료형을 int로 간주**
- `int *pnData = &nData;`
    - `pnData`에는 메모리 **주소상수 값**이 저장되는데, 해당 주소가 pointing하는 대상의 자료형은 `int`이다.

```cpp
pnData += 2
*pnData = 600
```

- `pnData += 2`: int에 대한 pointer이므로 **기준 자료형의 크기 +2**를 하면 기존 메모리 주소에서 `4(byte) * 2 = 8`이 더해진 주소를 가리킨다.
- `*`: **간접지정연산자**, <u>포인터 변수에 저장된 주소가 가리키는 메모리를 특정 자료형의 변수로 취급한다.</u>
    - `*pnData = 600`: pnData는 int pointer이므로 int형 변수로 간접지정한뒤, 해당 메모리에 600을 넣는다.

<br>

## 함수를 Pointer로 받기

```cpp
#include <stdio.h>
// void (*)(int) → return자료형-(*name)-(parameter)
void TestFunc(int nParam){
	printf(“TestFunc() : %d\n”, nParam);
}

int main(void){
	TestFunc(10);                          // High-Level
	((void(*)(int))0x004113C))(10);        // Low-Level
	return 0;
}
```

```cpp
#include <stdio.h>
void tFunc(int nParam){
	printf("%d\n",nParam);
	return;
}

int main(void){
	void (*func)(int) = tFunc;  // void(int) 자료형을 가리키는 pointer
	func(7);
	return 0;
}
```

<br>

## Parameter에 배열 전달

```cpp
#include <stdio.h>
void tFunc(int *paList){          // int *paList = aList; 
	for(int i=0;i<5;i++){           // paList에는 배열의 메모리 주소를 담고, 해당주소의 메모리는 int 자료형을 의미
		printf(“%d\t”, paList[i]);    // [index] 쓰는 것은 배열,포인터,벡터
	}
	putchar(‘\n’);
	return;
}

int main(void){
	int aList[5] = {40,20,50,30,10};
	TestFunc(aList);
	return 0;
}
```

<br>

### 포인터와 배열

**배열의 이름**은 주소상수이고 해당 주소는 배열 0번 요소의 주소를 기리킨다. **포인터 변수**는 주소를 저장하기 위한 변수이다.

- **배열의 이름은 주소이므로 포인터 변수에 저장할 수 있다.**
- **`int aList[3];`**
    - aList 식별자 자체는 주소 상수 → 배열은 주소로 식별한다.
    - 해당 배열의 자료형은 int[3] → 배열 각각 요소의 형식은 `int`
    - `int *paList = aList;`으로 배열의 주소를 포인터 변수에 저장할 수 있다.

		```cpp
		int aList[5] = {40,20,50,30,10};
		int *paList = aList;

		paList + 1
		*(paList + 1) == paList[1]
		```

- `paList + 1` : (+1)의 의미는 offset = 기준주소 + 포인터 변수가 가리키는 주소의 자료형식 만큼의 offset으로 상대적으로 표현한다.
- `*(paList + 1)`: paList + 1의 주소를 간접지정연산자를 통해 해당 포인터 변수가 가리키는 주소의 자료형식의 변수로 간접 지정 → `paList[1]`과 같은 역할
	```cpp
	char szBuffer[12] ={ “Hello” };
	char *pszData = szBuffer;
	int nLength = 0;

	while( *pszData != ‘\0’ ){
		pszData++;
		nLength++;
	}
	```