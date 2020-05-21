---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: d9c6062e7106f764058d6c96c6f730d235b2ef0c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673036"
---
MacOS 용으로 개발 하는 경우 세 가지 음성 Sdk를 사용할 수 있습니다.

- 목표-C Speech SDK는 기본적으로 CocoaPod 패키지로 사용할 수 있습니다.
- .NET Speech SDK는 .NET Standard 2.0를 구현 하므로 Xamarin.ios와 함께 사용할 수 있습니다 **.**
- Python Speech SDK는 PyPI 모듈로 사용할 수 있습니다.

> [!TIP]
> Swift와 함께 목표-C Speech SDK를 사용 하는 방법에 대 한 자세한 내용은 <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Swift <span class="docon docon-navigate-external x-hidden-focus"></span> 로 목표-c 가져오기 </a>를 참조 하세요.

### <a name="system-requirements"></a>시스템 요구 사항

- MacOS 버전 10.13 이상

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod 패키지는 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (이상) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> IDE (통합 개발 환경)에서 다운로드 하 여 사용할 수 있습니다. 먼저 <a href="https://aka.ms/csspeech/macosbinary" target="_blank">이진 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> 를 다운로드 </a>합니다. 사용 하기 위해 동일한 디렉터리에서 pod를 추출 하 고, *Podfile* 을 만들고,를 `pod` 로 나열 합니다 `target` .
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.12.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac은 .NET 개발자가 C#을 사용하여 원시 Mac 애플리케이션을 빌드하기 위한 전체 macOS SDK를 제공합니다. 자세한 내용은 <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">xamarin.ios <span class="docon docon-navigate-external x-hidden-focus"></span> </a>를 참조 하세요.
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

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK 퀵 스타트 목표-C 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK 퀵 스타트 Swift 원본 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>