---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656728"
---
:::row:::
    :::column span="3":::
        C ++ 음성 SDK는 윈도우에서 사용할 수 있습니다, 리눅스, 맥 OS. 자세한 내용은 <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>를 참조하십시오.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet 패키지

C++ 음성 SDK는 다음 `Install-Package` 명령을 통해 패키지 **관리자에서** 설치할 수 있습니다.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++ 바이너리 및 헤더 파일

또는 바이너리에서 C++ 음성 SDK를 설치할 수 있습니다. SDK를 <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">.tar 패키지로 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 다운로드하고 선택한 디렉토리에 있는 파일의 압축을 풀어줍니다. 이 패키지의 내용(x86 및 x64 대상 아키텍처 모두에 대한 헤더 파일 포함)은 다음과 같이 구성됩니다.

  | 경로                   | 설명                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | License                                              |
  | `ThirdPartyNotices.md` | 타사 알림                                  |
  | `include`              | C++ 용 헤더 파일                                 |
  | `lib/x64`              | 애플리케이션과 연결할 기본 x64 라이브러리 |
  | `lib/x86`              | 애플리케이션과 연결할 기본 x86 라이브러리 |

  애플리케이션을 만들려면 개발 환경에 필수 이진 파일(및 라이브러리)을 복사하고 이동합니다. 필요에 따라 빌드 프로세스에 포함합니다.

#### <a name="additional-resources"></a>추가 자료

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">윈도우, 리눅스, 맥OS 퀵스타트 C++ 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>