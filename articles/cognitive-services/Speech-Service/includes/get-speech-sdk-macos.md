---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 18a2e5118ab8ab30de5cc4dbf75342cc5275256c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656519"
---
macOS용으로 개발할 때 세 가지 음성 SDK를 사용할 수 있습니다.

- Objective-C 스피치 SDK는 기본적으로 CocoaPod 패키지로 제공됩니다.
- .NET 음성 SDK는 .NET 표준 2.0을 구현할 때 **Xamarin.Mac과** 함께 사용할 수 있습니다.
- 파이썬 스피치 SDK는 PyPI 모듈로 사용할 수 있습니다.

> [!TIP]
> Swift를 사용하여 Objective-C 음성 SDK를 사용하는 자세한 내용은 <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C를 <span class="docon docon-navigate-external x-hidden-focus"> </span>Swift로 가져오기 </a>를 참조하십시오.

### <a name="system-requirements"></a>시스템 요구 사항

- macOS 버전 10.13 이상

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS CocoaPod 패키지는 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1(이상) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 통합 개발 환경(IDE)과 함께 다운로드하여 사용할 수 있습니다. 먼저, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">바이너리 <span class="docon docon-navigate-external x-hidden-focus"> </span>코코아팟을 다운로드 </a>. 의도된 용도로 동일한 디렉토리에서 포드를 추출하고 *Podfile을* 만들고 를 `pod` 로 나열합니다. `target`
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[자마린.맥](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac은 .NET 개발자가 C#을 사용하여 원시 Mac 애플리케이션을 빌드하기 위한 전체 macOS SDK를 제공합니다. 자세한 내용은 <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>을 참조하십시오.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>추가 자료

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">맥OS 스피치 SDK 퀵스타트 목표-C 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">맥OS 음성 SDK 빠른 시작 스위프트 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>