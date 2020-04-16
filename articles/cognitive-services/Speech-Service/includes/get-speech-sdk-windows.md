---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399962"
---
:::row:::
    :::column span="3":::
        음성 SDK는 Windows 10 및 Windows 서버 2016 또는 이후 버전을 지원합니다. 이전 버전은 공식적으로 **지원되지 않습니다.** 이전 버전의 Windows와 함께 음성 SDK의 일부를 사용할 수 있지만 권장되지는 않습니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>시스템 요구 사항

Windows의 음성 SDK에는 시스템에서 <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> 2019에 대해 재배포 가능한 Microsoft 시각적 C++ 재배포가</a> 필요합니다.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">x86용 설치<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">x64용 설치<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">ARMx64용 설치<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

마이크 입력을 위해 미디어 파운데이션 라이브러리를 설치해야 합니다. 이러한 라이브러리는 Windows 10 및 Windows Server 2016에 포함됩니다. 마이크를 오디오 입력 디바이스로 사용하지 않는 경우에는 이러한 라이브러리 없이 Speech SDK를 사용할 수 있습니다.

필요한 Speech SDK 파일은 애플리케이션과 동일한 디렉터리에 배포할 수 있습니다. 이렇게 하면 애플리케이션이 라이브러리에 직접 액세스할 수 있습니다. 응용 프로그램과 일치하는 올바른 버전(x86/x64)을 선택해야 합니다.

| 속성                                            | 함수                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 네이티브 및 관리된 배포에 필요한 핵심 SDK |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 관리된 배포에 필요                      |

> [!NOTE]
> 릴리스 1.3.0부터 파일(이전 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 릴리스에서 제공)은 더 이상 필요하지 않습니다. 이제 이 기능은 핵심 SDK에 통합되었습니다.

> [!IMPORTANT]
> Windows Forms 앱(.NET Framework) C# 프로젝트의 경우 라이브러리가 프로젝트의 배포 설정에 포함되어 있는지 확인합니다. 아래에서 `Properties -> Publish Section`확인할 수 있습니다. 단추를 `Application Files` 클릭하고 스크롤 아래로 목록에서 해당 라이브러리를 찾습니다. 값이 `Included`로 설정되어 있는지 확인합니다. Visual Studio에는 프로젝트가 게시/배포될 때 파일이 포함됩니다.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
