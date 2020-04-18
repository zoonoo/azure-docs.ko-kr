---
title: Azure Cosmos DB 데이터에 액세스하기 위해 시스템 할당된 관리되는 ID를 사용하는 방법
description: Azure Cosmos DB의 키에 액세스하도록 Azure Active Directory(Azure AD) 시스템 할당된 관리되는 ID(관리되는 서비스 ID)를 구성하는 방법을 알아봅니다.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641203"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Azure Cosmos DB 데이터에 액세스하려면 시스템 할당된 관리 ID사용

이 문서에서는 [관리되는 ID를](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)사용하여 Azure Cosmos DB 키에 액세스하기 위한 *강력한 키 회전 에 대한 불가지론* 솔루션을 설정합니다. 이 문서의 예제에서는 Azure Functions를 사용하지만 관리되는 ID를 지원하는 모든 서비스를 사용할 수 있습니다. 

Azure Cosmos DB 키를 복사할 필요 없이 Azure Cosmos DB 데이터에 액세스할 수 있는 함수 앱을 만드는 방법을 알아봅니다. 기능 응용 프로그램은 분마다 일어나 수족관 어항의 현재 온도를 기록합니다. 타이머 트리거 함수 앱을 설정하는 방법에 대해 알아보려면 타이머 아티클에 [의해 트리거되는 Azure의 함수 만들기를](../azure-functions/functions-create-scheduled-function.md) 참조하십시오.

시나리오를 단순화하기 위해 이전 온도 문서를 정리하도록 [이미 시간](./time-to-live.md) 라이브 설정이 구성되었습니다. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>함수 앱에 시스템 할당관리 ID 할당

이 단계에서는 함수 앱에 시스템 할당된 관리되는 ID를 할당합니다.

1. Azure [포털에서](https://portal.azure.com/)Azure **Function** 창을 열고 함수 앱으로 이동합니다. 

1. 플랫폼 **기능** > **ID** 탭 열기: 

   ![함수 앱의 플랫폼 기능 및 ID 옵션을 보여주는 스크린샷입니다.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. **ID** 탭에서 시스템 ID **상태를** **켜고** **저장을**선택합니다. **ID** 창은 다음과 같이 표시됩니다.  

   ![시스템 ID 상태를 켜기로 설정한 화면캡처입니다.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Azure 코스모스 계정에 대한 액세스 권한 부여

이 단계에서는 함수 앱의 시스템 할당관리 ID에 역할을 할당합니다. Azure Cosmos DB에는 관리되는 ID에 할당할 수 있는 여러 기본 제공 역할이 있습니다. 이 솔루션에서는 다음 두 가지 역할을 사용합니다.

|기본 제공 역할  |Description  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다. 읽기/쓰기 키를 검색할 수 있습니다. |
|[코스모스 DB 계정 리더](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음. 읽기 키를 검색할 수 있습니다. |

> [!IMPORTANT]
> Azure Cosmos DB의 역할 기반 액세스 제어에 대한 지원은 제어 평면 작업에만 적용됩니다. 데이터 평면 작업은 마스터 키 또는 리소스 토큰을 통해 보호됩니다. 자세한 내용은 데이터 [에 대한 보안 액세스를](secure-access-to-data.md) 참조하세요.

> [!TIP] 
> 역할을 할당할 때 필요한 액세스만 할당합니다. 서비스에서 데이터 읽기만 필요한 경우 **Cosmos DB 계정 리더** 역할을 관리되는 ID에 할당합니다. 최소 권한 액세스의 중요성에 대한 자세한 내용은 권한 있는 [계정노출 감소](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) 문서를 참조하세요.

이 시나리오에서는 함수 앱은 수족관의 온도를 읽은 다음 Azure Cosmos DB의 컨테이너에 해당 데이터를 다시 씁니다. 함수 앱에서 데이터를 작성해야 하므로 **DocumentDB 계정 기여자** 역할을 할당해야 합니다. 

1. Azure 포털에 로그인하고 Azure Cosmos DB 계정으로 이동합니다. **IAM(액세스 컨트롤)** 창을 연 다음 **역할 할당 탭을 엽니다.**

   ![액세스 제어 창 및 역할 할당 탭을 보여 주면 됩니다.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. +**역할 할당** **추가** > 를 선택합니다.

1. **역할 할당 추가** 패널이 오른쪽에서 열립니다.

   ![역할 할당 추가 창을 보여주는 스크린샷입니다.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **역할**: **문서DB 계정 기여자** 선택
   * **에 대한 액세스 할당**: **시스템 할당관리 ID** 하위 섹션에서 함수 **앱을**선택합니다.
   * **선택**: 창은 **관리되는 시스템 ID가**있는 구독의 모든 기능 앱으로 채워집니다. 이 경우 **FishTankTemperatureService** 함수 앱을 선택합니다. 

      ![예제로 채워진 역할 할당 추가 창을 보여주는 스크린샷입니다.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 함수 앱을 선택한 후 **저장을**선택합니다.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Azure 코스모스 DB 키에 프로그래밍 방식으로 액세스

이제 Azure Cosmos DB 사용 권한에서 **DocumentDB 계정 기여자** 역할을 가진 시스템 할당된 관리되는 ID가 있는 함수 앱이 있습니다. 다음 함수 앱 코드는 Azure Cosmos DB 키를 얻고, CosmosClient 개체를 만들고, 수족관의 온도를 이동한 다음 Azure Cosmos DB에 저장합니다.

이 샘플에서는 [목록 키 API를](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) 사용하여 Azure Cosmos DB 계정 키에 액세스합니다.

> [!IMPORTANT] 
> [Cosmos DB 계정 리더](#grant-access-to-your-azure-cosmos-account) 역할을 할당하려면 읽기 전용 키 [API를](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)사용해야 합니다. 이렇게 하면 읽기 전용 키만 채워집니다.

키 목록 API는 `DatabaseAccountListKeysResult` 개체를 반환합니다. 이 형식은 C# 라이브러리에 정의되어 있지 않습니다. 다음 코드는 이 클래스의 구현을 보여 주며 있습니다.  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

이 예제에서는 다음과 같이 정의되는 "온도 기록"이라는 간단한 문서도 사용합니다.

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 라이브러리를 사용하여 시스템 할당된 관리되는 ID 토큰을 가져옵니다. 토큰을 얻고 `Microsoft.Azure.Service.AppAuthentication` 라이브러리에 대한 자세한 내용을 알아보는 다른 방법을 알아보려면 서비스 간 인증 문서를 [참조하세요.](../key-vault/general/service-to-service-authentication.md)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

이제 [함수 앱을 배포할](../azure-functions/functions-create-first-function-vs-code.md)준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 및 Azure Active Directory를 사용 하 인 인증서 기반 인증](certificate-based-authentication.md)
* [Azure 키 볼트를 사용하여 보안 Azure 코스모스 DB 키](access-secrets-from-keyvault.md)
* [Azure 코스모스 DB에 대한 보안 기준](security-baseline.md)
