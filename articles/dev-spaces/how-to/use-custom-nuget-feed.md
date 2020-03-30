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
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325736"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Azure 개발자 공간에서 사용자 지정 NuGet 피드 사용

NuGet 피드는 프로젝트에 패키지 원본을 포함하는 편리한 방법을 제공합니다. 종속성이 Docker 컨테이너에 제대로 설치되려면 Azure 개발자 공간에서 이 피드에 액세스해야 합니다.

## <a name="set-up-a-nuget-feed"></a>NuGet 피드 설정

노드 아래의 파일에 종속성에 대한 `*.csproj` 패키지 `PackageReference` [참조를](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) 추가합니다. 예를 들어:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

프로젝트 폴더에 [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) 파일을 만들고 `packageSources` NuGet 피드의 및 `packageSourceCredentials` 섹션을 설정합니다. 섹션에는 `packageSources` AKS 클러스터에서 액세스할 수 있어야 하는 피드 URL이 포함되어 있습니다. 는 `packageSourceCredentials` 피드에 액세스하기 위한 자격 증명입니다. 예를 들어:

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

Dockerfiles를 업데이트하여 `NuGet.Config` 파일을 이미지에 복사합니다. 예를 들어:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows에서 `NuGet.Config`, `Nuget.Config`및 `nuget.config` 모든 유효한 파일 이름으로 작동합니다. Linux에서는 이 `NuGet.Config` 파일에 대한 유효한 파일 이름만 있습니다. Azure 개발자 공간은 Docker 및 Linux를 `NuGet.Config`사용하므로 이 파일의 이름을 지정해야 합니다. 수동으로 또는 실행하여 `dotnet restore --configfile nuget.config`이름을 수정할 수 있습니다.


Git을 사용하는 경우 버전 컨트롤에서 NuGet 피드에 대한 자격 증명이 없어야 합니다. 파일이 `.gitignore` 버전 컨트롤에 추가되지 않도록 프로젝트에 추가합니다. `NuGet.Config` `NuGet.Config` Azure 개발자 공간 컨테이너 이미지 빌드 프로세스 중에 이 파일이 필요하지만 기본적으로 `.gitignore` `.dockerignore` 동기화 중에 정의된 규칙을 따릅니다. 기본값을 변경하고 Azure 개발자 공간에서 `NuGet.Config` 파일을 동기화할 수 `azds.yaml` 있도록 하려면 파일을 업데이트합니다.

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Git을 사용하지 않는 경우 이 단계를 건너뛸 수 있습니다.

다음에 Visual Studio `azds up` 코드 `F5` 또는 Visual Studio에서 실행하거나 적중할 때 `NuGet.Config` Azure 개발자 공간은 패키지 종속성을 설치하는 데 사용하는 파일을 동기화합니다.

## <a name="next-steps"></a>다음 단계

[NuGet 및 작동 방식에](https://docs.microsoft.com/nuget/what-is-nuget)대해 자세히 알아보십시오.