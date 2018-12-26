---
title: API 버전 프로필을 사용 하 여 Azure Stack에서.NET SDK를 사용 하 여 | Microsoft Docs
description: API 버전 프로필을 사용 하 여 Azure Stack의.NET을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: cfebbdb9b88a1de6a05f06e6ed72ebc9cddddcf6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074454"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Azure Stack에서.NET API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack Resource Manager에 대 한.NET SDK를 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. 계산, 네트워킹, 저장소, app services 리소스 공급자 SDK에 포함 하 고 [KeyVault](../../key-vault/key-vault-whatis.md)합니다. .NET SDK는 때마다 프로젝트 솔루션을 다운로드 해야 프로필 정보를 통합 하는 14 NuGet 패키지를 포함 합니다. 하지만 리소스 공급자를 구체적으로 다운로드할 수 있습니다 2018-03-01-하이브리드 또는 2017-03-09-프로필에 대 한 응용 프로그램에 대 한 메모리를 최적화 하기 위해 사용 합니다. 리소스 공급자, API 버전은 각 및 속해 있는 API 프로필의 각 패키지 구성 됩니다. .NET SDK의 API 프로필 전역 Azure 리소스 및 Azure Stack에서 리소스 간에 전환할 수 있도록 하 여 하이브리드 클라우드 개발을 사용 합니다.

## <a name="net-and-api-version-profiles"></a>.NET 및 API 버전 프로필

프로필을 API에는 리소스 공급자 및 API 버전의 조합입니다. 리소스 공급자 패키지의 각 리소스 종류의 최신, 가장 안정적 버전을 얻으려면 API 프로필을 사용할 수 있습니다.

-   최신 버전의 모든 서비스를 사용 하 고 사용 합니다 **최신** 패키지의 프로필입니다. 이 프로필의 일부인 합니다 **은 Microsoft.Azure.Management** NuGet 패키지.

-   Azure Stack 호환 서비스를 사용 하려면 사용 합니다 **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** 하거나 **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** 패키지 있습니다.

    -   각 프로필에 대 한 각 리소스 공급자에 대 한 두 개의 패키지 있습니다.

    -   있는지 확인 합니다 **ResourceProvider** 위의 NuGet 패키지의 부분은 올바른 공급자로 변경 됩니다.

-   서비스의 최신 API 버전을 사용 하려면 사용 합니다 **최신** 특정 NuGet 패키지의 프로필입니다. 예를 들어 사용 하려는 경우는 **최신 API** 계산 서비스를 단독으로 사용 하 여 버전을 **최신** 의 프로필을 **계산** 패키지 합니다. 합니다 **최신** 프로필의 일부인 합니다 **은 Microsoft.Azure.Management** NuGet 패키지.

-   특정 리소스 공급자의 리소스 형식에 대 한 특정 API 버전을 사용 하려면 패키지 내에 정의 된 특정 API 버전을 사용 합니다.

참고 동일한 응용 프로그램 옵션을 모두 조합할 수 있습니다.

## <a name="install-the-azure-net-sdk"></a>Azure.NET SDK를 설치 합니다.

1.  Git을 설치 합니다. 자세한 내용은 [시작-Git 설치][]합니다.

2.  올바른 NuGet 패키지를 설치 하려면 [찾기 및 패키지를 설치 하면][]합니다.

3.  패키지를 설치 해야 하는 사용 하려는 프로필 버전에 따라 달라 집니다. 프로필 버전의 패키지 이름을 다음과 같습니다.

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Visual Studio Code에 대 한 올바른 NuGet 패키지를 설치 하려면 다운로드 링크를 참조 합니다 [NuGet 패키지 관리자 지침][]합니다.

5.  사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만들려면 지침은 [Azure Stack에서 제품에 대 한 구독 만들기][]합니다.

6.  서비스 주체를 만들고 클라이언트 ID 및 클라이언트 암호를 저장 합니다. Azure Stack에 대 한 서비스 주체를 만드는 방법에 지침은 [Azure Stack에 응용 프로그램 액세스를 제공 합니다.][]합니다. 클라이언트 ID 라고도 응용 프로그램 ID 서비스 주체를 만들 때 note 합니다.

7.  구독에서 서비스 주체에 참가자/소유자 역할이 있는지 확인 합니다. 서비스 주체에 역할을 할당 하는 방법에 지침은 [Azure Stack에 응용 프로그램 액세스를 제공 합니다.][]합니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack을 사용 하 여 Azure.NET SDK를 사용 하려면 다음 값을 제공 하며 다음 환경 변수를 사용 하 여 값을 설정 합니다. 환경 변수를 설정 하려면 표 다음에 운영 체제에 대 한 지침을 참조 하세요.

