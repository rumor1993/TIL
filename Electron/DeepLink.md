## DeepLink
사내에서 홈페이지에 특정버튼을 클릭했을때 일렉트론으로 만든 설치형 프로그램이 실행 되도록 처리를 하고싶다는 요청을 받고 이를 알아보던중에 모바일쪽에서 스킴(scheme)을 활용해서 앱에 화면을 보여주거나 앱을 실행시키는 기능을 DeepLink라고 하는것을 알게 되었다. 이를 일렉트론에 적용하면서 알게 된 내용을 정리해보려고 한다.


### 프로토콜
```
app.setAsDefaultProtocolClient(protocol[, path, args]) 
```
해당 함수를 통해서 URL스킴을 설정해주면 현재 실행파일에 대한 핸들러로 등록이 되며 이를 통해서 설정한 프로토콜값 (your-protocol://) 과 같은 모든 링크에 대해 호출시 현재 실행 파일이 실행된다


### package.json 
```
  "build": {
    "appId": "your.id",
    "mac": {
      "category": "your.app.category.type"
    },
    "protocols": {
      "name": "myApp",
      "schemes": ["myApp"]
    }
  }
```
Electron-builder의 경우 빌드를 위한 과정을 설정하는 부분에 'protocols" => 'schemes' 사용할 URL스킴을 설정해주고 app.setAsDefaultProtocolClient('myApp') 과 같은 형식으로 설정을 맞춰주면 된다.


### open-url
간단하게 해당 설정만으로도 URL스킴을 통한 앱 실행이 가능해지지만 URL스킴을 통해서 앱이 실행되었는지 여부를 체크하는 로직이 필요했고 이를 체크하기 위해서 
open-url 이라는 이벤트를 사용했다

```
app.on('open-url', function (event, url) {
  event.preventDefault();
  log("open-url event: " + url)
})
```

일렉트론 자체에서 지원해주는 기능인 만큼 구현이 간편했고 활용도가 높은 기술이라고 생각한다. 이를 통해서 링크별로 앱을 실행하는 방식이나 원하는 앱의 페이지를 보여주는등 다양한 방식으로 활용이 가능해보인다


### 추가적으로 고려해야하는 부분
홈페이지에 해당 버튼을 클릭하면 앱이 실행되는 기능을 하려고 한다면 우선적으로 URL스킴을 통해 프로토콜이 등록된 일렉트론 앱이 존재하는지 여부를 체크하는 로직 또한 어떤식으로 만들어야할지 생각해봐야겠다