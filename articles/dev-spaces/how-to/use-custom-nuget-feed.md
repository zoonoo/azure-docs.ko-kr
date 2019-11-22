---
title: Azure Dev Spaces에서 사용자 지정 NuGet 피드를 사용 하는 방법
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Azure Dev Space에서 사용자 지정 NuGet 피드를 사용하여 NuGet 패키지에 액세스하고 이를 사용합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279960"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure Dev Space에서 사용자 지정 NuGet 피드 사용

NuGet 피드는 프로젝트에 패키지 원본을 포함하는 편리한 방법을 제공합니다. Docker 컨테이너에 종속성을 제대로 설치 하려면 Azure Dev Spaces이 피드에 액세스 해야 합니다.

## <a name="set-up-a-nuget-feed"></a>NuGet 피드 설정

`*.csproj` 파일의 `PackageReference` 노드 아래에 종속성에 대 한 [패키지 참조](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) 를 추가 합니다. 예:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

프로젝트 폴더에 [nuget.exe](https://docs.microsoft.com/nuget/reference/nuget-config-file) 파일을 만들고 nuget 피드에 대 한 `packageSources` 및 `packageSourceCredentials` 섹션을 설정 합니다. `packageSources` 섹션에는 AKS 클러스터에서 액세스할 수 있어야 하는 피드 url이 포함 되어 있습니다. 피드에 액세스 하기 위한 자격 증명은 `packageSourceCredentials`입니다. 예:

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

Dockerfiles를 업데이트 하 여 `NuGet.Config` 파일을 이미지에 복사 합니다. 예:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows에서는 `NuGet.Config`, `Nuget.Config`및 `nuget.config` 모두 유효한 파일 이름으로 작동 합니다. Linux에서는 `NuGet.Config`만이 파일에 대 한 올바른 파일 이름입니다. Azure Dev Spaces는 Docker 및 Linux를 사용 하므로이 파일의 이름을 `NuGet.Config`로 지정 해야 합니다. 이름을 수동으로 지정 하거나 `dotnet restore --configfile nuget.config`를 실행 하 여 수정할 수 있습니다.


Git를 사용 하는 경우 버전 제어에서 NuGet 피드에 대 한 자격 증명을 사용 하지 않아야 합니다. `NuGet.Config` 파일이 버전 제어에 추가 되지 않도록 프로젝트의 `.gitignore`에 `NuGet.Config`를 추가 합니다. Azure Dev Spaces는 컨테이너 이미지 빌드 프로세스 중에이 파일이 필요 하지만 기본적으로 `.gitignore` 및 `.dockerignore`에 정의 된 규칙을 동기화 중에 수행 합니다. 기본값을 변경 하 고 Azure Dev Spaces `NuGet.Config` 파일을 동기화 할 수 있도록 허용 하려면 `azds.yaml` 파일을 업데이트 합니다.

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Git를 사용 하지 않는 경우이 단계를 건너뛸 수 있습니다.

다음에 `azds up`를 실행 하거나 Visual Studio Code 또는 Visual Studio에서 `F5`를 누르면 Azure Dev Spaces는 `NuGet.Config` 파일을 동기화 하 여 패키지 종속성을 설치 합니다.

## <a name="next-steps"></a>다음 단계

[NuGet 및 작동 방식](https://docs.microsoft.com/nuget/what-is-nuget)에 대해 자세히 알아보세요.