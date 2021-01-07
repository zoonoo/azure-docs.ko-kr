---
title: '빠른 시작: .NET Core를 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 .NET Core를 사용하여 미준수 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91604579"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>빠른 시작: .NET Core를 사용하여 미준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만듭니다. 완료되면 _비준수_ 가상 머신을 식별하게 됩니다.

.NET Core 라이브러리는 Azure 리소스를 관리하는 데 사용됩니다. 이 가이드에서는 Azure Policy에 대한 .NET Core 라이브러리를 사용하여 정책 할당을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- _clientId_ 및 _clientSecret_을 포함하는 Azure 서비스 주체. Azure Policy에 사용할 서비스 주체가 없거나 새로 만들려면 [.NET 인증을 위한 Azure 관리 라이브러리](/dotnet/azure/sdk/authentication#mgmt-auth)를 참조하세요.
  .NET Core 패키지를 설치하는 단계는 건너뜁니다. 다음 단계에서 수행할 것입니다.

## <a name="create-the-azure-policy-project"></a>Azure Policy 프로젝트 만들기

.NET Core를 사용하여 Azure Policy를 관리하려면 새 콘솔 애플리케이션을 만들고 필요한 패키지를 설치합니다.

1. 최신 .NET Core가 설치되었는지 확인합니다(**3.1.8** 이상). 아직 설치되지 않았으면 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core)에서 다운로드합니다.

1. 다음과 같이 "policyAssignment"라는 새 .NET Core 콘솔 애플리케이션을 초기화합니다.

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. 디렉터리를 새 프로젝트 폴더로 변경하고 Azure Policy에 필요한 패키지를 설치합니다.

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. `policyAssignment` 콘솔 애플리케이션을 빌드하고 게시합니다.

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사**(`06a78e20-9358-41c9-923c-fb736d382a4d`) 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

1. 디렉터리를 이전에 `dotnet publish` 명령을 사용하여 정의한 `{run-folder}`로 변경합니다.

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

이 명령은 다음 정보를 사용합니다.

- `{tenantId}` - 테넌트 ID로 대체
- `{clientId}` - 서비스 주체의 클라이언트 ID로 대체
- `{clientSecret}` - 서비스 주체의 클라이언트 암호로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체
- **name** – 정책 할당 개체의 고유한 이름입니다. 위의 예제에서는 _audit-vm-manageddisks_를 사용합니다.
- **displayName** - 정책 할당의 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다.
- **policyDefID** – 할당을 만드는 데 기준으로 사용되는 정책 정의 경로입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다.
- **설명** - 정책에서 수행하는 작업 또는 이 범위에 할당된 이유에 대한 자세한 설명입니다.
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 관리 그룹에서 개별 리소스에 이르기까지 다양할 수 있습니다. `{scope}`를 다음 패턴 중 하나로 바꾸어야 합니다.
  - 관리 그룹: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 구독: `/subscriptions/{subscriptionId}`
  - 리소스 그룹: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 리소스: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

이제 비규격 리소스를 식별하여 환경의 규정 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>비규격 리소스 식별

이제 정책 할당이 생성되었으므로 미준수 리소스를 식별할 수 있습니다.

1. 다음과 같이 "policyCompliance"라는 새 .NET Core 콘솔 애플리케이션을 초기화합니다.

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. 디렉터리를 새 프로젝트 폴더로 변경하고 Azure Policy에 필요한 패키지를 설치합니다.

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. `policyAssignment` 콘솔 애플리케이션을 빌드하고 게시합니다.

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. 디렉터리를 이전에 `dotnet publish` 명령을 사용하여 정의한 `{run-folder}`로 변경합니다.

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

이 명령은 다음 정보를 사용합니다.

- `{tenantId}` - 테넌트 ID로 대체
- `{clientId}` - 서비스 주체의 클라이언트 ID로 대체
- `{clientSecret}` - 서비스 주체의 클라이언트 암호로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체
- **name** – 정책 할당 개체의 고유한 이름입니다. 위의 예제에서는 _audit-vm-manageddisks_를 사용합니다.

`response`의 결과는 Azure Portal 보기에서 정책 할당의 **리소스 규정 준수** 탭에 보이는 것과 일치합니다.

## <a name="clean-up-resources"></a>리소스 정리

- 포털을 통해 _관리 디스크 할당을 사용하지 않는 VM 감사_ 정책 할당을 삭제합니다. 이 정책 정의는 기본 제공되는 정의이므로 제거할 정의가 없습니다.

- .NET Core 콘솔 애플리케이션 및 설치된 패키지를 제거하려면 `policyAssignment` 및 `policyCompliance` 프로젝트 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하기 위해 정책 정의를 할당하는 방법에 대해 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)
