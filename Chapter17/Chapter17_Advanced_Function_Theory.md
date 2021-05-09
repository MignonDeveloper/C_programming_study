# 17. Advanced Function Theory

## 최적화
함수형식 = 반환형식 + [호출규칙] + 이름 + (매개변수 목록)
- 함수의 이름은 배열처럼 **주소상수**다.
- 함수는 **R-X**가 가능한 메모리에 저장 → 함수의 연산과정을 함부로 바꾸는 것을 방지

**성능향상과 함수**
- 함수를 호출하려면 여러가지 비용이 든다.(Overhead의 발생)
- 예전에는 간단한 함수를 `macro`로 처리했는데, 매개변수의 자료형을 가지고 있지 않다는 점에서 단점이 있다.
- 매크로의 단점을 보완한 `__inline` 함수 도입 → Release Mode로 하면 Compiler가 자체적으로 알아서 만들어준다.

<br>

## 함수 호출 규칙 (Calling Convention)
> 호출자 함수가 피호출자 함수를 호출하는 과정에서, **매개변수를 전달하는 순서** 및 **매개변수가 사용한 메모리 관리방법** 등에 관한 규칙이다.

```cpp
int __cdecl GetMax(int a, int b, int c){   
	// Call stack에 c,b,a 순서대로 push가 되어있다.
	int nMax = a;
	if(b>nMax) nMax = b;
	if(c>nMax) nMax = c;
	return nMax;
}

int main(void){
	int nResult = 10;
	GetMax(1,2,3);
	return 0;
}
```

**`__cdecl`** 경우에는 Caller 인 main( )함수가 Stack Memory상에 들어가 있는 **매개변수**를 정리

- **Stack의 Top pointer를 함수호출 전으로 내려버린다**
    - stack에서 pop과 같은 효과
- 이는 **ESP(Extension Stack Pointer) Register**에서 처리한다.
    - ESP 주소값을 늘려서 스택의 크기를 줄인다

이러한 처리를 할 때

- `__cdecl` 은 Caller 쪽에서 이러한 작업을 처리
- `__stdcall`은 Callee 쪽에서 이러한 작업을 처리
- `__fastcall`은 레지스터도 활용해서 매개변수를 저장을 도움

<br>

## Function Pointer
> 함수의 이름도 배열의 이름처럼 ‘주소상수'에 부여한 식별자이다. 따라서 함수의 이름을 포인터변수에 저장할 수 있다.

- 변수의 자료형이 함수 호출에 필요한 정보(**매개변수, 호출 규칙, 반환 자료형**)를 포함해야 변수를 이용해서 함수를 호출할 수 있다.
    - `반환자료형 (호출 규칙 * 변수이름) (매개변수)`

<br>

## Call Back 구조
함수 포인터가 필요한 2가지 Case
- `동적 연결 라이브러리`(DLL, Dynamic Linking Library)
- `역호출(Call Back)` 구조를 구현하는 경우
    - 내가 능동적으로 호출하는 것이 아니라, 다른 무엇에 의해 필요해지는 경우 호출된다!

**Quick Sort**

`void qsort (void * base, size_t num, size_t width, int (__cdecl *compare)(const void *, const void *))`

- **`base`** = 정렬대상 배열의 기준주소

    **`num`** = 배열요소의 개수

    **`width`** = 배열요소의 바이트 단위 크기

    **`compare`** = 각 요소를 비교하여 같을 경우 0, 그렇지 않을 경우 양수 혹은 음수를 반환하는 함수의 주소

- **반환값** = 없음
- 퀵 정렬 알고리즘을 이용하여 배열에 담긴 요소를 정리하는 함수이다.

```cpp
int MyCmp(const void *left, const void *right){
	return *(int*)(left) - *(int*)(right);
}

// 내가 만든 함수인 MyCmp를 내가 호출하는게 아니라 qsort() 내부에서 호출시킨다!

int main(void){
	int aList[5] = {20, 50, 10, 30, 40};
	qsort(aList, 5, sizeof(aList[0]), MyCmp);
	for(int i=0; i<5; i++){
		printf(“%d “, aList[i]);
	}
	return 0;
}
```

**CallBack** = 함수 주소를 등록한다는 개념 = Called by FrameWork
- 주소를 등록받은 쪽(Caller 함수)에서 함수의 주소를 알기 때문에, 등록한 함수를 Call 한다.
- 내가 선언 및 정의를 하되 내가 호출을 하지는 않고 등록받은 쪽에서 필요하면 함수를 호출

**CallBack 구조를 이용하는 이유**
- User Level에서 코드를 짜고 함수를 만들었는데, 해당 함수를 Kernel Level에 등록시켜주면, 나머지 코드는 User Level에서 돌아가는데 등록한 함수는 Kernel이 원할 때 알아서 호출하므로, 비동기적인 구조를 만들 수 있다!
- 각 자료형식에 따라서 연산하는 방법이 다를 수 있으므로, 이러한 문제를 함수 등록을 통해서 해결할 수 있다.