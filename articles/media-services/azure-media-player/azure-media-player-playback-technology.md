---
title: Azure Media Player 재생 기술
description: 비디오 또는 오디오를 재생하는 데 사용되는 재생 기술에 대해 자세히 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 56fd644e43b704eced4f5a97b82e4b07ab1b4db9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424088"
---
# <a name="playback-technology-tech"></a>재생 기술("기술") #

재생 기술은 비디오 또는 오디오를 재생하는 데 사용되는 특정 브라우저 또는 플러그 인 기술을 나타냅니다.

- **azureHtml5JS**: Azure Media Services에서 AES-128 비트 봉투 암호화된 콘텐츠 또는 DRM 공통 암호화된 콘텐츠(브라우저에서 지원하는 경우 PlayReady 및 Widevine을 통해)를 지원하여 DASH 콘텐츠의 플러그 인 기반이 아닌 재생을 위해 비디오 요소와 함께 MSE 및 EME 표준을 활용합니다.
- **flashSS**: Azure Media Services에서 AES-128 비트 봉투 암호화를 지원하여 Smooth 콘텐츠를 재생하는 Flash Player 기술을 활용합니다. Flash 버전 11.4 이상이 필요합니다.
- **html5FairPlayHLS**: 비디오 요소를 사용하는 HLS를 통해 브라우저 기반 재생 기술 특정의 Safari를 활용합니다. 이 기술은 Azure Media Services에서 FairPlay로 보호된 콘텐츠를 재생해야 하며, 10/19/16부터 techOrder에 추가되었습니다.
- **silverlightSS**: Azure Media Services에서 PlayReady로 보호된 콘텐츠를 지원하여 Smooth 콘텐츠를 재생하는 Silverlight 기술을 활용합니다.
- **html5**: 비디오 요소를 사용하는 브라우저 기반 재생 기술을 활용합니다.  Apple iOS 또는 Android 디바이스를 사용하는 경우 이 기술을 통해 AES-128 비트 봉투 암호화 또는 DRM 콘텐츠(브라우저에서 지원하는 경우 FairPlay를 통해)에 대한 몇 가지 기능을 지원하여 HLS 스트림을 재생할 수 있습니다.

## <a name="tech-order"></a>기술 순서 ##

다양한 디바이스에서 자산을 재생할 수 있도록 하려면 다음 기술 순서가 추천됩니다. 기본값은 `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]`이며, `<video>`에서 직접 설정하거나 옵션에서 프로그래밍 방식으로 설정할 수 있습니다.

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

또는

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>호환성 매트릭스 ##

Azure Media Services에서 콘텐츠를 스트림하는 데 추천되는 기술 순서를 고려할 때 필요한 호환성 재생 매트릭스는 다음과 같습니다.

| 브라우저        | OS                                                       | 필요한 기술(지우기)  | 필요한 기술(AES)  | 필요한 기술(DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS(PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS(PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | 지원되지 않음                |
| Microsoft Edge           | Xbox One<sup>1</sup>(2015년 11월 업데이트)                   | azureHtml5JS           | azureHtml5JS         | 지원되지 않음                |
| Chrome 37 이상     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS(Widevine)      |
| Firefox 47 이상    | Windows 10, Windows 8.1, macOS X Yosemite 이상<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS(Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite 이상<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS(PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | silverlightSS(PlayReady)    |
| Safari         | iOS 6 이상                                                   | html5                  | html5(토큰 없음)3    | 지원되지 않음                |
| Safari 8 이상      | OS X Yosemite 이상                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS(FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS(PlayReady)    |
| Chrome 37 이상     | Android 4.4.4 이상<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS(Widevine)      |
| Chrome 37 이상     | Android 4.02                                             | html5                  | html5(토큰 없음)<sup>3</sup>    | 지원되지 않음                |
| Firefox 42 이상    | Android 5.0 이상<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | 지원되지 않음                |
| IE 8           | Windows                                                  | 지원되지 않음          | 지원되지 않음        | 지원되지 않음                |

<sup>1</sup> 지원되지 않거나 테스트되지 않은 구성입니다. 완료를 위한 참조로 나열됩니다.

<sup>2</sup> Android 디바이스에서 성공적으로 재생하려면 디바이스 기능, 그래픽 지원, 코덱 렌더링, OS 지원 등의 조합이 필요합니다. Android는 Google에서 제공하는 Vanilla Android OS를 휴대폰 제조업체에서 변경할 수 있는 오픈 소스 플랫폼이므로 Android 공간에서 일부 조각화가 발생하며 부족한 기능으로 인해 일부 디바이스가 지원되지 않을 수 있습니다. 또한 일부 Android 디바이스는 모든 코덱을 지원하지 않습니다.  

<sup>3</sup> 토큰을 지원하지 않는 경우 프록시를 사용하여 이 기능을 추가할 수 있습니다. 이 솔루션에 대해 자세히 알아보려면 이 [블로그](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)를 확인하세요.

> [!NOTE]
> 선택한 필요한 기술에서 Flash와 같은 플러그 인을 설치해야 하고, 사용자의 머신에 설치되지 않은 경우 AMP에서 원본 유형 및 보호 정보와 함께 기술 목록상의 다음 기술에 대한 기능을 계속 확인합니다. 예를 들어 OS X Yosemite에서 Safari 8의 보호되지 않은 주문형 스트림을 보려고 하고 Flash 및 Silverlight가 모두 설치되지 않은 경우 AMP는 네이티브 Html5 재생 기술을 선택합니다.<br/><br/>새로운 브라우저 기술이 매일 나타나고 있으므로 이 매트릭스에 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)