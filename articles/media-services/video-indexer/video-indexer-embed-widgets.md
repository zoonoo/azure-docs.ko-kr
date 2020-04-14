---
title: 앱에 비디오 인덱서 위젯 포함
titleSuffix: Azure Media Services
description: 앱에 비디오 인덱서 위젯을 포함하는 방법을 알아보세요.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 5134a262397676aa9b59de9b0c6de61c26d21523
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262913"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>앱에 비디오 인덱서 위젯 포함

이 문서에서는 앱에 비디오 인덱서 위젯을 포함할 수 있는 방법을 보여 줍니다. 비디오 인덱서앱에 세 가지 유형의 위젯을 포함시키는 것을 지원합니다: *인지 인사이트,* *플레이어*및 *편집기*.

버전 2부터 위젯 기본 URL에는 지정된 계정의 영역이 포함됩니다. 예를 들어 미국 서부 지역의 계정은 `https://wus2.videoindexer.ai/embed/insights/...`를 생성합니다.

## <a name="widget-types"></a>위젯 유형

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

인지 인사이트 위젯에는 비디오 인덱싱 프로세스에서 추출한 모든 시각적 인사이트가 포함되어 있습니다. 인지 인사이트 위젯은 다음과 같은 선택적 URL 매개 변수를 지원합니다.

|속성|정의|Description|
|---|---|---|
|`widgets` | 쉼표로 구분된 문자열 | 렌더링하려는 인사이트를 제어할 수 있습니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` 사람 및 키워드 UI 인사이트만 렌더링합니다.<br/>사용 가능한 옵션 : 사람, 애니메이션캐릭터, 키워드, 라벨, 감정, 감정, 주제, 키프레임, 성적 증명서, ocr, 스피커, 장면, 및 명명 엔터티.|
|`controls`|쉼표로 구분된 문자열|렌더링할 컨트롤을 제어할 수 있습니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` 검색 옵션 및 다운로드 버튼만 렌더링합니다.<br/>사용 가능한 옵션: 검색, 다운로드, 사전 설정, 언어.|
|`language`|짧은 언어 코드(언어 이름)|인사이트 언어를 제어합니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>또는 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 짧은 언어 코드 | UI의 언어를 제어합니다. 기본값은 `en`입니다. <br/>예: `locale=de`.|
|`tab` | 기본 선택 탭 | 기본적으로 렌더링되는 **Insights** 탭을 제어합니다. <br/>예: `tab=timeline` **타임라인** 탭을 선택한 인사이트를 렌더링합니다.|

### <a name="player-widget"></a>플레이어 위젯

플레이어 위젯을 사용하여 적응형 비트 레이트(bit rate)를 사용하여 비디오를 스트리밍할 수 있습니다. 플레이어 위젯은 다음과 같은 선택적 URL 매개 변수를 지원합니다.

|속성|정의|Description|
|---|---|---|
|`t` | 시작시간부터 초 | 플레이어가 지정된 시점부터 재생을 시작합니다.<br/> 예: `t=60`. |
|`captions` | 언어 코드 | 캡션 메뉴에서 사용할 수 있도록 위젯 로드 중에 지정된 언어로 **캡션을** 가져옵니다.<br/> 예: `captions=en-US`. |
|`showCaptions` | 부울 값 | 플레이어가 자막을 사용하도록 설정된 상태로 로드되도록 합니다.<br/> 예: `showCaptions=true`. |
|`type`| | 오디오 플레이어 스킨을 활성화합니다(비디오 부분이 제거됨).<br/> 예: `type=audio`. |
|`autoplay` | 부울 값 | 플레이어가 로드될 때 비디오 재생을 시작해야 하는지 나타냅니다. 기본값은 `true`입니다.<br/> 예: `autoplay=false`. |
|`language`/`locale` | 언어 코드 | 플레이어 언어를 제어합니다. 기본값은 `en-US`입니다.<br/>예: `language=de-DE`.|

### <a name="editor-widget"></a>편집기 위젯

편집기 위젯을 사용하여 새 프로젝트를 만들고 비디오의 인사이트를 관리할 수 있습니다. 편집기 위젯은 다음과 같은 선택적 URL 매개 변수를 지원합니다.

|속성|정의|Description|
|---|---|---|
|`accessToken`<sup>*</sup> | String | 위젯을 포함하는 데 사용되는 계정에만 있는 동영상에 대한 액세스를 제공합니다.<br> 편집기 위젯에는 `accessToken` 매개 변수가 필요합니다. |
|`language` | 언어 코드 | 플레이어 언어를 제어합니다. 기본값은 `en-US`입니다.<br/>예: `language=de-DE`. |
|`locale` | 짧은 언어 코드 | 인사이트 언어를 제어합니다. 기본값은 `en`입니다.<br/>예: `language=de`. |

