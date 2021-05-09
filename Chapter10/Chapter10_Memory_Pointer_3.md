# 10. Memory & Pointer (3) - Memory Copy

## 여러 instance의 복사
단순 대입 연산자의 피연산자가 변수(l-value)일 경우, 그 인스턴스의 개수가 1개라고 전제하여 대입이 이루어집니다. 따라서 만일 배열처럼 여러 인스턴스가 뭉쳐진 경우엔 절대로 단순 대입(=)으로 r-value를 l-value로 복사할 수가 없습니다.

- **`void * memcpy(void *dest, const void *src, size_t count)`**
    - `dest`: 대상 메모리 주소

      `src`: 복사할 원본 데이터가 저장된 메모리 주소

      `count`: 복사할 메모리의 바이트 단위 크기

    - 반환값: 대상 메모리 주소(dest)
- **`char * strcpy(char *dest, const char *src)`** → 보안결함이 있으므로 `strcpy_s`로 대체
    - `dest`: 문자열이 복사될 곳을 가리키는 포인터

        `src`:  복사할 문자열을 보관하고 있는 포인터

    - 반환값: 대상 메모리 주소(dest)
- **`char * strncpy(char *dest, const char *src, size_t num)`**
    - `dest`: 문자열이 복사될 곳을 가리키는 포인터

        `src`:  복사할 문자열을 보관하고 있는 포인터

        `num`: src에서 복사할 문자의 개수

    - 반환값: 대상 메모리 주소(dest)
- **`char * strcpy_s(char *dest, rsize_t dest_size, const char *src)`  * 순서 주의**
    - `dest`: 문자열이 복사될 곳을 가리키는 포인터

        `dest_size`: dest가 갖는 메모리의 크기 

        `src`:  복사할 문자열을 보관하고 있는 포인터

    - 반환값: 대상 메모리 주소(dest)

<br>

## Deep & Shallow Copy

(1) **Deep Copy**: 메모리의 내용을 복사

```cpp
memcpy(szDstBuf, szScrBuf, sizeof(szDstBuf));
puts(szDstBuf);

// ---------------------------------

char const *pszData = malloc(sizeof(char) * 12);
memcpy(pszData, szSrcBuf, sizeof(char)*12);
free(pszData);
```

- `const`를 쓰는 이유는 `pszData`를 상수화 시켜서 다른 주소값을 대입시키지 못하게 고정시키는 역할 (`malloc`시 꼭 사용)

(2) **Shallow Copy**: 메모리의 주소만 복사

```cpp
char *pszData = NULL;
pszData = szSrcBuf;
```

C++에서는 Shallow Copy를 사용할 때, `int &pszData = szSrcBuf;`

1. 초기값을 지정해주고(R-Value)
2. pszData를 상수화시켜 pszData 자체의 값을 바꿀 수 없게 만든다.

      → 물론 pszData의 주소가 가리키는 값이 바뀌는 것은 가능.

<br>

## 여러 instance의 비교

- **`int memcmp(const void *buf1, const void *buf2, size_t count)`**
    - `buf1`: 비교 원본 메모리 주소

        `buf2`: 비교 대상 메모리 주소

        `count`: 비교할 메모리의 바이트 단위 크기

    - 반환 값 = 결과가 0이면 같다 / 0보다 크면 buf1 > buf2 / 0보다 작으면 반대
    - 첫 번째 인자로 전달된 주소의 메모리에 저장된 값에서, 두 번째 인자로 전달된 주소의 메모리에 저장된 값을 빼서 두 값이 같은지 비교한다. 주어진 길이만큼 두 메모리를 비교하는 함수
- **`int strcmp(const char *string1, const char *string2)`**
    - `string1`: 비교할 문자열이 저장된 메모리 주소

        `string2`: 비교할 문자열이 저장된 메모리 주소

    - 반환 값 = 문자열이 같다면 0 반환 / string1 > string2 면 0보다 큰 수 / 반대면 0보다 작은 수
    - 대소문자를 식별하여 두 문자열이 같은지 비교하는 함수
