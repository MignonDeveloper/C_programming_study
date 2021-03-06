# 6. Array

## 배열(Array)

>형식이 같은 자료 여러 개가 모여 새로운 하나를 이룬 형식

1. **배열의 이름은 첫 번째 요소의 메모리 주소 상수**

    ```cpp
    int aList[5];
    int *paList = aList;
    ```

    - 자료형은 `int[5]`이고 aList는 aList[0]을 가리키는 주소상수

2. **index는 배열 요소의 기준 자료형에 따라서 기준 주소에 값을 더해주면서 접근**
    - 첫 번째 원소를 기준으로 나머지 요소가 상대적으로 얼마나 떨어져있는지를 표현하는 숫자를 나타낸다. (요소의 offset)
    - 해당 index에 접근하는 2가지 방법

        1) `aList[0]`, `aList[1]`, `aList[2]`
        2) `aList + 0`, `aList + 1`, `aList + 2` : 기준주소 + Index(**기준 주소 자료형의 size크기 기준**)를 하면 **상대주소**를 표현한다.

<br>

## 문자의 배열

> 문자열의 실체 = 요소가 Char 자료형을 가지는 배열 (단, 문자열의 끝은 `\0`으로 저장)

`“Hello”`의 본질

- 해당 문자열의 자료형은 `char[6]` → NULL 문자가 문자열의 끝에 있기 때문에
- 문자열 상수는 이름을 부여하지 않으므로, **문자열 상수 자체는 (첫 번재 요소를 가리키는) 메모리 주소로 식별**한다.
- 따라서, `char *szPtr = “Hello”;` 표기 가능
    - 즉, **szPtr의 주소를 따라가보면 나오는 것은 char 형식으로 저장되어 있으니, 이 형식에 맞춰서 읽을 것**!
    - szPtr 주소에 담긴 값은 **“Hello”에서 ‘H’가 담겨있는 메모리의 주소**

<br>

## 다차원 배열

> 배열의 배열

`char[3][4]`

- char[4]
- char[4]
- char[4]

즉, char[0] & char[1] & char[2]는 각각 **(1) char[4]의 자료형을 가지고**, **(2) 각각은 char[4]가 시작되는 주소를 가리키는 주소상수**다.

한 가지 주의 사항
- `char aData[3][4]`를 3행 4열 2차원으로 표현하는 것은 **논리적인 구조**를 위해서이고, **실제 메모리는 1차원 구조가 이어지는 것으로 표현**된다.
- 따라서 만약 `aData[0][4]`를 하게 되면, `aData[1][0]`에 접근을 하게 되는 것이므로, 이와 같은 논리적인 오류는 주의할 것

<br>

## 배열을 활용한프로그래밍 기법

1. **버블 정렬**
    > 버블처럼 2개의 요소를 계속 비교해나가며 자리를 확정

    - ex) 1 4 3 5 2 를 오름차순으로 정렬
        - 1,4 비교 → 1이 더 작다 = 그대로
        - 4,3 비교 → 4가 더 크다 = 4와 3의 위치를 바꾼다. → 1 3 4 5 2
        - 4,5 비교 → 4가 더 작다 = 그대로
        - 5,2 비교 → 5가 더 크다 = 5와 2의 위치를 바꾼다. → 1 3 4 2 5
            - 이 과정을 통해서 5의 자리가 확정. 제일 큰 숫자를 제일 오른쪽으로 옮김
        - 다시 1,3 비교를 하는 과정을 거치면서 4번째 자리의 숫자를 결정!

    **비교를 할 때마다 매번 자리를 교환하는 과정**이 있으면 버블 정렬


2. **선택 정렬**
    > 기준과 선택을 통해서 자리를 확정

    - ex) 5 1 4 3 2
        - 첫 번째 자리를 기준으로 잡고, 기준 포함 뒤의 숫자들 중에서 가장 작은 숫자를 선택하고, 
        가장 작은 숫자의 위치를 선택한다음, 그 위치와 첫 번째 자리의 요소를 교환한다.
        - 1 5 4 3 2
        - 1 2 4 3 5
        - 1 2 3 4 5
        - 1 2 3 4 5

    **비교를 계속 하면서 index를 갱신하면서 마지막에 자리교환**을 하면 선택정렬

3. **배열과 교차의 구현**

    - 배열을 이용할 때 **Flag**를 세워서, 특정 조건에 따라서 다른 작업을 수행하도록 만들 수 있다

4. **Lookup 배열**

    - 고성능의 프로그램에서 선택의 개념을 구현하는데 매우 중요!
    - **input에 해당하는(상응하는) index에 위치하는 배열의 요소에 필요한 값을 저장**해두면, 바로바로 가져올 수 있다. O(1)