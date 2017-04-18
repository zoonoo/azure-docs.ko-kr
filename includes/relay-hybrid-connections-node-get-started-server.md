### <a name="create-a-nodejs-application"></a>Node.js 응용 프로그램 만들기
* `listener.js`라는 새 JavaScript 파일을 만듭니다.

### <a name="add-the-relay-npm-package"></a>릴레이 NPM 패키지 추가
* 프로젝트 폴더에 있는 노드 명령 프롬프트에서 `npm install hyco-ws`를 실행합니다.

### <a name="write-some-code-to-receive-messages"></a>메시지를 수신하는 코드 작성
1. `listener.js` 파일 맨 위에 다음 `constant`를 추가합니다.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. 다음 릴레이 `constants`를 하이브리드 연결 연결 정보에 대한 `listener.js`에 추가합니다. 대괄호 안의 자리 표시자를 하이브리드 연결을 만들 때 얻은 적절한 값으로 바꿉니다.
   
   1. `const ns` - 릴레이 네임스페이스(FQDN 사용 - 예: `{namespace}.servicebus.windows.net`)
   2. `const path` - 하이브리드 연결의 이름
   3. `const keyrule` - SAS 키의 이름
   4. `const key` - SAS 키 값
3. `listener.js` 파일에 다음 코드를 추가합니다.
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    listener.js는 다음과 같아야 합니다.
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

