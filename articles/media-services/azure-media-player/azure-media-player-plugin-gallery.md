---
title: Azure Media Player 플러그 인 갤러리
description: 이 문서에는 Azure Media Player에서 사용할 수 있는 플러그 인 목록이 포함되어 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727500"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player 플러그 인 갤러리 #

## <a name="plugins"></a>플러그 인 ##

| 플러그 인 이름                         | 데모 URL                    | 소스 코드                | Description    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| 추가 기능                 | | | |
| **신규!** AMP360Video                | [데모](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | 플러그 인을 사용하면 데스크톱 또는 VR 호환 디바이스의 Amp에서 360 비디오를 시각화할 수 있습니다. 전체 설명서는 [여기](https://doc\.babylonjs\.com/extensions/amp360video)서 사용할 수 있습니다. |
|  스프라이트 팁                         | [데모](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | AMS(Azure Media Services) MES(Media Encoder Standard)에서 생성된 비디오 썸네일 이미지 스프라이트의 타임라인 렌더링을 위한 AMP(Azure Media Player) 플러그 인입니다. |
| 진단 오버레이                 | [데모](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | 이 플러그 인은 모든 주요 매개 변수, 비디오 통계, 비디오 재생 수명 주기의 모든 이벤트 및 보호된 경우 DRM 보호 정보(예: 키 ID, 라이선스 취득 URL)를 표시합니다.                                                                                                                                                                      |
| 프레임 속도 및 시간 코드 계산기 | [데모](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | 이 플러그 인은 첫 번째 MPEG-DASH 비디오 조각의 `tfhd`/`trun` MP4 Box를 기반으로 하여 비디오 프레임 속도을 계산하고, MPEG-DASH 클라이언트 매니페스트의 시간 단위 값을 구문 분석하며, 플레이어로부터 지정된 절대 시간에 대한 시간 코드를 생성하는 방법을 제공합니다(시간 코드를 고려하여 플레이어에 절대 시간도 제공함). |
| <strike>재생 속도</strike>                      | [데모](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 이 플러그 인을 사용하면 뷰어에서 비디오 속도를 제어할 수 있습니다. *이 기능은 AMP v2.0.0 이상 버전에서 자동으로 사용할 수 있지만, 기본적으로 사용하지 않도록 설정되어 있습니다.* 사용하도록 설정하는 방법에 대해 알아보려면 [여기](https://github.com/Azure-Samples/azure-media-player-samples)에 있는 샘플을 확인하세요. |
| 가리키기 시간 팁                      | [데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | 정확한 시간 검색을 위해 마우스 가리키기의 진행률 표시줄 위에 시간 팁을 표시합니다. *참고: 이 플러그 인은 이미 AMP*에 통합되어 있습니다. 프로그래밍 방식으로 검색하는 데 관심이 있으면 언제든지 자유롭게 살펴보세요.                                                                                                                       |
| 제목 오버레이                       | [데모](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | 구성 가능한 비디오 제목을 화면에 오버레이합니다. |
| 타임라인 표식                    | [데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | 이 플러그 인은 시간 배열에서 수행되며, 해당 시간에서 진행 표시줄 위에 작은 표식을 오버레이합니다. |
| 분석                           | | | |
| Application Insights                | [블로그 게시물](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | 플레이어 메트릭을 추적하고 Power BI에 포팅하여 뷰어의 플레이어 환경을 직관적인 그래픽으로 표현하는 플러그 인입니다. |
| Google Analytics                    | 해당 없음                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Azure Media Player용 Google Analytics 플러그 인 |
| 진단                         | | | |
| 진단 출력                  | [데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | 이 플러그 인은 플레이어에서 진단 배열을 출력하며, 작동 상황을 확인할 수 있도록 데모 링크로 이동하여 JavaScript 콘솔을 엽니다. |
| 간편한 액세스                      | | | |
| 확대                             | [데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | 이 플러그 인은 뷰어에서 콘텐츠를 확대할 수 있도록 플레이어 화면에서 끌 수 있는 확대 배율을 표시합니다. |
| 라이브 캡션                       | [Azure 블로그 게시물](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/), [SubPly 게시물](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | 해당 없음 | *자세한 내용은 게시물을 참조하세요.* Azure Media Player용 라이브 캡션 기반 플러그 인으로 설계된 엔드투엔드 워크플로입니다. 솔루션에 대해 자세히 알아보려면 가장 왼쪽의 링크를 클릭하여 SubPly 사이트로 이동합니다. |
| 바로 가기 키                            | <strike>[데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | 바로 가기 키 플러그 인을 사용하면 뷰어에서 전체 화면의 경우 F, 음소거의 경우 M, 진행률 표시줄 컨트롤의 경우 화살표 키와 같은 일반 플러그 인 조합을 통해 플레이어의 다양한 측면을 제어할 수 있습니다. *참고: 이 플러그 인은 이미 AMP에 통합되었지만 언제든지 리소스로 자유롭게 사용할 수 있습니다.* |
| 사회적                              | | | |
| 공유                               | [데모](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | 이 플러그 인은 뷰어에서 Facebook, Twitter 또는 Linkedin을 통해 시청하고 있는 비디오를 친구와 공유할 수 있도록 공유 단추를 플레이어의 컨트롤 막대에 추가합니다. |

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)