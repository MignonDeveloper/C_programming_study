# 3. Standard Input / Output


## 입출력(I/O)의 주체는?

- **입출력은 항상 Kernel Layer에서 이루어지는 것**으로, User Mode Software가 하는 것이 아니다. (HW → Kernel → Process)
즉, printf를 call하는 것은 User Layer에서 하는 것이 아니라 Kernel Layer에서 일이 일어나는 것.
- **File Interface**로 Read/Write를 할 때 각각의 Protocol을 미리 정해두고 함수를 만들어서 사용하는데,
해당 함수들은 Kernel 수준에서 입출력을 하도록 요구하는 함수이다.

<br>

## 입출력을 하는 Interface, FILE

**H/W** (Low Level) → 입출력 장치 연결 (키보드, 마우스, 모니터…)

**Kernel** → H/W 장치를 control하는 Driver가 있으며 이를 통해 정보를 주고 받는다

**User** (High Level)

- User 수준에서는 Kernel에 접근 불가능하지만, User와 Kernel 사이에는 하나의 Interface가 있는데 이 것이 **FILE SYSTEM**이다. 이를 통해 일정한 규칙(Protocol)을 가지고 정보를 입력하거나 출력한다
- **Write** : 이를 FILE Interface를 통해 Kernel에 전달하고 이를 H/W장치에 전달
- **Read** : Kernel에서 control하는 H/W장치에서 정보를 가져와서 File로 보내와서 읽는다

<br>

## Buffer

> Buffer는 메모리를 의미한다.

예를 들어 YouTube 동영상 Streaming Service에 비유하면 PC는 YouTube에 network를 통해 Request를 하고 YouTube는 그에 대한 Response로 동영상 정보(Data)를 제공한다. 그러면 Buffer에 해당 정보가 들어오고(=Buffering) 사용자는 동영상을 감상한다.

그런데 만약 잠시 네트워크에 문제가 생겨 영상 정보를 받아오는 것이 정지되면(=충격) buffer에 정보가 더 이상 안들어오게 되고 재생이 이미 들어온 부분까지 도달하게 된다면 play가 멈추게 된다. 이후 다시 네트워크가 잘 흐르게 되면 사용자는 문제 없이 영상을 볼 수 있다. <U>즉, Buffer란 일종의 메모리 완충기로써 충격을 완화하는 역할을 한다.</U>

File은 각각 고유한 2가지(Read & Write) I/O buffer를 가진다.

- `getchar()`와 같은 함수로 입력값을 받을 때, 사용자가 입력하는 즉시 바로 값을 받아오는 것이 아니라, 키보드에 값을 입력하면
이를 **File의 Read Buffer**에 전달하고, Read Buffer에서 문자 하나를 가져오는 방식으로 입력값을 받아온다.

- 반대로 `putchar()`와 같은 함수로 출력값을 보낼 때, **File의 Wirte Buffer**를 통해 Kernel로 전달하고 Console에 출력한다.

<br>

## Buffered I/O 함수의 종류

- **getchar()**
    - 한 글자만 buffer에서 가져오는 것
    - `int getchar(void);`
    - RETURN: 읽어들인 문자 int 값 / 파일 끝이나 오류가 발생한다면 EOF return
    - 주의 사항: 개행 문자도 buffer로 받아들이므로, getchar()를 하나 더 써서 buffer를 비우는데에 이용!

- **gets_s()**
    - 한 줄 ( *‘\n’ or EOF* 기준) 전체를 따오되, 정해진 size를 넘어서면 Error 발생
    - `char* gets_s( char* str, rsize_t Size );`
        - size parameter: `sizeof(szBuffer)`
        - `char* str`의 마지막에 자동으로 `\0`을 채운다.
    - RETURN: 성공적으로 읽어 들이면 str(문자 배열 주소 값)을 return 한다.
    - 기존의 gets는 심각한 보안 결함이 있다 이를 대체해서 **gets_s()**를 사용

- **scanf_s()**
    - 형식문자에 규칙에 맞게 Read Buffer에서 정보를 가져온다.
    - `int scanf_s(const char *format, arguments, size);`
        - size parameter: `sizeof(c)`
    - return = 성공적으로 읽어들인 인자의 개수
    - 주의사항: 개행문자도 read buffer에 입력되므로 공백이나, %*c로 처리해줘서 buffer에서 끄집어 내야한다.

- **putchar()**
    - 표준 출력(stdout)에 문자 하나를 출력한다.
    - `int putchar(int character);`
    - RETURN: 표준 출력에 쓰여진 문자 반환 / 오류가 발생한다면 EOF return

- **puts()**
    - 표준 출력에 문자열을 출력한다. (str이 가리키는 주소부터 종료 문자(‘\0’)에 도달할 때 까지 표준 출력에 복사)
    - `int puts(const char* str);`
    - RETURN: 성공적으로 쓰여지면 음이 아닌 값이 return 된다.
    - 주의사항: **마지막에 자체적으로 개행을 추가한다.**
    
- **printf()**
    - `int printf(const char *format [,argument...]);`
    - 자리수 맞춰서 깔끔하게 출력
        - **%d -**> `%5d`(5자리 맞추기), `%05d`(선행 zero), `%-5d`(공백 역순)
        - **%f**> `%10f`, `%.2f`, `%10.2f`, `%010.2f`, `%-10.2`