| 값                     | 환경 변수   | 설명                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| 테넌트 ID                 | AZURE_TENANT_ID       | Azure Stack에 값 [ *테 넌 트 ID*][]합니다.                                                                          |
| 클라이언트 ID                 | AZURE_CLIENT_ID       | 주 응용 프로그램 ID 저장이 문서의 이전 섹션에서 서비스 주체를 만들 때 서비스입니다. |
| 구독 ID           | AZURE_SUBSCRIPTION_ID | 합니다 [ *구독 ID* ][] 제품을 액세스 하는 방법에 Azure Stack에서.                                                      |
| 클라이언트 암호             | AZURE_CLIENT_SECRET   | 서비스 주체를 만들 때 저장 서비스 주 응용 프로그램 암호입니다.                                      |
| Resource Manager 끝점 | ARM_ENDPOINT           | 참조 [ *Azure Stack 리소스 관리자 끝점*][]합니다.                                                                    |

Azure Stack에 대 한 테 넌 트 ID를 찾으려면 지침을 따릅니다 [여기](../azure-stack-csp-ref-operations.md)합니다. 환경 변수를 설정 하려면 다음을 수행 합니다.

### <a name="microsoft-windows"></a>Microsoft Windows

Windows 명령 프롬프트에서 환경 변수를 설정 하려면 다음 형식을 사용 합니다.

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux 및 Unix 기반 시스템

Unix 기반 시스템에서 다음 명령을 사용할 수 있습니다.

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack resource manager 끝점

Microsoft Azure 리소스 관리자는 관리자가 배포, 관리 및 Azure 리소스를 모니터링할 수 있도록 관리 합니다. Azure Resource Manager 그룹으로 대신 개별적으로 단일 작업에서 이러한 작업을 처리할 수 있습니다.

Resource Manager 끝점에서 메타 데이터 정보를 가져올 수 있습니다. 코드를 실행 하는 데 필요한 정보를 사용 하 여 JSON 파일을 반환 하는 끝점입니다.

다음 고려 사항을 note 합니다.

- 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: https://management.local.azurestack.external/

- **ResourceManagerUrl** 통합된 시스템의: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

샘플 JSON 파일:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>기존 API 프로필

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: Azure Stack에 대 한 최신 프로필 작성 합니다. 가장 1808 스탬프에는 Azure Stack을 사용 하 여 호환 또는 추가 서비스에 대 한이 프로필을 사용 합니다.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: 1808 빌드 보다 낮은 스탬프를 사용 하는 경우이 프로필을 사용 합니다.

3.  **최신**: 모든 서비스의 최신 버전으로 구성 된 프로필입니다. 모든 서비스의 최신 버전을 사용 합니다. 이 프로필의 일부인 합니다 **은 Microsoft.Azure.Management** NuGet 패키지.

Azure Stack 및 API 프로필에 대 한 자세한 내용은 참조는 [프로필 API 요약][]합니다.

## <a name="azure-net-sdk-api-profile-usage"></a>Azure.NET SDK API 프로필 사용

다음 코드는 프로필 클라이언트를 인스턴스화하려면 사용할 해야 합니다. 이 매개 변수는 Azure Stack 또는 다른 사설 클라우드 필요 합니다. 전역 Azure는 기본적으로 이러한 설정에 이미 있습니다.

다음 코드는 Azure Stack에서 서비스 주체를 인증 해야 합니다. ID 및 Azure Stack에만 해당 되는 인증 자료에는 테 넌 트에서 토큰을 만듭니다.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

이렇게 하면 API 프로필 NuGet 패키지를 사용 하 여 Azure Stack에 성공적으로 응용 프로그램을 배포할 수 있습니다.

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack 환경 설정 함수를 정의 합니다.

Azure Stack 환경에 서비스 주체를 인증 하려면 다음 코드를 사용 하세요.

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

이 Azure Stack에 인증할 수 초기화 서비스 클라이언트를 재정의 합니다.

## <a name="samples-using-api-profiles"></a>API 프로필을 사용 하는 샘플

다음에 있는 샘플 GitHub 리포지토리.NET 및 Azure Stack API 프로필을 사용 하 여 솔루션을 만들기 위한 참조로 사용할 수 있습니다.

