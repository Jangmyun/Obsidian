## [Setup](https://gbdev.io/gb-asm-tutorial/part1/setup.html)
#### 특이사항
+ RGBDS 문법 하이라이팅 패키지를 제공하는 코드 에디터로 [Sublime Text](https://www.sublimetext.com/)를 추천하는데, VSCode 에도 `Z80 Assembly`라는 익스텐션이 있으므로 VSCode를 사용해도 무방하다.
+ 튜토리얼에서는 에뮬레이터로 `Emulicious`를 사용한다. 맥 환경에서는 `Sameboy`가 더 깔끔한 것 같다. 

## Hello World! 출력하기

원하는 디렉토리에서 아래 파일을 생성
+ [hello-world.asm](https://gbdev.io/gb-asm-tutorial/assets/hello-world.asm)
+ [hardware.inc](https://raw.githubusercontent.com/gbdev/hardware.inc/v4.0/hardware.inc)
다음 명령어로 컴파일

```shell
$ rgbasm -o hello-world.o hello-world.asm
$ rgblink -o hello-world.gb hello-world.o
$ rgbfix -v -p 0xFF hello-world.gb
```

#### 생성된 `hello-world.gb` 실행 결과

![[Screenshot 2025-01-28 at 12.53.23.png]]
### Toolchain의 설명
툴체인은 쉽게 말하면 하나의 소프트웨어를 만드는 데 사용하는 도구 모음집이다. (컴파일러, 링커, 라이브러리 등)

#####  `rgbasm`과 `rgblink`가 무엇인지 알아보자

```shell
$ rgbasm -o hello-world.o hello-world.asm
$ rgblink -o hello-world.gb hello-world.o
$ rgbfix -v -p 0xFF hello-world.gb
```

`RGBASM`은**어셈블러**다. `.asm`과 `.inc` 파일을 읽어서 object 파일 (`.o`)로 만든다. 이것만으로는 게임보이 ROM으로 만들 수 없다.

`RGBLINK`는  **linker**로, object 파일들을 가져와서 ROM으로 linking한다.

소스코드 -> `rgbasm` -> object 파일 -> `rgblink` -> ROM

##### `rgbfix`
`RGBLINK`로 ROM 파일을 생성했지만 아직도 부족하다. ROM에는 메타데이터를 보관하는 헤더가 있다.
헤더는 아래 세 요소가 필수적이다.
+ 닌텐도 로고
+ ROM 사이즈
+ 헤더 checksum

콘솔 시작 전에 boot ROM이라는 것을 실행하고, 카트리지로부터 로고를 읽어와 화면에 그리고, 부트 애니메이션을 표시한다. 애니메이션이 끝나면 logo가 내부적으로 저장한 값과 같은지를 체크하고 같지 않다면 게임이 실행되지 않는다.

마찬가지로 boot ROM은 header의 체크섬을 검사하는데, 헤더가 가지고 있는 내용과 일치하지 않으면 실행되지 않는다.

`RGBFIX`는 헤더의 필수적인 세 필드를 채운다. `-v`옵션으로 헤더를 valid하게 만든다. `-p 0xFF`는 ROM에게 올바른 사이즈를 알려주고 헤더의 ROM size 필드를 채운다.


