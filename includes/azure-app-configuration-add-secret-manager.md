---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440416"
---
## <a name="add-secret-manager"></a>비밀 관리자 추가

비밀 관리자라는 도구는 개발 작업에 대한 중요한 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드 내에서 앱 암호를 실수로 공유하는 경우를 방지할 수 있습니다. ASP.NET Core 프로젝트에서 비밀 관리자를 사용하도록 설정하려면 다음 단계를 완료합니다.

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

프로젝트의 루트 디렉터리로 이동하고 다음 명령을 실행하여 프로젝트에서 비밀 스토리지를 사용하도록 설정합니다.

```dotnetcli
dotnet user-secrets init
```

GUID를 포함하는 `UserSecretsId` 요소가 *.csproj* 파일에 추가됩니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. *.csproj* 파일을 엽니다.

1. 여기에 표시된 대로 `UserSecretsId` 요소를 *.csproj* 파일에 추가합니다. 같은 GUID를 사용해도 되고, 이 값을 원하는 값으로 바꿔도 됩니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. *.csproj* 파일을 저장합니다.

---

> [!TIP]
> 비밀 관리자에 대한 자세한 내용은 [ASP.NET Core에서 개발 중인 앱 비밀 보안 스토리지](/aspnet/core/security/app-secrets)를 참조하세요.
