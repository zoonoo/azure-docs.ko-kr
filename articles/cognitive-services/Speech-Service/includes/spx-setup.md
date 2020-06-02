---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800689"
---
## <a name="prerequisites"></a>사전 요구 사항

유일한 필수 구성 요소는 Azure Speech 구독입니다. 새 구독을 아직 만들지 않은 경우 [가이드](../get-started.md#new-resource)를 참조하세요.

## <a name="download-and-install"></a>다운로드 및 설치

#### <a name="windows-install"></a>[Windows 설치](#tab/windowsinstall)

Windows에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) 또는 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)을 설치합니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. 다운로드에서 추출한 루트 디렉터리 `spx-zips`로 이동하여 필요한 하위 디렉터리(.NET Framework 4.7에 대해 `spx-net471` 또는 x64 CPU에서 .NET Core 3.0에 대해 `spx-netcore-win-x64`)를 추출합니다.

명령 프롬프트에서 디렉터리를 이 위치로 변경한 다음, `spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

#### <a name="linux-install"></a>[Linux 설치](#tab/linuxinstall)

x64 CPU에서 Linux에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)을 설치합니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. 다운로드에서 추출한 루트 디렉터리 `spx-zips`로 이동하여 `spx-netcore-30-linux-x64`를 새 `~/spx` 디렉터리로 추출합니다.
4. 터미널에서 다음 명령을 입력합니다.
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

`spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

***

## <a name="create-subscription-config"></a>구독 구성 만들기

Speech CLI 사용을 시작하려면 먼저 Speech 구독 키 및 지역 정보를 입력해야 합니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요. 구독 키와 지역 식별자가 있으면(예: `eastus`, `westus`) 다음 명령을 실행합니다.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

구독 인증은 이제 향후 SPX 요청에 대해 저장됩니다. 이러한 저장된 값 중 하나를 제거해야 하는 경우 `spx config @region --clear` 또는 `spx config @key --clear`를 실행합니다.
