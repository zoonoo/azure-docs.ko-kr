---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020364"
---
확장 번들을 사용할 수 없는 경우 Azure Functions Core Tools를 로컬로 사용하여 프로젝트에 필요한 특정 확장 패키지를 설치할 수 있습니다.

> [!IMPORTANT]
> 확장 번들을 사용하는 함수 앱에는 확장을 명시적으로 설치할 수 없습니다. 확장을 명시적으로 설치하기 전에 *host.json* 에서 `extensionBundle` 섹션을 제거합니다.

다음 항목은 확장을 수동으로 설치해야 하는 몇 가지 이유를 설명합니다.

* 번들에서 사용할 수 없는 특정 버전의 확장에 액세스해야 합니다.
* 번들에서 사용할 수 없는 사용자 지정 확장에 액세스해야 합니다.
* 단일 번들에서 사용할 수 없는 특정 확장 조합에 액세스해야 합니다.

> [!NOTE]
> Core Tools를 사용하여 수동으로 확장을 설치하려면 [.NET Core 2.x SDK](https://dotnet.microsoft.com/download)가 설치되어 있어야 합니다. .NET Core SDK는 Azure Functions Core Tools에서 NuGet의 확장을 설치하는 데 사용됩니다. Azure Functions 확장을 사용하기 위해 .NET을 알 필요는 없습니다.

확장을 명시적으로 설치하면 extensions.csproj라는 .NET 프로젝트 파일이 프로젝트의 루트에 추가됩니다. 이 파일은 함수에 필요한 NuGet 패키지 세트를 정의합니다. 이 파일에서 [NuGet 패키지 참조](/nuget/consume-packages/package-references-in-project-files)로 작업할 수 있지만 Core Tools를 사용하면 파일을 수동으로 편집하지 않고도 확장을 설치할 수 있습니다.

Core Tools를 사용하여 로컬 프로젝트에 필요한 확장을 설치하는 방법에는 여러 가지가 있습니다. 

#### <a name="install-all-extensions"></a>모든 확장 설치 

다음 명령을 사용하여 로컬 프로젝트의 바인딩에서 사용하는 모든 확장 패키지를 자동으로 추가합니다.

```dotnetcli
func extensions install
```
이 명령은 *function.json* 파일을 읽고 필요한 패키지를 확인하고 설치한 후 확장 프로젝트(extensions.csproj)를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다.

함수 앱에서 Core Tools가 인식하지 못하는 바인딩을 사용하는 경우 수동으로 특정 확장을 설치해야 합니다.

#### <a name="install-a-specific-extension"></a>특정 확장 설치

다음 명령을 사용하여 특정 버전(이 경우 Storage 확장)에서 특정 확장 패키지를 설치합니다.

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
