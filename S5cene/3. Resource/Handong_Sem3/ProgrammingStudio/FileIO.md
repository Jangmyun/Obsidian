사용자 입력을 받아 파일에 쓰기

cpp

Copy code

  

```cpp

#include <iostream>
#include <fstream>
#include <string>

int main() {
std::string filename;
std::string userInput;

// 파일 이름 입력 받기
std::cout << "저장할 파일 이름을 입력하세요: ";
std::getline(std::cin, filename);

// 파일 출력 스트림 객체 생성
std::ofstream outFile(filename);
  
// 파일 열기 확인
if (!outFile) {
std::cerr << "파일을 열 수 없습니다." << std::endl;
return 1; // 오류 코드 반환
}

// 사용자 입력 받기
std::cout << "파일에 저장할 텍스트를 입력하세요 (끝내려면 'exit' 입력):" << std::endl;

while (true) {
std::getline(std::cin, userInput);

if (userInput == "exit") {
break; // 'exit' 입력 시 루프 종료
}

// 파일에 데이터 작성
outFile << userInput << std::endl;
}

// 파일 스트림 닫기
outFile.close();
std::cout << "파일 작성이 완료되었습니다." << std::endl;
return 0;
}
```

  

코드 설명:

#include <iostream>와 #include <fstream>:

  

iostream은 표준 입출력 라이브러리입니다.
fstream은 파일 입출력 라이브러리입니다.
std::string filename;과 std::string userInput;:
파일 이름과 사용자로부터 입력 받을 텍스트를 저장할 변수를 선언합니다.
std::cout << "저장할 파일 이름을 입력하세요: ";:
사용자로부터 저장할 파일 이름을 입력 받습니다.
std::getline(std::cin, filename);
파일 이름을 입력 받습니다. std::getline을 사용하여 공백을 포함한 전체 문자열을 읽습니다.
std::ofstream outFile(filename);:
std::ofstream 객체를 생성하여 파일을 엽니다. 파일이 없으면 새로 생성됩니다.
if (!outFile):
파일이 제대로 열렸는지 확인합니다. 파일을 열 수 없는 경우, 에러 메시지를 출력하고 프로그램을 종료합니다.
std::cout << "파일에 저장할 텍스트를 입력하세요 (끝내려면 'exit' 입력):" << std::endl;:
  
사용자로부터 텍스트 입력을 받기 전에 안내 메시지를 출력합니다.

while (true) { ... }:
무한 루프를 사용하여 사용자로부터 텍스트 입력을 계속 받습니다.

std::getline(std::cin, userInput);:
사용자로부터 한 줄의 텍스트를 입력 받습니다.

if (userInput == "exit") { break; }:
입력된 텍스트가 'exit'이면 루프를 종료합니다.

outFile << userInput << std::endl;:
파일에 입력된 텍스트를 씁니다.

outFile.close();:
파일 스트림을 닫습니다.
std::cout << "파일 작성이 완료되었습니다." << std::endl;:

파일 작성이 완료되었음을 알리는 메시지를 출력합니다.
이 예제는 사용자로부터 파일 이름과 텍스트를 입력 받아 해당 파일에 텍스트를 저장합니다. 'exit'를 입력하면 입력이 종료되고 파일 작성이 완료됩니다. 이 방법을 통해 파일에 텍스트를 쉽게 입력할 수 있습니다.