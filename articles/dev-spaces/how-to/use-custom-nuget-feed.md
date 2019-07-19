---
title: Azure Dev Spaces에서 사용자 지정 NuGet 피드를 사용 하는 방법
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Azure Dev Space에서 사용자 지정 NuGet 피드를 사용하여 NuGet 패키지에 액세스하고 이를 사용합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305392"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure Dev Space에서 사용자 지정 NuGet 피드 사용

NuGet 피드는 프로젝트에 패키지 원본을 포함하는 편리한 방법을 제공합니다. Docker 컨테이너에 종속성을 제대로 설치 하려면 Azure Dev Spaces이 피드에 액세스 해야 합니다.

## <a name="set-up-a-nuget-feed"></a>NuGet 피드 설정

노드의 파일에 종속성에 대 한 [패키지 참조](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` `PackageReference` 를 추가 합니다. 예를 들어:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

프로젝트 폴더에 [nuget.exe](https://docs.microsoft.com/nuget/reference/nuget-config-file) 파일을 만들고 nuget 피드에 대 한 `packageSources` 및 `packageSourceCredentials` 섹션을 설정 합니다. 섹션 `packageSources` 은 공개적으로 액세스할 수 있어야 하는 피드 url을 포함 합니다. 는 `packageSourceCredentials` 피드에 액세스 하기 위한 자격 증명입니다. 예를 들어:

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

Dockerfiles를 업데이트 하 여 `NuGet.Config` 파일을 이미지에 복사 합니다. 예를 들어:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows `NuGet.Config` `nuget.config` 에서, 및는 모두 유효한 파일 이름으로 작동 합니다. `Nuget.Config` Linux `NuGet.Config` 에서이 파일에 대 한 올바른 파일 이름입니다. Azure Dev Spaces는 Docker 및 Linux를 사용 하므로이 파일의 이름을 `NuGet.Config`로 지정 해야 합니다. 이름을 수동으로 또는를 실행 `dotnet restore --configfile nuget.config`하 여 수정할 수 있습니다.


Git를 사용 하는 경우 버전 제어에서 NuGet 피드에 대 한 자격 증명을 사용 하지 않아야 합니다. 파일이 버전 제어에 추가 되지 않도록 프로젝트 `NuGet.Config` 의에를추가합니다.`.gitignore` `NuGet.Config` 컨테이너 이미지 빌드 프로세스 중에는이 파일이 필요 하지만 기본적으로는 동기화 하는 동안 및 `.gitignore` `.dockerignore` 에 정의 된 규칙을 Azure Dev Spaces 합니다. 기본값을 변경 하 고 Azure Dev Spaces `NuGet.Config` 파일을 동기화 할 수 있도록 허용 하려면 파일을 업데이트 합니다. `azds.yaml`

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Git를 사용 하지 않는 경우이 단계를 건너뛸 수 있습니다.

다음에 Visual Studio Code 또는 Visual `azds up` Studio에서 `F5` 실행 되거나 적중 될 때 `NuGet.Config` 파일을 동기화 하면 패키지 종속성을 설치 하는 데 사용 Azure Dev Spaces.

## <a name="next-steps"></a>다음 단계

[NuGet 및 작동 방식](https://docs.microsoft.com/nuget/what-is-nuget)에 대해 자세히 알아보세요.