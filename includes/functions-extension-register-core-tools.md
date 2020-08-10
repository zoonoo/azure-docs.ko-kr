---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031129"
---
확장 번들을 사용할 수 없는 경우 Azure Functions Core Tools를 로컬로 사용 하 여 프로젝트에 필요한 특정 확장 패키지를 설치할 수 있습니다. 

> [!NOTE]
> 핵심 도구를 사용 하 여 확장을 수동으로 설치 하려면 .NET Core 2.x SDK가 설치 되어 있어야 합니다.

확장을 명시적으로 설치 하면 확장명이 .csproj 인 .NET 프로젝트 파일이 프로젝트의 루트에 추가 됩니다. 이 파일은 함수에 필요한 NuGet 패키지 집합을 정의 합니다. 이 파일에서 [NuGet 패키지 참조](/nuget/consume-packages/package-references-in-project-files) 를 사용할 수 있지만, 핵심 도구를 사용 하면 파일을 수동으로 편집 하지 않고도 확장을 설치할 수 있습니다.

핵심 도구를 사용 하 여 로컬 프로젝트에 필요한 확장을 설치 하는 방법에는 여러 가지가 있습니다. 

#### <a name="install-all-extensions"></a>모든 확장 설치 

다음 명령을 사용 하 여 로컬 프로젝트의 바인딩에서 사용 하는 모든 확장 패키지를 자동으로 추가 합니다.

```dotnetcli
func extensions install
```
이 명령은 파일 *의function.js* 를 읽어 필요한 패키지를 확인 하 고, 설치 하 고, 확장 프로젝트 (.csproj)를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다.

#### <a name="install-a-specific-extension"></a>특정 확장 설치

다음 명령을 사용 하 여 특정 버전 (이 경우 저장소 확장)에 특정 확장 패키지를 설치 합니다.

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```