---
title: 단일 사용자/암호 회전 자습서
description: 이 자습서를 사용하여 단일 사용자/암호의 회전을 자동화합니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223360"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>단일 사용자/암호 인증을 사용하여 리소스에 대한 비밀 회전 자동화

Azure 서비스를 인증하는 가장 좋은 방법은 [관리 ID](managed-identity.md)를 사용하는 것이지만, 이 방법을 옵션으로 선택할 수 없는 시나리오도 있습니다. 이러한 경우 액세스 키 또는 비밀이 사용됩니다. 액세스 키 또는 비밀은 정기적으로 회전해야 합니다.

이 자습서에서는 단일 사용자/암호 인증을 사용하여 데이터베이스 및 서비스에 대한 비밀 회전을 정기적으로 자동화하는 방법을 보여 줍니다. 특히 이 시나리오는 Event Grid 알림에서 트리거된 함수를 사용하여 키 자격 증명 모음에 저장된 SQL 서버 암호를 회전시킵니다.

![회전 다이어그램](./media/rotate1.png)

1. Key Vault에서 비밀의 만료 날짜 30일 전에 "곧 만료" 이벤트를 Event Grid에 게시합니다.
1. Event Grid에서 이벤트 구독을 확인하고 http post를 사용하여 이 이벤트를 구독하는 함수 앱 엔드포인트를 호출합니다.
1. 함수 앱에서 비밀 정보를 받고, 새 임의 암호를 생성하고, Key Vault의 새 암호를 사용하여 새 버전의 비밀을 만듭니다.
1. 함수 앱에서 SQL을 새 암호로 업데이트합니다.

> [!NOTE]
> 3단계와 4단계 사이에 지연이 발생할 수 있으며, 그 동안 Key Vault의 비밀은 SQL을 인증하는 데 유효하지 않습니다. 모든 단계 중 하나에서 실패하는 경우 Event Grid에서 2시간 동안 다시 시도합니다.

## <a name="setup"></a>설치 프로그램

## <a name="create-a-key-vault-and-sql-server"></a>키 자격 증명 모음 및 SQL Server 만들기

시작하기 전에 Key Vault를 만들고, SQL Server 및 데이터베이스를 만들고, SQL Server 관리자 암호를 Key Vault에 저장해야 합니다.

이 자습서에서는 미리 만든 Azure Resource Manager 템플릿을 사용하여 구성 요소를 만듭니다. 전체 코드는 [기본 비밀 회전 템플릿 샘플](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates) 위치에서 찾을 수 있습니다.

1. Azure 템플릿 배포 링크를 클릭합니다.
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. "리소스 그룹"에 대해 "새로 만들기"를 선택하고 "simplerotation"이라는 이름을 지정합니다.
1. "구매"를 선택합니다.

    ![새 리소스 그룹 만들기](./media/rotate2.png)

이러한 단계가 완료되면 키 자격 증명 모음, SQL 서버 및 SQL 데이터베이스가 만들어집니다. 다음을 실행하여 Azure CLI 터미널에서 이를 확인할 수 있습니다.

```azurecli
az resource list -o table
```

결과는 다음과 같습니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>함수 앱 만들기

시스템 관리 ID 외에도 추가 필수 구성 요소를 사용하여 함수 앱을 만듭니다. 

함수 앱에 필요한 구성 요소와 구성은 다음과 같습니다.
- App Service 계획
- 스토리지 계정
- 함수 앱 관리 ID를 사용하여 Key Vault의 비밀에 액세스하는 액세스 정책

1. Azure 템플릿 배포 링크를 클릭합니다.
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. "리소스 그룹"에 대해 "simplerotation"을 선택합니다.
1. "구매"를 선택합니다.

   ![구매 화면](./media/rotate3.png)

위의 단계가 완료되면 스토리지 계정, 서버 팜 및 함수 앱이 만들어집니다.  다음을 실행하여 Azure CLI 터미널에서 이를 확인할 수 있습니다.

```azurecli
az resource list -o table
```

결과는 다음과 같습니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

함수 앱을 만들고 관리 ID를 사용하여 Key Vault에 액세스하는 방법에 대한 자세한 내용은 [Azure Portal에서 함수 앱 만들기](../azure-functions/functions-create-function-app-portal.md) 및 [관리 ID를 사용하여 Key Vault 인증 제공](managed-identity.md)을 참조하세요.

