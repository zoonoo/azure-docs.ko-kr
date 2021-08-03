---
title: Azure Video Analyzer 플레이어 위젯 사용
description: 이 참조 문서에서는 애플리케이션에 Video Analyzer 플레이어 위젯을 추가하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 05/11/2021
ms.openlocfilehash: f4c2d3f7d13002d0de231859bc31a74b72b1c7fd
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555131"
---
# <a name="using-the-azure-video-analyzer-player-widget"></a>Azure Video Analyzer 플레이어 위젯 사용

이 자습서에서는 애플리케이션 내에서 Azure Video Analyzer 플레이어 위젯을 사용하는 방법에 대해 알아봅니다.  이 코드는 최종 사용자가 비디오를 재생하고 분할된 비디오 파일의 일부를 탐색할 수 있도록 하는 간편한 위젯을 포함합니다.  이렇게 하려면 위젯이 포함된 정적 HTML 페이지와 이를 작동하도록 하는 모든 부분을 생성합니다.

이 자습서에서는 다음 작업을 수행하게 됩니다.

> [!div class="checklist"]
> * 토큰 만들기
> * 비디오 나열
> * [비디오 애플리케이션 리소스](./terminology.md#video) 재생을 위한 기본 URL 가져오기
> * 플레이어를 사용하여 페이지 만들기
> * 스트리밍 엔드포인트 및 토큰을 플레이어에 전달

## <a name="suggested-pre-reading"></a>추천 참고 자료

- [웹 구성 요소](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 다음과 같습니다.
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
* HTML 파일에 대한 [Visual Studio Code](https://code.visualstudio.com/) 또는 기타 편집기.
하나.
* [지속적인 비디오 녹화 및 재생](./use-continuous-video-recording.md) 또는 [에지 디바이스에서 동작 감지 및 비디오 녹화](./detect-motion-record-video-clips-cloud.md)

## <a name="create-a-token"></a>토큰 만들기

이 섹션에서는 나중에 문서에서 사용할 JWT 토큰을 만듭니다.  JWT 토큰을 생성하는 샘플 애플리케이션을 사용하고 액세스 정책을 만드는 데 필요한 모든 필드를 제공합니다.

> [!NOTE] 
> RSA 또는 ECC 인증서를 기반으로 JWT 토큰을 생성하는 방법을 잘 알고 있는 경우 이 섹션을 건너뛸 수 있습니다.

1. [여기](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/jwt-token-issuer/)에 있는 JWTTokenIssuer 애플리케이션을 다운로드합니다.

   > [!NOTE] 
   > 대상 그룹 값 구성에 대한 자세한 내용은 이 [문서](./access-policies.md)를 참조하세요.

2. Visual Studio Code를 시작하고 *.sln 파일이 포함된 폴더를 엽니다.
3. 탐색기 창에서 program.cs 파일로 이동합니다.
4. 라인 77 수정 - 다음과 같이 대상 그룹을 Video Analyzer 엔드포인트 및 /videos/*로 변경합니다.

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```
5. 라인 78 수정 - 발급자를 인증서의 발급자 값으로 변경합니다.  예제: https://contoso.com

   > [!NOTE] 
   > Video Analyzer 엔드포인트는 Azure에서 Video Analyzer 리소스의 개요 섹션에서 찾을 수 있습니다. "JSON 보기" 링크를 클릭해야 합니다. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/endpoint.png" alt-text="플레이어 위젯 - 엔드포인트":::
6. 파일을 저장합니다.
7. `F5` 키를 눌러 JWTTokenIssuer 애플리케이션을 실행합니다.

그러면 애플리케이션이 빌드되고 실행됩니다.  빌드 후 openssl을 통해 인증서를 만들어 실행됩니다.  디버그 폴더에 있는 JWTTokenIssuer.exe 파일을 실행할 수도 있습니다.  애플리케이션을 실행하는 이점은 다음과 같이 입력 옵션을 지정할 수 있다는 것입니다.

- JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]

JWTTokenIssuer는 JWT 토큰 및 다음과 같은 필요한 구성 요소를 만듭니다.

- `kty`; `alg`; `kid`; `n`; `e`

나중에 사용할 수 있도록 이러한 값을 복사해야 합니다.

## <a name="create-an-access-policy"></a>액세스 정책 만들기

액세스 정책은 지정된 Video Analyzer 비디오 스트림에 대한 액세스 권한 및 기간을 정의합니다.  이 자습서에서는 Azure Portal에서 Video Analyzer에 대한 액세스 정책을 구성합니다.  

1. Azure Portal에 로그인하여 Video Analyzer 계정이 있는 리소스 그룹으로 이동합니다.
1. Video Analyzer 리소스를 선택합니다.
1. Video Analyzer 아래에서 액세스 정책을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="플레이어 위젯 - 포털 액세스 정책":::    
1. 새로 만들기를 클릭하고 다음을 입력합니다.

   > [!NOTE] 
   > 이러한 값은 이전 단계에서 만든 JWTTokenIssuer 애플리케이션에서 제공됩니다.

   - 액세스 정책 이름 - 임의의 이름입니다.

   - 발급자 - JWT 토큰 발급자와 일치해야 합니다. 

   - 대상 그룹 - JWT 토큰의 대상 그룹 -- ${System.Runtime.BaseResourceUrlPattern}이 기본값입니다.  대상 그룹 및 ${System.Runtime.BaseResourceUrlPattern}에 대한 자세한 내용은 이 [문서](./access-policies.md)를 참조하세요.

   - 키 유형 - kty -- RSA 

   - 알고리즘 - 지원되는 값은 RS256, RS384, RS512입니다.

   - 키 ID - kid -- 인증서에서 생성됩니다.

   - N 값 - RSA의 경우 N 값은 모듈러스입니다.

   - E 값 - RSA의 경우 E 값은 공용 지수입니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="플레이어 위젯 - 액세스 정책 포털":::     
1. `save`을 클릭합니다.

## <a name="list-video-analyzer-video-resources"></a>Video Analyzer 비디오 리소스 나열

다음으로 비디오 리소스 목록을 생성해야 합니다.  이 작업은 생성된 토큰으로 인증하여 위에서 사용한 계정 엔드포인트에 대한 REST 호출을 통해 수행됩니다.

여러 가지 방법으로 REST API에 GET 요청을 보낼 수 있지만, 이를 위해 JavaScript 함수를 사용할 예정입니다.  아래 코드는 동기 `GET` 요청을 보내기 위해 페이지의 `clientApiEndpointUrl` 및 `token` 필드에 저장하는 값과 결합된 [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp)를 사용합니다.  그런 다음 결과 비디오 목록을 가져와서 페이지에서 설정한 `videoList` 텍스트 영역에 저장합니다.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```

## <a name="add-the-video-analyzer-player-component"></a>Video Analyzer 플레이어 구성 요소 추가

이제 클라이언트 API 엔드포인트 URL, 토큰 및 비디오 이름이 있으므로 플레이어를 페이지에 추가할 수 있습니다.

1. 페이지에 직접 패키지를 추가하여 플레이어 모듈 자체를 포함합니다.  애플리케이션에 NPM 패키지 방향을 포함하거나 다음과 같이 런타임에 동적으로 포함되도록 할 수 있습니다.
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. AVA-Player 요소를 문서에 추가합니다.
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. 페이지에 있는 Video Analyzer 플레이어 위젯에 대한 링크를 가져옵니다.
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. 플레이어를 보유한 값으로 구성하려면 다음과 같이 개체로 설정해야 합니다.
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. 플레이어에 비디오를 로드하여 시작
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>모든 요소 결합

위의 웹 요소를 결합하면 계정 엔드포인트 및 토큰을 사용하여 비디오를 볼 수 있는 다음과 같은 정적 HTML 페이지가 표시됩니다.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
</body>
</html>
```

## <a name="host-the-page"></a>페이지 호스팅

이 페이지를 로컬로 테스트할 수 있지만 호스트된 버전을 테스트할 수 있습니다.  페이지를 빠르게 호스트하는 방법이 없는 경우 Storage에서 [정적 웹 사이트](../../storage/blobs/storage-blob-static-website.md)를 사용하는 방법에 대한 지침은 다음과 같습니다.  아래는 이 자습서에서 사용하는 파일에 대해 업데이트된 [보다 완전한 지침](../../storage/blobs/storage-blob-static-website-how-to.md)의 요약 버전입니다.

1. Storage 계정 만들기
1. 왼쪽의 `Data management` 아래에서 `Static website`를 클릭합니다.
1. 스토리지 계정에서 정적 웹 사이트 `Enable`
1. `Index document name`의 경우 `index.html`을 입력합니다.
1. `Error document path`의 경우 `404.html`을 입력합니다.
1. 위쪽에 있는 `Save`을 선택합니다.
1. 표시되는 `Primary endpoint`에 유의합니다. 이는 사용자의 웹 사이트가 됩니다.
1. `Primary endpoint` 위의 `$web`을 클릭합니다.
1. 상단의 `Upload` 단추를 사용하여 정적 HTML 페이지를 `index.html`로 업로드합니다.

## <a name="play-a-video"></a>비디오 재생

이제 페이지를 호스트했으므로 해당 페이지로 이동하여 단계를 진행할 수 있습니다.

1. `Client API endpoint URL` 및 `Token`을 입력합니다.
1. `Get videos`를 누릅니다.
1. 비디오 목록에서 비디오 이름을 선택하고 `Video name` 입력 필드에 입력합니다.
1. `Play video`를 누릅니다.

## <a name="additional-details"></a>추가 정보

### <a name="refreshing-the-access-token"></a>액세스 토큰 새로 고침

플레이어는 이전에 생성한 액세스 토큰을 사용하여 재생 권한 부여 토큰을 가져옵니다.  토큰은 정기적으로 만료되므로 새로 고쳐야 합니다.  새 액세스 토큰을 생성한 후 플레이어의 액세스 토큰을 새로 고치는 방법에는 두 가지가 있습니다.

* 위젯 메서드 `setAccessToken`을 적극적으로 호출
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* 이 이벤트를 수신하여 `TOKEN_EXPIRED` 이벤트에 대한 조치
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` 이벤트는 토큰이 만료되기 5초 전에 발생합니다.  이벤트 수신기를 설정하는 경우 플레이어 위젯에서 `load` 함수를 호출하기 전에 설정하는 것이 좋습니다.

### <a name="configuration-details"></a>구성 세부 정보

위의 플레이어에 대한 간단한 구성이 있지만 구성 값에 대한 광범위한 옵션을 지원합니다.  다음은 지원되는 필드입니다.

| 이름   | Type             | 설명                         |
| ------ | ---------------- | ----------------------------------- |
| token  | 문자열 | 위젯의 JWT 토큰 |
| videoName | 문자열 | 비디오 리소스의 이름  |
| clientApiEndpointUrl | 문자열 | 클라이언트 API의 엔드포인트 URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>애플리케이션에 코드를 로드하는 대체 방법

애플리케이션에 코드를 가져오는 데 사용되는 패키지는 [여기](https://www.npmjs.com/package/video-analyzer-widgets)의 NPM 패키지입니다.  위의 예에서 최신 버전은 런타임에 리포지토리에 직접 로드되었지만 다음을 사용하여 로컬로 패키지를 다운로드하고 설치할 수도 있습니다.

```bash
npm install @azure/video-analyzer/widgets
```

또는 Typescript에 다음을 사용하여 애플리케이션 코드 내에서 가져올 수 있습니다.

```typescript
import { Player } from '@video-analyzer/widgets';
```

또는 플레이어 위젯을 동적으로 생성하려는 경우 Javascript에 다음을 사용할 수 있습니다.
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

이 방법을 사용하여 가져오는 경우 가져오기가 완료된 후에는 프로그래밍 방식으로 플레이어 개체를 만들어야 합니다.  위의 예에서는 `ava-player` HTML 태그를 사용하여 페이지에 모듈을 추가했습니다.  코드를 통해 플레이어 개체를 만들려면 JavaScript에서 다음을 수행할 수 있습니다.

```javascript
const avaPlayer = new ava.widgets.player();
```

또는 Typescript에서 다음을 수행할 수 있습니다.

```typescript
const avaPlayer = new Player();
```

그런 다음 HTML에 추가해야 합니다.

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>다음 단계

* [위젯 API](https://github.com/Azure/video-analyzer/widgets)에 대해 자세히 알아보기
