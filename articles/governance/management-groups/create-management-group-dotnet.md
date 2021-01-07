---
title: '빠른 시작: .NET Core를 사용하여 관리 그룹 만들기'
description: 이 빠른 시작에서는 .NET Core를 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 138998fdc23fd8a296ca50093e2952017888041f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91604562"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>빠른 시작: .NET Core를 사용하여 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- _clientId_ 및 _clientSecret_을 포함하는 Azure 서비스 주체. Azure Policy에 사용할 서비스 주체가 없거나 새로 만들려면 [.NET 인증을 위한 Azure 관리 라이브러리](/dotnet/azure/sdk/authentication#mgmt-auth)를 참조하세요.
  .NET Core 패키지를 설치하는 단계는 건너뜁니다. 다음 단계에서 수행할 것입니다.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>애플리케이션 설정

.NET Core를 사용하여 관리 그룹을 관리하려면 새 콘솔 애플리케이션을 만들고 필요한 패키지를 설치합니다.

1. 최신 .NET Core가 설치되었는지 확인합니다(**3.1.8** 이상). 아직 설치되지 않았으면 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core)에서 다운로드합니다.

1. 다음과 같이 "mgCreate"라는 새 .NET Core 콘솔 애플리케이션을 초기화합니다.

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. 디렉터리를 새 프로젝트 폴더로 변경하고 Azure Policy에 필요한 패키지를 설치합니다.

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 기본 `program.cs`를 다음 코드로 바꾸고 업데이트된 파일을 저장합니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. `mgCreate` 콘솔 애플리케이션을 빌드하고 게시합니다.

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>관리 그룹 만들기

이 빠른 시작에서는 루트 관리 그룹에 새 관리 그룹을 만듭니다.

1. 디렉터리를 이전에 `dotnet publish` 명령을 사용하여 정의한 `{run-folder}`로 변경합니다.

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

이 명령은 다음 정보를 사용합니다.

- `{tenantId}` - 테넌트 ID로 대체
- `{clientId}` - 서비스 주체의 클라이언트 ID로 대체
- `{clientSecret}` - 서비스 주체의 클라이언트 암호로 대체
- `{groupID}` - 사용자의 새 관리 그룹 ID로 바꾸기
- `{displayName}` - 사용자의 새 관리 그룹 식별 이름으로 바꾸기

결과적으로 루트 관리 그룹에 새 관리 그룹이 생성됩니다.

## <a name="clean-up-resources"></a>리소스 정리

- 포털을 통해 새 관리 그룹을 삭제합니다.

- .NET Core 콘솔 애플리케이션 및 설치된 패키지를 제거하려면 `mgCreate` 프로젝트 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 그룹으로 리소스 관리](./manage.md)