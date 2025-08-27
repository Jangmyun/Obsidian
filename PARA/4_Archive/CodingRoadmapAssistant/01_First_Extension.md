# VSCode Extension 만들어보기

## 1. 익스텐션 생성
Yeoman과 VS Code Extension Generator 설치
```bash
$ npm install -g yo generator-code
```

generator를 통해 프로젝트 설정 후 생성
```bash
yo code

# ? What type of extension do you want to create? New Extension (TypeScript)
# ? What's the name of your extension? HelloWorld
### Press <Enter> to choose default for all options below ###

# ? What's the identifier of your extension? helloworld
# ? What's the description of your extension? LEAVE BLANK
# ? Initialize a git repository? Yes
# ? Which package manager to use? npm

code ./helloworld
```

`F5` 버튼으로 익스텐션 컴파일 및 실행
`Hello World`를 Command Palette에서 실행

## 2. `Hello World` 익스텐션의 구조

- **Activation Event** : `package.json`파일의 `activationEvents` 배열에서 [activation events](https://code.visualstudio.com/api/references/activation-events)를 등록하고 특정 동작을 수행했을 때 익스텐션을 활성화 한다.<br>`Hello World`명령어의 경우 `onCommand:extension.helloWorld`를 등록했을 때 유저가 명령어 실행시 활성화된다.
- **Contribution Point** : `package.json`에서 `contirbutes.commands`를 통해 `Hello World`를 사용할 수 있도록 연결한다. 
-  [**VS Code API**](https://code.visualstudio.com/api/references/vscode-api) : `commands.registerCommand`를 사용해서 명령어 ID `extension.helloWorld`에 함수를 연결한다.

## 3. 익스텐션 프로젝트 구조
```
├── .vscode
│   ├── launch.json     // 익스텐션을 실행하고 디버깅하기 위한 설정
│   └── tasks.json      // 타입스크립트를 컴파일하는 빌드 작업을 위한 설정
├── .gitignore          // 빌드결과와 node_module을 무시한다
├── README.md           // 여러분의 익스텐션 기능에 대한 읽을 수 있는 설명
├── src
│   └── extension.ts    // 익스텐션 소스코드
├── package.json        // 익스텐션 manifest
├── tsconfig.json       // 타입스크립트 환경 설정
```

### [Extension Manifest](https://code.visualstudio.com/api/references/extension-manifest)
VS Code extension마다 하나의 `package.json`파일이 있어야 한다.
`package.json`은 Node.js 관련 정보와 `activationEvents`나 `contributes`같이 VSCode Extension 내용을 포함한다.
- `main` : extension의 entry point (첫 시작 파일 정의)
- `activationEvents` : Activation Events
- `contributes` : Contribution Points
- `engines.vscode`: VS Code API 최소 버전 명시

## 4. 익스텐션 엔트리 파일 (시작 파일)
익스텐션 파일은 `active`와 `deactivate`함수를 export한다. `activate`는 등록한 `Activation Event`가 발생했을 때 호출되고, `deactivate`는 익스텐션이 비활성화될 때 호출된다. (`deactive`함수는 어지간하면 쓸 일이 없다.)
```ts
// The module 'vscode' contains the VS Code extensibility API
// Import the module and reference it with the alias vscode in your code below
import * as vscode from 'vscode';

// this method is called when your extension is activated
// your extension is activated the very first time the command is executed
export function activate(context: vscode.ExtensionContext) {
  // Use the console to output diagnostic information (console.log) and errors (console.error)
  // This line of code will only be executed once when your extension is activated
  console.log('Congratulations, your extension "helloworld-sample" is now active!');

  // The command has been defined in the package.json file
  // Now provide the implementation of the command with registerCommand
  // The commandId parameter must match the command field in package.json
  let disposable = vscode.commands.registerCommand('helloworld.helloWorld', () => {
    // The code you place here will be executed every time your command is executed

    // Display a message box to the user
    vscode.window.showInformationMessage('Hello World!');
  });

  context.subscriptions.push(disposable);
}

// this method is called when your extension is deactivated
export function deactivate() {}

```


