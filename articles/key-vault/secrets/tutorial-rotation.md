---
title: Azure Key Vault에 저장된 단일 인증 자격 증명 세트가 있는 리소스에 대한 회전 자습서
description: 이 자습서를 사용하여 단일 인증 자격 증명 세트를 사용하는 리소스의 비밀을 자동으로 순환하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 5adc2a91df5d394fbed3ff10b0ebc5cb543a3ba3
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378018"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>단일 인증 자격 증명 세트를 사용하는 리소스의 비밀 순환 자동화

Azure 서비스를 인증하는 가장 좋은 방법은 [관리 ID](../general/authentication.md)를 사용하는 것이지만, 이 방법을 사용할 수 없는 시나리오도 있습니다. 이러한 경우 액세스 키 또는 비밀이 사용됩니다. 액세스 키 또는 비밀은 정기적으로 순환해야 합니다.

이 자습서에서는 단일 인증 자격 증명 세트를 사용하는 데이터베이스 및 서비스의 비밀을 정기적으로 자동 순환하는 방법을 보여줍니다. 특히 이 자습서에서는 Azure Event Grid 알림에 의해 트리거되는 함수를 사용하여 Azure Key Vault에 저장된 SQL 서버 암호를 순환합니다.

![순환 솔루션 다이어그램](../media/rotate1.png)

1. 비밀 만료 30일 전에 Key Vault는 "만료 임박" 이벤트를 Event Grid에 게시합니다.
1. Event Grid는 이벤트 구독을 확인하고 HTTP POST를 사용하여 이벤트를 구독하는 함수 앱 엔드포인트를 호출합니다.
1. 함수 앱에서는 비밀 정보를 수신하고, 임의의 새 암호를 생성하고, Key Vault의 새 암호를 사용하여 새 버전의 비밀을 만듭니다.
1. 함수 앱에서는 SQL Server를 새 암호로 업데이트합니다.

> [!NOTE]
> 3단계와 4단계 사이에 시간 지연이 있을 수 있습니다. 이 시간 동안 Key Vault의 비밀은 SQL Server를 인증할 수 없습니다. 특정 단계가 실패하면 Event Grid에서는 2시간 동안 다시 시도합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* SQL Server

기존 키 자격 증명 모음 및 SQL Server가 없는 경우 아래의 배포 링크를 사용할 수 있습니다.

[!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **리소스 그룹**에서 **새로 만들기**를 선택합니다. 그룹 이름을 **akvrotation**로 지정합니다.
1. **Sql 관리자 로그인**에서 Sql 관리자 로그인 이름을 입력합니다. 
1. **검토 + 만들기**를 선택합니다.
1. **만들기**

    ![리소스 그룹 만들기](../media/rotate2.png)

이제 키 자격 증명 모음 및 SQL Server 인스턴스가 있습니다. Azure CLI에서 다음 명령을 실행하여 이 설정을 확인할 수 있습니다.

```azurecli
az resource list -o table
```

결과는 다음 출력과 비슷합니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Sql server 암호 순환 함수 만들기 및 배포

다음으로, 시스템 관리 ID와 기타 필수 구성 요소를 사용하여 함수 앱을 만들고, Sql server 암호 순환 함수를 배포합니다.

함수 앱에는 다음 구성 요소가 필요합니다.
- Azure App Service 계획
- 이벤트 트리거와 http 트리거를 통해 Sql 암호 순환 함수를 사용하는 함수 앱 
- 함수 앱 트리거 관리에 필요한 스토리지 계정
- 함수 앱 ID가 키 자격 증명 모음의 비밀에 액세스하는 액세스 정책
- **SecretNearExpiry** 이벤트에 대한 EventGrid 이벤트 구독

1. Azure 템플릿 배포 링크를 선택합니다. 

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. **리소스 그룹** 목록에서 **akvrotation**을 선택합니다.
1. **Sql Server 이름**에서 순환할 암호가 있는 Sql Server 이름을 입력합니다.
1. **Key Vault 이름**에 키 자격 증명 모음 이름을 입력합니다.
1. **함수 앱 이름**에 함수 앱 이름을 입력합니다.
1. **비밀 이름**에 암호가 저장될 비밀 이름을 입력합니다.
1. **리포지토리 Url**에 함수 코드 GitHub 위치( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git** )를 입력합니다.
1. **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택합니다.

   ![검토+만들기 선택](../media/rotate3.png)

위의 단계가 완료되면 스토리지 계정, 서버 팜 및 함수 앱이 만들어집니다. Azure CLI에서 다음 명령을 실행하여 이 설정을 확인할 수 있습니다.

```azurecli
az resource list -o table
```

결과는 다음 출력과 비슷합니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

함수 앱을 만들고 관리 ID를 사용하여 Key Vault에 액세스하는 방법에 대한 자세한 내용은 [Azure Portal에서 함수 앱 만들기](/azure/azure-functions/functions-create-function-app-portal), [App Service 및 Azure Functions에 관리 ID를 사용하는 방법](/azure/app-service/overview-managed-identity) 및 [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-portal.md)을 참조하세요.

### <a name="rotation-function"></a>순환 함수
이전 단계에서 배포된 함수는 이벤트를 사용하여 Key Vault 및 SQL 데이터베이스를 업데이트하는 방식으로 비밀의 순환을 트리거합니다. 

#### <a name="function-trigger-event"></a>함수 트리거 이벤트

다음 함수는 이벤트 데이터를 읽고 순환 논리를 실행합니다.

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>비밀 순환 논리
다음 순환 메서드는 비밀에서 데이터베이스 정보를 읽고, 새 버전의 비밀을 만들고, 데이터베이스를 새 비밀로 업데이트합니다.

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
전체 코드는 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp)에서 확인할 수 있습니다.