<sup>*</sup>소유자는 주의 `accessToken` 제공 해야 합니다.

## <a name="embedding-public-content"></a>공용 콘텐츠 포함

1. [비디오 인덱서](https://www.videoindexer.ai/) 웹 사이트에 로그인합니다.
2. 작업할 비디오를 선택합니다.
3. 비디오 아래에 나타나는**</>** 임베드 버튼()을 선택합니다.

    **포함** 단추를 선택한 후 앱에 포함할 위젯을 선택할 수 있습니다.
4. 원하는 위젯 유형을 선택합니다(인지**인사이트,** **플레이어**또는 **편집기).**
5. 포함 코드를 복사합니다(포함 & **공유** 대화 상자에 **포함된 코드 복사에** 나타납니다).
6. 앱에 코드를 추가합니다.

> [!NOTE]
> 동영상 URL을 공유하는 데 문제가 있는 `location` 경우 링크에 매개변수를 추가합니다. 매개 변수는 비디오 [인덱서가 있는 Azure 영역으로](regions.md)설정해야 합니다. 예: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`

## <a name="embedding-private-content"></a>프라이빗 콘텐츠 포함

개인 비디오를 포함하려면 iframe의 특성에 액세스 `src` 토큰을 전달해야 합니다.

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Cognitive Insights 위젯 콘텐츠를 얻으려면 다음 방법 중 하나를 사용합니다.

- [인사이트 위젯](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API 를 가져옵니다.<br/>
- [비디오 액세스 토큰 가져옵니다.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?) URL에 쿼리 매개 변수로 추가합니다. 이 URL을 `src` 앞에 표시된 것처럼 iframe의 값으로 지정합니다.

포함된 위젯에서 편집 인사이트 기능을 제공하려면 편집 권한이 포함된 액세스 토큰을 전달해야 합니다. [인사이트 위젯 받기를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) [사용하거나](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) `&allowEdit=true`.

## <a name="widgets-interaction"></a>위젯 상호 작용

인지 인사이트 위젯은 앱의 동영상과 상호 작용할 수 있습니다. 이 섹션에서는 이러한 상호 작용을 수행하는 방법을 보여 줍니다.

![인지 통찰력 위젯 비디오 인덱서](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>원본 간 통신

비디오 인덱서 위젯을 사용하여 다른 구성 요소와 통신하려면 비디오 인덱서 서비스를 제공합니다.

- 원본 간 통신 HTML5 `postMessage`방법을 사용합니다.
- VideoIndexer.ai 원본을 통해 메시지의 유효성을 검사합니다.

고유한 플레이어 코드를 구현하고 Cognitive Insights 위젯과 통합하는 경우 VideoIndexer.ai 메시지의 출처를 확인하는 것은 사용자의 책임입니다.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>앱 또는 블로그에 위젯 포함(권장)

이 섹션에서는 사용자가 앱에서 인사이트 제어를 선택하면 플레이어가 관련 순간으로 이동하도록 두 비디오 인덱서 위젯 간의 상호 작용을 달성하는 방법을 보여 줍니다.

1. 플레이어 위젯 포함 코드를 복사합니다.
2. 인지 인사이트 포함 코드를 복사합니다.
3. 두 위젯 간의 통신을 처리하는 [중재자(mediator) 파일](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)을 추가합니다.<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

이제 사용자가 앱에서 인사이트 제어를 선택하면 플레이어는 관련 순간으로 이동합니다.

자세한 내용은 비디오 [인덱서 - 위젯 데모를 모두 포함하십시오.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>인지 인사이트 위젯 포함 및 Azure Media Player를 사용하여 콘텐츠 재생

이 섹션에서는 [AMP 플러그인을](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)사용하여 인지 정보 위젯과 Azure Media Player 인스턴스 간의 상호 작용을 달성하는 방법을 보여 주며 있습니다.

1. AMP 플레이어에 대한 비디오 인덱서 플러그인 추가:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 비디오 인덱서 플러그인을 사용하여 Azure 미디어 플레이어를 인스턴스화합니다.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. 인지 인사이트 포함 코드를 복사합니다.

이제 Azure 미디어 플레이어와 통신할 수 있습니다.

자세한 내용은 Azure [미디어 플레이어 + VI 인사이트 데모를](https://codepen.io/videoindexer/pen/rYONrO)참조하십시오.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>비디오 인덱서 인지 인사이트 위젯을 포함하고 다른 비디오 플레이어를 사용

Azure Media Player 이외의 비디오 플레이어를 사용하는 경우 통신을 위해 비디오 플레이어를 수동으로 조작해야 합니다.

1. 비디오 플레이어를 삽입합니다.

    예를 들어, 표준 HTML5 플레이어:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 인지 인사이트 위젯을 포함합니다.
3. "메시지" 이벤트를 수신 대기하여 플레이어에 대한 통신을 구현합니다. 다음은 그 예입니다.

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

자세한 내용은 Azure [미디어 플레이어 + VI 인사이트 데모를](https://codepen.io/videoindexer/pen/YEyPLd)참조하십시오.

## <a name="adding-subtitles"></a>자막 추가

사용자 고유의 [Azure Media Player에](https://aka.ms/azuremediaplayer)Video Indexer 인사이트를 `GetVttUrl` 포함하면 이 방법을 사용하여 자막(자막)을 가져올 수 있습니다. 비디오 인덱서 AMP 플러그인에서 자바 스크립트 메서드를 `getSubtitlesUrl` 호출할 수도 있습니다(앞에서 설명한 대로).

## <a name="customizing-embeddable-widgets"></a>포함 가능한 위젯 사용자 지정

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

원하는 인사이트 유형을 선택할 수 있습니다. 이렇게 하려면 API 또는 웹 앱에서 받는 포함 코드에 추가된 다음 URL 매개 변수에 대한 값으로 `&widgets=<list of wanted widgets>`지정합니다.

가능한 값은 `people`다음과 `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr`같습니다. `speakers` `scenes` `namedEntities`

예를 들어 사람 및 키워드 인사이트만 포함하는 위젯을 포함하려는 경우 iframe 포함 URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

iframe 창의 제목도 iframe URL에 `&title=<YourTitle>`을 제공하여 사용자 지정할 수 있습니다. (HTML `<title>` 값을 사용자 지정합니다.)
   
예를 들어 iframe 창에 "MyInsights"라는 제목을 지정하려는 경우 URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

이 옵션은 새 창에서 인사이트를 열어야 하는 경우에만 관련이 있습니다.

### <a name="player-widget"></a>플레이어 위젯

Video Indexer 플레이어를 포함하는 경우 iframe의 크기를 지정하여 플레이어 크기를 선택할 수 있습니다.

다음은 그 예입니다.

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

기본적으로 비디오 인덱서 플레이어는 비디오의 자막을 기반으로 하는 자막을 자동 생성했습니다. 자막은 동영상이 업로드될 때 선택된 소스 언어로 동영상에서 추출됩니다.

다른 언어로 포함하려는 경우 포함 된 플레이어 `&captions=<Language Code>` URL에 추가할 수 있습니다. 캡션을 기본적으로 표시하려면 showCaptions=true를 &전달할 수 있습니다.

그러면 포함 URL이 다음과 같습니다.

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>자동 실행

기본적으로 플레이어는 비디오 재생을 시작합니다. 이전 임베드 URL로 `&autoplay=false` 전달하지 않도록 선택할 수 있습니다.

## <a name="code-samples"></a>코드 샘플

비디오 인덱서 API 및 위젯에 대한 샘플이 포함된 [코드 샘플](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) 리포지토리를 참조하십시오.

| 파일/폴더                       | Description                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 사용자 지정 Azure 미디어 플레이어에서 비디오 인덱서 비디오를 로드합니다.                        |
| `azure-media-player-vi-insights`  | 사용자 지정 Azure 미디어 플레이어로 VI 인사이트를 포함합니다.                             |
| `control-vi-embedded-player`      | VI 플레이어를 포함하고 외부에서 제어 할 수 있습니다.                                    |
| `custom-index-location`           | 사용자 지정 외부 위치에서 VI 인사이트를 포함합니다(고객이 Blob일 수 있음).     |
| `embed-both-insights`             | VI 인사이트 사용의 기본 사용 모두 플레이어와 통찰력.                            |
| `embed-insights-with-AMP`         | 사용자 지정 Azure 미디어 플레이어와 VI 인사이트 위젯을 포함합니다.                      |
| `customize-the-widgets`           | 사용자 정의 옵션으로 VI 위젯을 포함합니다.                                     |
| `embed-both-widgets`              | VI 플레이어와 인사이트를 포함하고 그들 사이의 통신.                      |
| `url-generator`                   | 사용자 지정 옵션에 따라 위젯 사용자 지정 포함 URL을 생성합니다.             |
| `html5-player`                    | 기본 HTML5 비디오 플레이어로 VI 인사이트를 포함합니다.                           |

## <a name="next-steps"></a>다음 단계

비디오 인덱서 인사이트를 보고 편집하는 방법에 대한 자세한 내용은 [비디오 인덱서 인사이트를 보고 편집하는 방법을](video-indexer-view-edit.md)참조하세요.

또한 비디오 [인덱서 CodePen을](https://codepen.io/videoindexer/pen/eGxebZ)확인하십시오.