### <a name="rotation-function-and-deployment"></a>회전 함수 및 배포
함수는 이벤트를 트리거로 사용하고, Key Vault 및 SQL 데이터베이스를 업데이트하기 위해 비밀의 회전을 수행합니다.

#### <a name="function-event-trigger-handler"></a>함수 이벤트 트리거 처리기

아래 함수는 이벤트 데이터를 읽고 회전 논리를 실행합니다.

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>비밀 회전 논리
다음 회전 메서드는 비밀에서 데이터베이스 정보를 읽고, 새 버전의 비밀을 만들고, 새 비밀을 사용하여 데이터베이스를 업데이트합니다.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
전체 코드는 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function 에서 찾을 수 있습니다.

#### <a name="function-deployment"></a>함수 배포

1. 함수 앱 Zip 파일(https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip )을 다운로드합니다.

1. simplerotationsample-fn.zip 파일을 Azure Cloud Shell에 업로드합니다.
 
1. 아래의 CLI 명령을 사용하여 Zip 파일을 함수 앱에 배포합니다.

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![구매 화면](./media/rotate4.png)

배포 후에는 simplerotation-fn 아래에서 두 함수를 확인할 수 있습니다.

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>"SecretNearExpiry" 이벤트에 대한 이벤트 구독 추가

함수 앱 eventgrid_extension 키를 복사합니다.

![Azure Cloud Shell](./media/rotate6.png)

![테스트 및 확인](./media/rotate7.png)

아래 명령으로 복사한 eventgrid 확장 키와 구독 ID를 사용하여 SecretNearExpiry 이벤트에 대한 이벤트 그리드 구독을 만듭니다.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Key Vault에 비밀 추가
사용자에게 "비밀 관리" 권한을 부여하도록 액세스 정책을 설정합니다.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

이제 SQL 데이터베이스 데이터 원본 및 사용자 ID가 포함된 태그를 사용하여 만료 날짜가 내일로 설정된 새 비밀을 만듭니다.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

만료 날짜가 짧은 비밀을 만들면 SecretNearExpiry 이벤트가 즉시 게시되어 비밀을 회전시키는 함수가 트리거됩니다.

### <a name="test-and-verify"></a>테스트 및 확인
몇 분 후 sqluser 비밀이 자동으로 회전됩니다.

비밀 회전을 확인하려면 Key Vault > 비밀로 차례로 이동합니다.

![테스트 및 확인](./media/rotate8.png)

"sqluser" 비밀을 열고, 원래 버전과 회전된 버전을 확인합니다.

![테스트 및 확인](./media/rotate9.png)

## <a name="create-web-app"></a>웹앱 만들기

SQL 자격 증명을 확인하려면 웹 애플리케이션을 만듭니다. 이 웹 애플리케이션은 키 자격 증명 모음에서 비밀을 가져오고, 비밀에서 SQL 데이터베이스 정보 및 자격 증명을 추출하며, SQL에 대한 연결을 테스트합니다.

웹앱에 필요한 구성 요소와 구성은 다음과 같습니다.
- 시스템 관리 ID를 사용하는 웹앱
- 웹앱 관리 ID를 사용하여 Key Vault의 비밀에 액세스하는 액세스 정책

1. Azure 템플릿 배포 링크를 클릭합니다.
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. **simplerotation** 리소스 그룹을 선택합니다.
1. [구매]를 클릭합니다.

### <a name="deploy-web-app"></a>웹앱 배포

웹앱의 소스 코드는 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp 에서 찾을 수 있습니다. 웹앱을 배포하려면 다음을 수행합니다.

1. 함수 앱 Zip 파일을 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip 에서 다운로드합니다.
1. `simplerotationsample-app.zip` 파일을 Azure Cloud Shell에 업로드합니다.
1. 다음 Azure CLI 명령을 사용하여 Zip 파일을 함수 앱에 배포합니다.

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>웹 애플리케이션 열기

배포된 애플리케이션으로 이동하여 "URL"을 클릭합니다.
 
![테스트 및 확인](./media/rotate10.png)

[데이터베이스 연결됨]이 true인 [생성된 비밀 값]이 표시됩니다.

## <a name="learn-more"></a>자세한 정보:

- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](event-grid-overview.md)
- 방법: [키 자격 증명 모음 비밀 변경 시 이메일 받기](event-grid-logicapps.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../event-grid/event-schema-key-vault.md)