## <a name="add-the-secret-to-key-vault"></a>Key Vault에 비밀 추가
사용자에게 *비밀 관리* 권한을 부여하도록 액세스 정책을 설정합니다.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

SQL Server 리소스 ID, SQL Server 로그인 이름 및 비밀의 유효 기간(일)이 포함된 태그를 사용하여 새 비밀을 만듭니다. 비밀 이름과 SQL 데이터베이스(이 예에서는 "Simple123")의 초기 암호를 제공하고 내일 설정된 만료 날짜를 포함합니다.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

만료 날짜가 짧은 비밀을 만들면 `SecretNearExpiry` 이벤트가 15분 내에 게시되고, 이어서 비밀을 순환하는 함수가 트리거됩니다.

## <a name="test-and-verify"></a>테스트 및 확인

비밀이 순환되었는지 확인하려면 **Key Vault** > **비밀**로 이동합니다.

![비밀로 이동](../media/rotate8.png)

**sqlPassword** 비밀을 열고, 원래 버전과 순환된 버전을 확인합니다.

![sqluser 비밀 열기](../media/rotate9.png)

### <a name="create-a-web-app"></a>웹앱 만들기

SQL 자격 증명을 확인하려면 웹앱을 만듭니다. 이 웹앱은 Key Vault에서 비밀을 가져오고, 비밀에서 SQL 데이터베이스 정보 및 자격 증명을 추출하고, SQL Server에 대한 연결을 테스트합니다.

웹앱에는 다음 구성 요소가 필요합니다.
- 시스템 관리 ID를 사용하는 웹앱
- 웹앱 관리 ID를 통해 Key Vault의 비밀에 액세스하는 액세스 정책

1. Azure 템플릿 배포 링크를 선택합니다. 

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json)

1. **akvrotation** 리소스 그룹을 선택합니다.
1. **Sql Server 이름**에서 순환할 암호가 있는 Sql Server 이름을 입력합니다.
1. **Key Vault 이름**에 키 자격 증명 모음 이름을 입력합니다.
1. **비밀 이름**에 암호가 저장된 비밀 이름을 입력합니다.
1. **리포지토리 Url**에 웹앱 코드 GitHub 위치( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )를 입력합니다.
1. **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택합니다.


### <a name="open-the-web-app"></a>웹앱 열기

배포된 애플리케이션 URL로 이동합니다.
 
https://akvrotation-app.azurewebsites.net/

애플리케이션이 브라우저에서 열리면 **생성된 비밀 값** 및 **데이터베이스 연결** 값이 *true*로 표시됩니다.

## <a name="learn-more"></a>자세한 정보

- 자습서: [두 개의 자격 증명 세트가 있는 리소스에 대한 순환](tutorial-rotation-dual.md)
- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](../general/event-grid-overview.md)
- 방법: [키 자격 증명 모음 비밀 변경 시 이메일 받기](../general/event-grid-logicapps.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../../event-grid/event-schema-key-vault.md)
