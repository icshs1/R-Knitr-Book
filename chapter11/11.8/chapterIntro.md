실행하는 방법은 다음과 같다. 

1. 프로젝트 파일(`11.8.Rproj`)파일을 클릭하면 RStutio가 실행된다. 
2. RStudio의 R 콘솔에서 먼저 `shiny` 패키지를 로딩한다.
    - `library(shiny)`
3. R 콘솔에 다음 명령을 입력하여 앱을 실행한다.
    - `runApp()`

* 컴퓨터에 `shiny` 패키지가 설치되어 있지 않다면 `install.packages('shiny')`라는 명령으로 설치한다. 
* 위 아무런 파라미터 없어 `runApp()` 명령이 실행되기 위해서는 `ui.R`, `server.R` 파일이 들어있는 디렉터리가 워킹디렉터리가 되어야 한다. 위에서 설명한대로 프로젝트 파일을 누르면 자동적으로 설정되겠지만, 혹 다른 방법으로 접근하는 경우 이 디렉터리를 워킹디렉터리로 하거나, `runApp()`함수에 디렉터리 이름을 경로와 함께 준다.
* `runApp()` 함수가 실행되면, 앱이 브라우저로 보통은 자동적으로 실행되겠지만, 그렇지 않은 경우에는 R 콘솔에서 다음 메시지를 확인한다. 끝의 번호는 사용할 때마다 다를 수 있다.
    - `Listening on http://127.0.0.1:5754'
    -  이것은 로컬호스트에서 5754 포트로 실행된다는 뜻이므로 웹 브라우저 주소창에서 <http://localhost:5754>라고 입력하면 된다. 