-   [가상 머신, vNet, 리소스 그룹 및 저장소 계정에 테스트 프로젝트][]
-   .NET을 사용 하 여 virtual machines 관리

### <a name="sample-unit-test-project"></a>샘플 단위 테스트 프로젝트 

1.  다음 명령을 사용 하 여 리포지토리를 복제 합니다.

    ```shell
    git clone https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm.git
    ```

2.  Azure 서비스 주체 만들기 및 구독에 액세스 하는 역할을 할당 합니다. 서비스 주체를 만드는 방법에 지침은 [Azure PowerShell을 사용 하 여 인증서를 사용 하 여 서비스 주체를 만들려면][]입니다.

3.  다음 값을 검색 합니다.

    1.  테넌트 ID
    2.  클라이언트 ID
    3.  클라이언트 암호
    4.  구독 ID
    5.  Resource Manager 끝점

4.  명령 프롬프트를 사용 하 여 만든 사용자 서비스에서 검색 한 정보를 사용 하 여 다음 환경 변수를 설정 합니다.

    1.  AZURE_TENANT_ID 내보내기 = {테 넌 트 id}
    2.  AZURE_CLIENT_ID 내보내기 = {client id}
    3.  AZURE_CLIENT_SECRET 내보내기 = {클라이언트 암호}
    4.  AZURE_SUBSCRIPTION_ID 내보내기 = {구독 id}
    5.  ARM_ENDPOINT 내보내기 = {에 Azure Stack Resource manager URL}

   Windows를 사용 하 여 **설정할** of **내보내기**합니다.

5.  위치 변수 Azure Stack 위치로 설정 되었는지 확인 합니다. 예를 들어 로컬 = "local".

6.  Azure Stack에 인증할 수 있도록 사용자 지정 로그인 자격 증명을 설정 합니다. 코드의이 부분은 권한 부여 폴더에이 샘플에 포함 되어 있는지를 참고 합니다.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Azure Stack을 사용 하 여 Azure Stack에 인증할 수 초기화 서비스 클라이언트를 재정의 하는 경우 다음 코드를 추가 합니다. 코드의 일부 권한 부여 폴더에이 샘플에 이미 포함 되어 있는지를 참고 합니다.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  NuGet 패키지 관리자를 사용 하 여 "2018-03-01-하이브리드"에 대 한 검색 하 고 계산, 네트워킹, 저장소, KeyVault 및 App Services 리소스 공급자에 대 한이 프로필을 사용 하 여 연관 된 패키지를 설치 합니다.

2.  .Cs 파일에서 각 작업 내에서 Azure Stack과 함께 작동 하는 데 필요한 매개 변수를 설정 합니다. 예제를 다음과 같이 작업에 대 한 `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  각 태스크와 선택에서 마우스 오른쪽 단추로 클릭 **테스트 실행**합니다.

    1.  왼쪽 창에서 녹색 확인 표시가 각 태스크를 지정 된 매개 변수를 따라 성공적으로 생성 된 경고 합니다. 리소스 생성 되었는지 확인 하기 위해 Azure Stack 구독을 확인 하세요.

    2.  단위 테스트를 실행 하는 방법에 대 한 자세한 내용은 참조 하세요. [테스트 탐색기를 사용 하 여 단위 테스트를 실행 합니다.][]

## <a name="next-steps"></a>다음 단계

API 프로필에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Stack에서 API 버전 프로필 관리](azure-stack-version-profiles.md)
- [프로필에서 지 원하는 리소스 공급자 API 버전](azure-stack-profiles-azure-resource-manager-versions.md)

  [시작-Git 설치]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [찾기 및 패키지를 설치 하면]: /nuget/tools/package-manager-ui
  [NuGet 패키지 관리자 지침]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Azure Stack에서 제품에 대 한 구독 만들기]: ../azure-stack-subscribe-plan-provision-vm.md
  [Azure Stack에 응용 프로그램 액세스를 제공 합니다.]: ../azure-stack-create-service-principals.md
  [* 테 넌 트 ID *]: ../azure-stack-identity-overview.md
  [* 구독 ID *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [*는 Azure Stack resource manager 끝점 *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [프로필 API 요약]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [가상 머신, vNet, 리소스 그룹 및 저장소 계정에 테스트 프로젝트]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Azure PowerShell을 사용 하 여 인증서를 사용 하 여 서비스 주체를 만들려면]: ../azure-stack-create-service-principals.md
  [테스트 탐색기를 사용 하 여 단위 테스트를 실행 합니다.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
