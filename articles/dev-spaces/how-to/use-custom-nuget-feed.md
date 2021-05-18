---
title: 사용자 지정 NuGet 피드 사용
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Azure Dev Space에서 사용자 지정 NuGet 피드를 사용하여 NuGet 패키지에 액세스하고 이를 사용합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960221"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Azure Dev Spaces에서 사용자 지정 NuGet 피드 사용

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

NuGet 피드는 프로젝트에 패키지 원본을 포함하는 편리한 방법을 제공합니다. 종속성이 Docker 컨테이너에 제대로 설치되려면 Azure Dev Spaces에서 이 피드에 액세스해야 합니다.

## <a name="set-up-a-nuget-feed"></a>NuGet 피드 설정

`PackageReference` 노드의 `*.csproj` 파일에서 종속성의 [패키지 참조](/nuget/consume-packages/package-references-in-project-files)를 추가합니다. 예를 들면 다음과 같습니다.

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

프로젝트 폴더에서 [NuGet.Config](/nuget/reference/nuget-config-file) 파일을 만들고 NuGet 피드의 `packageSources` 및 `packageSourceCredentials` 섹션을 설정합니다. `packageSources` 섹션에는 AKS 클러스터에서 액세스할 수 있어야 하는 피드 URL이 포함됩니다. `packageSourceCredentials`는 피드에 액세스하기 위한 자격 증명입니다. 예를 들면 다음과 같습니다.

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

`NuGet.Config` 파일을 이미지에 복사하도록 Dockerfile을 업데이트합니다. 예를 들면 다음과 같습니다.

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows에서 `NuGet.Config`, `Nuget.Config`, `nuget.config`는 모두 유효한 파일 이름으로 작동합니다. Linux에서 이 파일의 유효한 파일 이름은 `NuGet.Config`뿐입니다. Azure Dev Spaces는 Docker 및 Linux를 사용하므로 이 파일의 이름을 `NuGet.Config`로 지정해야 합니다. 이름을 수동으로 수정하거나 `dotnet restore --configfile nuget.config`를 실행하여 수정할 수 있습니다.


Git을 사용하는 경우 버전 제어에서 NuGet 피드의 자격 증명을 사용하면 안 됩니다. `NuGet.Config` 파일이 버전 제어에 추가되지 않도록 프로젝트의 `.gitignore`에 `NuGet.Config`를 추가합니다. 이 파일은 Azure Dev Spaces에서 컨테이너 이미지 빌드 프로세스 중에 필요하지만 기본적으로 동기화 중에는 `.gitignore` 및 `.dockerignore`에서 정의된 규칙이 적용됩니다. 기본값을 변경하여 Azure Dev Spaces에서 `NuGet.Config` 파일을 동기화할 수 있도록 하려면 `azds.yaml` 파일을 업데이트합니다.

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Git을 사용하지 않는 경우에는 이 단계를 건너뛰어도 됩니다.

다음에 Visual Studio Code 또는 Visual Studio에서 `azds up`을 실행하거나 `F5` 키를 누르면 Azure Dev Spaces에서 `NuGet.Config` 파일을 동기화하고 사용하여 패키지 종속성을 설치합니다.

## <a name="next-steps"></a>다음 단계

[NuGet 및 작동 방식](/nuget/what-is-nuget)을 자세히 알아봅니다.
