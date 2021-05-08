# 1. Introduction to C Programming

## C Language Breif History

C언어(1971)를 통해 UNIX 개발(1969)?

- 원래 B언어를 통해 UNIX 개발을 시작했는데 잘 안되서 새로운 프로그래밍 언어가 투입되는데, **데니스 리치의 C Language**이다.

C언어가 B언어와 달라진점 = **자료형의 개념을 추가**

- **자료형이란,**

    1. 메모리에 저장된
    2. 일정 길이의 정보를
    3. 해석하는 형식

<br>

## Visual Studio

2013부터 **SDL**의 추가

- C/C++은 Buffer Overflow 라는 에러가 자주 나타나는데, 사용자가 직접 메모리 관리를 해줘야 하기 때문이다. 그 중 높은 확률로 **문자열 함수의 결함**에서 나옴
- SDL 기능을 추가하면 **결함이 없는 함수를 이용하도록 유도**하기 때문에 결함이 있는 함수를 쓰면 아예 컴파일을 시켜주지 않는다. ex) `gets` 대신 `gets_s` 함수를 이용

글꼴이 상당히 중요함

- **Consolas** Font = 개발자들이 실수를 덜 하도록 방지하는 Font

<br>

## Solution & Project

![solution_project](./docs/solution_project.png)

1. **Solution**: **여러개의 프로그램**이 모인 하나의 완성품
2. **Project**: 하나의 실행 파일(프로그램)을 만드는데 필요한 관련 파일의 집합(cpp, img, wmv …)
3. **Build**: **Compile**과 **Link**를 통해 하나의 실행파일을 만드는 과정
    - `Ctrl + Shift + F5`: Solution 전체 빌드
    - `F7`: Project 전체 빌드
    - `Ctrl + F5`: 빌드 후 Project 실행
    - `F5`: Debug mode run

**Build** = Compile + Link

- **Compile**: Compiler가 소스코드(High Level Language, 인간을 위한 언어)를 해석해서 목적파일(Low Level Language, CPU가 이해할 수 있는 언어)로 번역
- **Link**: Linker가 OS에 맞게 목적파일끼리 서로 필요한 것을 연결시키고, library에서 추가 코드를 가지고 와서 하나의 실행파일을 완성

<br>

## (Complie) Preprocessor

소스코드 Complie전, 뒤에 오는 파일을 코드의 일부로 포함하여 Complie한다는 의미

- `.h` : Header file, 선언용 & `.c` : Source file, 정의용
- **선언 + 정의**가 최종적으로 Complie 된다.
- 함수의 선언과 정의 → `int main(void) { }`
    - **선언**: 반환형식 + 함수이름 + (매개변수)
    - **정의**: { }, Block Scope로 구문(Statement) 여러 개가 이어진 절차적 흐름
    - **void**: 없음을 의미하는 자료형 (생략된 건지, 없는 것인지 구분)