- **`int strncmp(const char *string1, const char *string2, size_t num)`**
    - `string1`: 비교할 문자열이 저장된 메모리 주소

        `string2`: 비교할 문자열이 저장된 메모리 주소

        `num`: 처음부터 비교할 문자의 최대 개수

    - 반환 값 = 문자열이 같다면 0 반환 / string1 > string2 면 0보다 큰 수 / 반대면 0보다 작은 수
    - 대소문자를 식별하여 두 문자열이 같은지 비교하는 함수

<br>

## 문자열을 위한 함수

- **`char *strstr(const char *string, const char *strCharSet)`**
    - `string`: 검색 대상이 될 문자열이 저장된 메모리 주소

        `strCharSet`: 검색할 문자열이 저장된 메모리 주소

    - 반환값 = 문자열을 찾으면 해당 문자열이 저장된 메모리 주소 반환 / 찾지 못하면 NULL
    - 임의의 대상 문자열에서 특정 문자열을 검색하는 함수

    ```cpp
    char szBuffer[12] = {“I am a boy.”};
    char *pszFound = strstr( szBuffer, “am” );
    int nIndex = pszFound - szBuffer;            // 몇 번째 index에 있는지 알아낼 수 있다.
    ```

- **`size_t strlen(const char *str)`**
    - `str`: 길이를 알고싶은 문자열의 메모리 주소
    - 반환값 = 문자열의 길이
        - `sizeof()` 연산자는 메모리의 크기를 반환 하는 것 → 차이에 주의

<br>

## realloc(), sprintf_s()

- **`void *realloc(void *memblock, size_t size)`**
    - `memblock`: 기존에 동적 할당된 메모리 주소. 만일 이 주소가 NULL이면 malloc()과 같은 역할

        `size`: 다시 할당 받을 메모리의 바이트 단위 크기 → 항상 `sizeof`를 이용

    - 반환 값 = 다시 할당된 메모리 덩어리 중 첫 번째 바이트 메모리 주소
    - 만일 다시 할당하는데 실패하면 NULL 반환 → ***Null인 경우에는 기존 동적 할당된 메모리 주소를 free해줘야 한다!*** (try catch를 통해서 추가하는 것이 중요)
    - 만일 이미 할당된 메모리 영역에서 크기를 조정할 수 있다면, 반환된 주소는 첫 번째 인자로 전달된 주소와 같다. 불가능하다면 기존의 메모리를 해제하고 새로운 영역에 다시 할당한 후, 새로 할당된 메모리의 주소를 반환한다.

- **`int sprintf(char *buffer, const char *format [, argument]…)`** → 보안결함이 있으므로 `sprintf_s`로 대체**
    - `buffer`: 출력 문자열이 저장될 메모리 주소

        `format`: 형식 문자열이 저장될 메모리 주소

        `[, argument]`: 형식 문자열에 대응하는 가변 인자들

    - 반환 값 = 출력된 문자열의 개수
    - 형식 문자열에 맞추어 특정 메모리에 문자열을 저장하는 함수
    printf()와 거의 흡사한데, 문자열이 콘솔 화면에 출력되는 것이 아니라 특정 주소의 메모리에 출력된다!
- **`int sprintf_s(char *buffer, size_t size, const char *format [, argument]…)`  순서 주의**
    - `buffer`: 출력 문자열이 저장될 메모리 주소

        `size`: buffer의 메모리 크기

        `format`: 형식 문자열이 저장될 메모리 주소

        `[, argument]`: 형식 문자열에 대응하는 가변 인자들

    - 반환 값 = 출력된 문자열의 개수
    - 형식 문자열에 맞추어 특정 메모리에 문자열을 저장하는 함수
    printf()와 거의 흡사한데, 문자열이 콘솔 화면에 출력되는 것이 아니라 특정 주소의 메모리에 출력된다!