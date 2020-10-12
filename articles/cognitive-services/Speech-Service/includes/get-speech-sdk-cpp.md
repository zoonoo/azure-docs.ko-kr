---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81400011"
---
:::row:::
    :::column span="3":::
        C + + Speech SDK는 Windows, Linux 및 macOS에서 사용할 수 있습니다. 자세한 내용은 <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">cognitiveservices account <span class="docon docon-navigate-external x-hidden-focus"></span> </a>를 참조 하세요.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C + + NuGet 패키지

다음 명령을 사용 하 여 **패키지 관리자** 에서 c + + Speech SDK를 설치할 수 있습니다 `Install-Package` .

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C + + 이진 파일 및 헤더 파일

또는 c + + Speech SDK를 이진 파일에서 설치할 수 있습니다. 해당 SDK를 <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">tar 패키지로 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 다운로드 하 고 원하는 디렉터리에서 파일의 압축을 풉니다. X86 및 x64 대상 아키텍처에 대 한 헤더 파일을 포함 하는이 패키지의 내용은 다음과 같이 구성 됩니다.

  | 경로                   | 설명                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | 라이선스                                              |
  | `ThirdPartyNotices.md` | 타사 알림                                  |
  | `include`              | C + + 용 헤더 파일                                 |
  | `lib/x64`              | 애플리케이션과 연결할 기본 x64 라이브러리 |
  | `lib/x86`              | 애플리케이션과 연결할 기본 x86 라이브러리 |

  애플리케이션을 만들려면 개발 환경에 필수 이진 파일(및 라이브러리)을 복사하고 이동합니다. 필요에 따라 빌드 프로세스에 포함합니다.

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux 및 macOS 퀵 스타트 c + + 소스 코드 <span class="docon docon-navigate-external x-hidden-focus"></span></a>