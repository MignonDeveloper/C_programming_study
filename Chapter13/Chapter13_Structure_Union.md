# 13. Structure & Union

## Basic

- **자료형:** 일정길이의 메모리에 저장된 정보를 해석하는 방법
- **배열:** 동일형식(자료형)의 연속(연접)된 집합체
- **사용자 정의 자료형**: 구조체, 공용체
    1. **구조체(Structure): 서로 같거나 다른 형식들의 연속된 집합체**

        → **다양한 기본 자료형(built-in data type)을 가진 요소들을 모아 새로운 자료형으로 만든 것**

        → 구조체의 선언은 선언에 불과하고, 정의된 것이 아니다!

    2. **공용체(Union): 한 형식에 여러 해석방법을 부여**하는 문법으로 같은 크기를 여러가지 형식으로 해석

<br>

## 구조체의 선언 및 정의
### (1) 기본적인 방법
```cpp
struct USERDATA{
	int age;
	char szName[32];
	char szPhone[32];
};

int main(void){
	struct USERDATA user = {0, "", ""};    // 멤버들의 순서에 맞춰 초기값을 기술
	user.nAge = 10;                        // 구조체 멤버접근 연산자 = 이름.멤버명
	strcpy_s(user.szName, sizeof(user.szName), "Hoon");
	strcpy_s(user.szPhone, sizeof(user.szPhone), "010-1234-1234");
	printf("%d살, %s, %s", user.nAge, user.szName, user.szPhone);
	return 0;
}
```

### (2) typedef를 이용한 형 재선언
```cpp
typedef struct USERDATA{
	… 
} USERDATA ;

int main(void){
	USERDATA user1={0,"hello","010-1122-3344"};   // typedef를 통해 struct 예약어를 생략할 수 있다.
	return 0;
}
```

### (3) 구조체 배열
```cpp
typedef struct USERDATA{
		int nAge;
		char szName[32];
		char szPhone[32];
} USERDATA;

int main(void){
		USERDATA userList[4]={
			{10, "일민용", "1234"},
			{20, "이민용", "1234"},
			{30, "삼민용", "1234"},
			{40, "사민용", "1234"}
		};

		for(int i = 0; i<4; i++)
			printf("%d살, %s, %s", userList[i].nAge, userList[i].szName, userList[i].szPhone);
		return 0;
}
```

### (4) 구조체 동적 할당
```cpp
typedef struct USERDATA{
	int nAge;
	char szName[32];
	char szPhone[32];
} USERDATA;

int main(void){
	USERDATA const *pUser = malloc(sizeof(USERDATA));
	pUser -> nAge = 10;                      // 포인터이므로 .이 아니라  -> 연산자로 멤버에 접근한다
											// pUser->nAge 를 다르게 표기하면 (*pUser).nAge
	strcpy(pUser -> szName, "Hoon"); 
	strcpy(pUser -> szPhone, "9876");
	printf("%d살 %s %s", pUser->nAge, pUser->szName, pUser->szPhone);
	return 0;
}
```

<br>

## 매개변수, 반환자료 구조체

> 구조체를 매개변수로 넘길 때는 반드시 주소로 넘길 것 (Call by Reference)

구조체도 함수의 반환 자료형이나 매개변수가 될 수 있다. 또한 구조체 변수는 배열의 이름(주소상수)과 달리 `l-value`가 될 수 있어서 구조체 변수에 대해 단순 대입이 가능하다. 만일 함수가 구조체를 반환한다면 이를 `r-value`로 사용해서 대입연산을 할 수도 있다.

<br>

## 구조체 멤버 맞춤

**Structure Member Alignment**

```cpp
typedef struct USERDATA{
	char ch;
	int nData;
} USERDATA;

int main(void){
	USERDATA user;
	user.ch = ‘A’;
	user.nData = 300;
	return 0;
}
```

user의 메모리를 뜯어보면, 아래와 같이 들어가 있다.

- 41 / cc / cc / cc
- 2c / 01 / 00 / 00

`sizeof(USERDATA)`는 `8`이다. VS가 기본적으로 4byte 단위로 끊는다.

```cpp
// 멤버의 간격을 1byte로 맞춘다
#pragma pack(push, 1)

typedef struct USERDATA{
	char ch;
	int nAge;
} USERDATA;
#pragma pack(pop)
```

- `#pragma pack` 전처리기를 이용하면 특정 구조체만 멤버를 1바이트로 맞출 수 있다

<br>

## Union

> 한 가지 자료에 대해 여러가지 해석방법(자료형)을 부여

예를 들어 4byte 길이의 메모리는
- `int` / `char[4]` / `short[2]`
- 길이가 4byte인 다른자료형을 가지는 메모리로도 해석할 수 있다.

네트워크(TCP/IP socket programing) 프로그래밍에 자주 나온다.

```cpp
typedef union IP_ADDR {
	int nAddress;	               	// 해석 1
	short awData[2];	          	// 해석 2
	unsigned char addr[4];	        // 해석 3
} IP_ADDR;

int main(void){
	IP_ADDR addr;
	addr.addr[0] = 192;
	addr.addr[1] = 168;
	addr.addr[2] = 0;
	addr.addr[3] = 10;
	printf(“%d\n”, addr.nAddress);
	return 0;
}
```