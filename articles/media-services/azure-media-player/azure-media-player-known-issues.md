---
title: 알려진 문제 Azure Media Player
description: 현재 릴리스에는 다음과 같은 알려진 문제가 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727217"
---
# <a name="known-issues"></a>알려진 문제 #

현재 릴리스에는 다음과 같은 알려진 문제가 있습니다.

## <a name="azure-media-player"></a>Azure Media Player ##

- 잘못 구성 된 인코더로 인해 재생 문제가 발생할 수 있음
- 타임 스탬프가 2 ^ 53 이상인 스트림은 재생 문제가 있을 수 있습니다.
  - 완화: 90-kHz 비디오 및 44.1-kHz 오디오 날짜 표시줄 사용
- 사용자 개입 없이 모바일 장치에서 자동으로 실행 되지 않습니다. 플랫폼에 의해 차단 됩니다.
- 가까운 불연속성를 검색 하면 재생 오류가 발생할 수 있습니다.
- 규모가 많은 프레젠테이션을 다운로드 하면 UI가 작동 하지 않을 수 있습니다.
- 프레젠테이션이 종료 된 후에는 동일한 원본을 다시 자동으로 재생할 수 없습니다.
  - 종료 된 후 소스를 재생 하려면 원본을 다시 설정 해야 합니다.
- 빈 매니페스트는 플레이어와 관련 된 문제를 일으킬 수 있습니다.
  - 이 문제는 라이브 스트림을 시작할 때 매니페스트에서 충분 한 청크가 없는 경우 발생할 수 있습니다.
- 재생 위치가 UI seekbar 외부에 있을 수도 있습니다.
- 모든 techs에서 이벤트 순서가 일치 하지 않습니다.
- 버퍼링 된 속성은 techs 간에 일치 하지 않습니다.
- nativeControlsForTouch는 "개체가 속성 또는 메서드 ' setControls '을 지원 하지 않습니다."를 방지 하려면 false (기본값) 여야 합니다.
- 포스터는 이제 절대 url 이어야 합니다.
- 장치의 고대비 모드를 사용 하는 경우 UI에서 사소한 미적 문제가 발생할 수 있습니다.
- 완전히 디코딩된 문자열에서 "%" 또는 "+"를 포함 하는 Url에는 원본을 설정 하는 데 문제가 있을 수 있습니다.

## <a name="ad-insertion"></a>광고 삽입 ##

- 광고는 브라우저에 광고를 설치할 때 (주문형 또는 live)에 삽입 되는 문제가 있을 수 있습니다.
- 모바일 장치에서 광고를 재생 하는 동안 문제가 발생할 수 있습니다.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- 라이브 콘텐츠의 DVR 창에서 콘텐츠가 완료 되 면 타임 라인은 영역을 검색 하거나 프레젠테이션 끝에 도달할 때까지 계속 증가 합니다.
- MSE를 사용 하는 Firefox의 라이브 프레젠테이션에 몇 가지 문제가 있습니다.
- 오디오나 비디오 자산은 AzureHtml5JS 기술을 통해 재생 되지 않습니다.
  - 오디오나 비디오를 사용 하지 않고 자산을 재생 하려는 경우 [Azure Media Services 탐색기 도구](https://aka.ms/amse) 를 사용 하 여 빈 오디오나 비디오를 삽입 하면 됩니다.
    - 자동 오디오를 삽입 하는 방법에 대 한 지침은 [여기](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio) 에서 찾을 수 있습니다.

## <a name="flash"></a>깜박임 ##

- Adobe의 캐싱 논리 버그로 인해 AES 콘텐츠가 Flash 버전 30.0.0.134에서 재생 되지 않습니다. Adobe에서 문제를 해결 하 고 30.0.0.154에서 릴리스 했습니다.
- 기술 및 http 오류 (예: 404 네트워크 시간 초과) 플레이어는 다른 techs 복구 하는 데 더 오래 걸립니다.
- Flashss 기술은 tech가 플레이어를 재생/일시 중지 하지 않도록 비디오 영역을 클릭 합니다.
- 사용자가 플래시를 설치 했지만 사이트에서 해당 파일을 로드할 수 있는 권한을 부여 하지 않는 경우 무한 회전이 발생할 수 있습니다. 이는 플러그 인이 설치 되어 있고 사용 가능한 것으로 인식 하 고 플러그 인이 콘텐츠를 실행 하 고 있다고 생각 하기 때문입니다. JavaScript 코드가 전송 되었지만 브라우저 설정에서 플러그 인을 허용 하 라는 메시지가 표시 될 때까지 플러그 인이 실행 되지 않도록 차단 했습니다. 이는 모든 브라우저에서 발생할 수 있습니다.  

## <a name="silverlight"></a>Silverlight ##

- 누락된 기능
- 기술 및 http 오류 (예: 404 네트워크 시간 초과) 플레이어는 다른 techs 복구 하는 데 더 오래 걸립니다.
- Silverlight를 사용 하 여 Mac에서 Safari와 Firefox를 `"http://` 재생 `https://` 하려면 소스에 대해 또는를 명시적으로 정의 해야 합니다.
- 이 기술에 대 한 API가 없는 경우 일반적으로 null을 반환 합니다.
- 사용자가 플래시를 설치 했지만 사이트에서 해당 파일을 로드할 수 있는 권한을 부여 하지 않는 경우 무한 회전이 발생할 수 있습니다. 이는 플러그 인이 설치 되어 있고 사용 가능한 것으로 인식 하 고 플러그 인이 콘텐츠를 실행 하 고 있다고 생각 하기 때문입니다. JavaScript 코드가 전송 되었지만 브라우저 설정에서 플러그 인을 허용 하 라는 메시지가 표시 될 때까지 플러그 인이 실행 되지 않도록 차단 했습니다. 이는 모든 브라우저에서 발생할 수 있습니다.  

## <a name="native-html5"></a>네이티브 HTML5 ##

- Html5 tech는 첫 번째 set source에 대해 canplaythrough 이벤트만 보냅니다.
- 이 기술에서는 브라우저가 구현한 내용만 지원 합니다.  이 기술에 일부 기능이 없을 수 있습니다.  
- 이 기술에 대 한 API가 없는 경우 일반적으로 null을 반환 합니다.
- 이 기술에 대 한 캡션과 자막에 문제가 있습니다. 이 기술에서는 사용 하거나 사용 하지 못할 수도 있고 표시 되지 않을 수도 있습니다.
- HLS/Html5 기술 시나리오에서 대역폭이 제한 되 면 비디오 없이 오디오가 재생 됩니다.

### <a name="microsoft"></a>Microsoft ###

- 현재 ECMAScript 5와의 비 호환성으로 인해 IE8 재생이 작동 하지 않습니다.
- IE 및 일부 Edge 버전에서는 단추로 탭 이동 하 여 선택 하거나 F/f 핫키를 사용 하 여 전체 화면을 입력할 수 없습니다.

## <a name="google"></a>Google ##

- 동일한 매니페스트의 여러 인코딩 프로필에는 Chrome의 재생 문제가 일부 있지만 권장 되지 않습니다.
- Chrome은 AzureHtml5JS를 사용 하 여 AAC 재생할 수 없습니다. [버그 추적기](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)에 대 한 세부 정보를 따르세요.
- Chrome v58의 경우에는 https://프로토콜을 통해 widevine 콘텐츠를 로드 하 고 재생 해야 합니다. 그렇지 않으면 재생이 실패 합니다.

## <a name="mozilla"></a>Mozilla ##

- 오디오 스트림 스위치를 사용 하려면 AzureHtml5JS를 사용할 때 오디오 스트림이 동일한 코덱 전용 데이터를 포함 해야 합니다. Firefox 플랫폼에는이 작업이 필요 합니다.

## <a name="apple"></a>Apple ##

- Mac의 Safari는 기본적으로 "플러그 인을 절약 하기 위해 플러그 인 중지" 설정을 사용 하 여 기본적으로 절전 모드를 사용 하도록 설정 하 여 Flash 및 Silverlight와 같은 플러그 인이 사용자에 게 선호 되지 않는다고 생각 되는 경우이를 차단 합니다. 이 블록은 플러그 인 존재를 차단 하지 않습니다. 기능만 제공 합니다. 기본 techOrder 경우 재생 하려고 할 때 문제가 발생할 수 있습니다.
  - 완화 1: 비디오 플레이어가 ' front and center ' 인 경우 (문서의 왼쪽 상단 모서리에서 시작 하는 3000 x 3000 픽셀 경계 내에 있는 경우) 계속 재생 됩니다.
  - 완화 2: Safari의 techOrder을 ["azureHtml5JS", "html5"]로 변경 합니다. 이 완화 방법은 Flashss 기술은 기술에서 사용할 수 있는 모든 기능을 얻지 못하는 것입니다.
- Silverlight를 통한 PlayReady 콘텐츠에서 Safari를 재생 하는 동안 문제가 발생할 수 있습니다.
- AES 및 제한 된 토큰 콘텐츠는 iOS 및 이전 Android 장치를 사용 하 여 재생 되지 않습니다.
  - 이 시나리오를 위해 서비스에 프록시를 추가 해야 합니다.
- IOS Phone의 기본 스킨은를 통해 표시 됩니다.
- iPhone 재생은 항상 네이티브 플레이어 전체 화면에서 발생 합니다.
  - 이 인라인이 아닌 재생에서는 캡션을 포함 하 여 기능이 유지 되지 않을 수 있습니다.
- 라이브 프레젠테이션이 종료 되 면 iOS 장치가 프레젠테이션을 제대로 종료 하지 않습니다.
- iOS에서는 DVR 기능을 사용할 수 없습니다.
- ios 오디오 스트림 스위치는 iOS 기본 플레이어 UI로만 수행할 수 있으며 오디오 스트림은 동일한 코덱 전용 데이터를 포함 해야 합니다.
- 이전 버전의 Safari는 라이브 스트림을 재생 하는 문제가 발생할 수 있습니다.

## <a name="older-android"></a>이전 Android ##

- AES 및 제한 된 토큰 콘텐츠는 iOS 및 이전 Android 장치를 사용 하 여 재생 되지 않습니다.
  - 이 시나리오를 위해 서비스에 프록시를 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)