---
title: 엔드투엔드 키 회전 및 감사를 사용하여 Azure Key Vault 설정 | Microsoft Docs
description: 키 회전을 설정 하 고 key vault 로그를 모니터링할 수 있도록이 방법 가이드를 사용 합니다.
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561197"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>키 회전 및 감사를 사용하여 Azure Key Vault 설정

## <a name="introduction"></a>소개

Key Vault가 있으면 이를 사용하여 키 및 암호를 저장할 수 있습니다. 사용자 애플리케이션에서는 키 또는 암호 정보를 더 이상 유지할 필요가 없으며, 필요에 따라 자격 증명 모음에서 요청할 수 있습니다. Key vault를 사용 하면 다양 한 키 및 비밀 관리 가능성 열리는 프로그램의 동작에 영향을 주지 않고 키 및 비밀을 업데이트할 수 있습니다.

>[!IMPORTANT]
> 이 문서의 예제는 설명 용도로만 제공됩니다. 프로덕션 사용에 대 한 아닙니다 하 고 있습니다. 

이 문서에서는 다음을 안내합니다.

- Azure Key Vault를 사용하여 암호를 저장하는 예. 이 문서에서는 저장 된 암호는 응용 프로그램이 액세스 하는 Azure storage 계정 키입니다. 
- 해당 저장소 계정 키의 예약된 된 회전 구현 하는 방법입니다.
- 감사 로그를 자격 증명 모음 하 고 예기치 않은 요청이 있을 때 경고를 생성 하는 키를 모니터링 하는 방법입니다.

> [!NOTE]
> 이 문서에서는 key vault의 초기 설정을 자세히 설명 하지 않습니다. 자세한 내용은 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요. 플랫폼 간 명령줄 인터페이스 지침은 [Azure CLI를 사용 하 여 Key Vault 관리](key-vault-manage-with-cli2.md)합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>주요 자격 증명 모음 설정

애플리케이션을 통해 Azure Key Vault에서 비밀을 검색하려면 먼저 비밀을 만들어 Key Vault에 업로드해야 합니다.

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.

```powershell
Connect-AzAccount
```

팝업 브라우저 창에서 Azure 계정의 사용자 이름과 암호를 입력 합니다. PowerShell이 이 계정과 연결된 모든 구독을 가져옵니다. PowerShell은 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Key Vault을 만드는 데 사용된 구독을 지정해야 할 수도 있습니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzSubscription
```

로깅하려에서는 key vault와 사용 하 여 연결 된 구독을 지정 하려면 다음을 입력 합니다.

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

이 문서에서는 저장소 계정 키를 비밀로 저장하는 방법을 설명하므로 해당 저장소 계정 키를 가져와야 합니다.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

사용자 비밀 (이 경우 저장소 계정 키)를 검색 한 후 해당 키를 보안 문자열로 변환 하며 key vault에서 비밀 값을 사용 하 여을 만들어야 합니다.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

그런 다음, 만든 비밀의 URI를 가져옵니다. Key vault를 호출 하 여 비밀을 검색 하는 이후 단계에서이 URI를 해야 합니다. 다음 PowerShell 명령을 실행 하 고 암호의 URI 인 ID 값을 기록해 둡니다.

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>애플리케이션 설정

저장 된 비밀 했으므로 검색 하 고 사용 하 여 몇 가지 추가 단계를 수행한 후 코드를 사용할 수 있습니다.

먼저 Azure Active Directory를 사용 하 여 응용 프로그램을 등록 해야 합니다. 그런 다음 지시할 Key Vault 응용 프로그램에서 요청을 허용할 수 있도록를 응용 프로그램 정보.

> [!NOTE]
> 애플리케이션은 Key Vault와 동일한 Azure Active Directory에서 만들어야 합니다.

1. 오픈 **Azure Active Directory**합니다.
2. **앱 등록**을 선택합니다. 
3. 선택 **새 응용 프로그램 등록** Azure Active Directory에 응용 프로그램을 추가 합니다.

    ![Azure Active Directory에서 애플리케이션 열기](./media/keyvault-keyrotation/azure-ad-application.png)

4. 아래 **만들기**, 응용 프로그램 유형으로 유지 **웹 앱 / API** 응용 프로그램 이름을 지정 합니다. 응용 프로그램 지정 된 **로그온 URL**합니다. 이 URL이이 데모에 대 한 원하는 대로 지정할 수 있습니다.

    ![애플리케이션 등록 만들기](./media/keyvault-keyrotation/create-app.png)

5. 응용 프로그램이 Azure Active Directory에 추가 된 후 응용 프로그램 페이지를 엽니다. 선택 **설정을**를 선택한 후 **속성**합니다. **응용 프로그램 ID** 값을 복사합니다. 이후 단계에서 필요 합니다.

다음으로, Azure Active Directory와 상호 작용할 수 있도록 응용 프로그램에 대 한 키를 생성 합니다. 선택 키를 만들려면 **키** 아래에서 **설정**합니다. Azure Active Directory 응용 프로그램에 대 한 새로 생성 된 키를 적어 둡니다. 이후 단계에서 필요합니다. 이 섹션을 나간 후에 키를 사용할 수 없습니다. 

![Azure Active Directory 앱 키](./media/keyvault-keyrotation/create-key.png)

Key vault에 응용 프로그램에서 모든 호출을 설정 하기 전에 응용 프로그램 및 해당 권한에 대 한 key vault을 알려야 합니다. 다음 명령은 자격 증명 모음 이름 및 Azure Active Directory 앱의 응용 프로그램 ID를 사용 하 여 응용 프로그램에 부여할 **가져올** key vault에 액세스 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

이제 응용 프로그램 호출을 작성할 준비가 되었습니다. 응용 프로그램에서 Azure Key Vault와 Azure Active Directory와 상호 작용 하는 데 필요한 NuGet 패키지를 설치 해야 합니다. Visual Studio 패키지 관리자 콘솔에서 다음 명령을 입력합니다. 이 문서를 작성할 당시 Azure Active Directory 패키지의 현재 버전은 3.10.305231913, 따라서 최신 버전을 확인 및 필요에 따라 업데이트 합니다.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

애플리케이션 코드에서 Azure Active Directory 인증에 대한 메서드를 보유할 클래스를 만듭니다. 이 예제에서 이 클래스는 **Utils**입니다. 다음 `using` 문을 추가합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

다음으로, Azure Active Directory에서 JWT 토큰을 검색하는 다음 메서드를 추가합니다. 유지 관리를 위해 하드 코드 된 문자열 값을 웹 또는 응용 프로그램 구성으로 이동 하는 것이 좋습니다.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Key Vault를 호출하고 비밀 값을 검색하는 데 필요한 코드를 추가할 수 있습니다. 다음을 추가 해야 합니다는 먼저 `using` 문:

```csharp
using Microsoft.Azure.KeyVault;
```

Key Vault를 호출하고 비밀을 검색하는 메서드 호출을 추가합니다. 이 메서드에 이전 단계에서 저장한 비밀 URI를 입력합니다. 사용 합니다 **GetToken** 메서드에서 **Utils** 이전에 만든 클래스입니다.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

애플리케이션을 실행하면 Azure Active Directory에 인증된 후 Azure Key Vault에서 비밀 값을 검색합니다.

## <a name="key-rotation-using-azure-automation"></a>Azure Automation을 사용하여 키 회전

> [!IMPORTANT]
> Azure Automation runbook은 계속 사용 해야는 `AzureRM` 모듈입니다.

Key Vault 비밀로 저장 하는 값을 위한 회전 전략을 설정할 준비가 되었습니다. 여러 가지 방법으로 암호를 회전할 수 있습니다.

- 수동 프로세스의 일부로
- API 호출을 사용 하 여 프로그래밍 방식으로
- Azure Automation 스크립트를 통해

이 문서에서는 Azure storage 계정의 액세스 키를 변경 하려면 Azure Automation과 결합 된 PowerShell을 사용 합니다. 해당 새 키를 사용 하 여 key vault 비밀을 업데이트할 수 있습니다.

Azure key vault에서 비밀 값을 설정 하는 자동화를 허용 하려면 명명 된 연결에 대 한 클라이언트 ID를 가져오려면 해야 **AzureRunAsConnection**합니다. 이 연결에는 Azure Automation 인스턴스를 설정할 때 생성 되었습니다. 선택이 ID를 찾으려면 **자산** Azure Automation 인스턴스에서 합니다. 여기에서 선택 **연결**를 선택한 후 합니다 **AzureRunAsConnection** 서비스 주체. 기록해는 **ApplicationId** 값입니다.

![Azure Automation 클라이언트 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**자산**를 선택 **모듈**합니다. 선택 **갤러리**, 다음 검색 하 고 각 다음 모듈의 업데이트 된 버전을 가져옵니다.

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 이 문서를 작성할 당시 위에 명시된 모듈만 다음 스크립트에 대해 업데이트되어야 합니다. 자동화 작업이 실패 하면 사용자가 가져온 필요한 모든 모듈과 해당 종속성을 확인 합니다.

Azure Automation 연결에 대 한 응용 프로그램 ID를 검색 한 후이 응용 프로그램 자격 증명 모음에 비밀을 업데이트할 권한이 있는지 키 자격 증명 모음 지시 해야 있습니다. 다음 PowerShell 명령을 사용 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

다음으로, 선택 **Runbook** 아래에 Azure Automation 인스턴스를 선택한 후 **Runbook 추가**합니다. **빠른 생성**을 선택합니다. 선택한 runbook의 이름을 **PowerShell** runbook 유형으로 합니다. 설명을 추가할 수 있습니다. 마지막으로 **만들기**를 선택합니다.

![runbook 만들기](./media/keyvault-keyrotation/Create_Runbook.png)

새 runbook의 편집기 창에 다음 PowerShell 스크립트를 붙여넣습니다.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

편집기 창에서 선택 **테스트 창** 스크립트를 테스트 합니다. 스크립트가 오류 없이 실행 한 후 선택할 수 있습니다 **게시**, 그런 다음 runbook 구성 창에서 runbook에 대 한 일정을 적용할 수 있습니다.

## <a name="key-vault-auditing-pipeline"></a>Key Vault 감사 파이프라인

키 자격 증명 모음을 설정할 때 감사를 켜서 키 자격 증명 모음에 대해 발생하는 액세스 요청에 대한 로그를 수집할 수 있습니다. 이러한 로그는 지정 된 Azure storage 계정에 저장 되 고, 모니터링 및 분석을 가져올 수 있습니다. 다음 시나리오는 웹 앱의 앱 ID와 일치 하지 않는 앱 자격 증명 모음에서 비밀을 검색 하는 경우 전자 메일을 보내는 파이프라인을 만드는 데 Azure functions, Azure logic apps 및 key vault 감사 로그를 사용 합니다.

먼저 Key Vault에서 로깅을 사용하도록 설정해야 합니다. 다음 PowerShell 명령을 사용 합니다. (의 전체 세부 정보를 볼 수 있습니다 [키 자격 증명 모음 로깅에 대 한이 문서에서는](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

로깅이 설정 되 면 감사 로그는 지정 된 저장소 계정에 저장 되 고 시작 합니다. 이러한 로그에는 키 자격 증명 모음을 어떻게, 언제, 누가 액세스하는지에 대한 이벤트가 포함됩니다.

> [!NOTE]
> Key Vault 작업 후 10분 내에 로깅 정보에 액세스할 수 있습니다. 자주 제공 됩니다 보다 더 빨리 합니다.

다음은 [Azure Service Bus 큐를 만드는](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)단계입니다. 이 큐는 key vault 감사 로그가 푸시됩니다 위치입니다. 감사 로그 메시지를 큐에 있는 논리 앱에서 선택 하 고 여 작업을 수행 합니다. 다음 단계를 사용 하 여 Service Bus 인스턴스를 만듭니다.

1. (이미 있는 경우 하나를 사용 하 여 2 단계로 건너뜁니다) Service Bus 네임 스페이스를 만듭니다.
2. Azure portal에서 Service Bus 인스턴스에 찾아 안에 큐를 만들려는 네임 스페이스를 선택 합니다.
3. 선택 **리소스 만들기** > **엔터프라이즈 통합** > **Service Bus**를 선택한 다음 필요한 세부 정보를 입력 합니다.
4. 네임 스페이스를 선택 하 고 다음을 선택 하 여 Service Bus 연결 정보를 찾습니다 **연결 정보**합니다. 이 정보는 다음 섹션에 필요 합니다.

그런 다음, [Azure function 만들기](../azure-functions/functions-create-first-azure-function.md) 저장소 계정 내에서 key vault 로그를 폴링하고 새 이벤트를 선택 합니다. 이 함수는 일정에 따라 트리거됩니다.

Azure 함수 앱을 만들려고 선택 **리소스 만들기**에 대 한 marketplace 검색 **함수 앱**를 선택한 후 **만들기**합니다. 만들기 중에 기존 호스팅 계획을 사용하거나 새 계획을 만들 수 있습니다. 동적 호스팅을 선택할 수도 있습니다. Azure Functions 호스팅 옵션에 대 한 자세한 내용은 참조 하세요. [Azure Functions 크기 조정 방법](../azure-functions/functions-scale.md)합니다.

Azure 함수 앱을 만든 후에 이동 하 고 선택 합니다 **타이머** 시나리오 및 **C\#**  언어에 대 한 합니다. 선택한 **이 함수 만들기**합니다.

![Azure Functions 시작 블레이드](./media/keyvault-keyrotation/Azure_Functions_Start.png)

**개발** 탭에서 run.csx 코드를 다음 코드로 바꿉니다.

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Key vault 로그가 기록 되는 저장소 계정, 이전에 만든 Service Bus 인스턴스 및 key vault 저장소 로그에 특정 경로 가리키도록 위의 코드에서 변수를 변경 합니다.

이 함수는 Key Vault 로그가 기록된 저장소 계정에서 최신 로그 파일을 선택하고 해당 파일에서 최신 이벤트를 가져와 Service Bus 큐에 푸시합니다. 

단일 파일에서 여러 이벤트를 가질 수 있으므로 함수 훌륭하면서도에서 선택 된 마지막 이벤트의 타임 스탬프를 확인 하는 sync.txt 파일을 만들어야 합니다. 이 파일을 사용 하는 푸시 하지 않게 같은 이벤트가 여러 번 확인 합니다. 

Sync.txt 파일을 마지막으로 발생 한 이벤트에 대 한 타임 스탬프를 포함합니다. 로그 로드 되는 경우 이러한 정렬 해야 올바르게 정렬 하는 되도록 해당 타임 스탬프에 따라.

이 함수에 대 한 Azure Functions에서 바로 사용할 수 없는 몇 가지 추가 라이브러리를 참조 했습니다. 이러한 라이브러리를 포함 하려면 NuGet을 사용 하 여 가져오려고 Azure Functions가 필요 했습니다. 아래는 **코드** 상자에서 **파일 보기**합니다.

!["파일 보기" 옵션](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

다음과 같은 내용으로 project.json 인 파일을 추가 합니다.

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

선택한 후 **저장할**, Azure Functions는 필요한 이진 파일을 다운로드 합니다.

**통합** 탭으로 전환하고 타이머 매개 변수에 함수 내에서 사용할 의미 있는 이름을 지정합니다. 위의 코드에서 함수는 호출할 타이머 *myTimer*합니다. 지정 된 [CRON 식](../app-service/webjobs-create.md#CreateScheduledCRON) 다음과 같이 타이머에 대 한: `0 * * * * *`합니다. 이 식은 사용 하면 함수가 1 분에 한 번 실행 합니다.

동일한 **통합** 탭에서 추가 된 형식의 입력 **Azure Blob storage**합니다. 이 입력에는 함수에서 확인 하는 마지막 이벤트의 타임 스탬프를 포함 하는 sync.txt 파일을 가리킵니다. 이 입력 매개 변수 이름을 사용 하 여 함수 내에서 액세스 합니다. 위의 코드에서 Azure Blob storage 입력 하 고 매개 변수 이름을 사용 해야 *inputBlob*합니다. Sync.txt 파일을 찾을 수는 저장소 계정을 선택 (수 동일 하거나 다른 저장소 계정). 경로 필드 형식으로 파일의 경로를 제공 `{container-name}/path/to/sync.txt`합니다.

형식의 출력 추가 **Azure Blob storage**합니다. 이 출력에는 입력에서 정의한 sync.txt 파일을 가리킵니다. 이 출력 살펴보고 마지막 이벤트의 타임 스탬프를 작성 하는 함수에 의해 사용 됩니다. 위의 코드에서는 이 매개 변수를 *outputBlob*이라고 합니다.

함수가 준비 되었습니다. 다시 **개발** 탭으로 전환하고 코드를 저장합니다. 출력 창에서 컴파일 오류를 확인 하 고 필요에 따라 수정 합니다. 코드가 컴파일되면 다음 코드는 이제 1 분 마다 key vault 로그를 확인 있고 정의 된 Service Bus 큐에 새 이벤트를 푸시 합니다. 함수가 트리거될 때마다 로깅 정보가 로그 창에 기록되는 것이 보입니다.

### <a name="azure-logic-app"></a>Azure 논리 앱

다음으로 Service Bus 큐에 푸시하는, 콘텐츠를 구문 분석 함수와 일치 하는 조건에 따라 전자 메일을 보내는 이벤트를 선택 하는 Azure 논리 앱을 만들어야 합니다.

[논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md) 를 선택 하 여 **리소스를 만듭니다** > **Integration** > **논리 앱**합니다.

논리 앱을 만든 후에 이동 하 고 선택 **편집**합니다. 논리 앱 편집기에서 선택 **Service Bus 큐** 큐에 연결할 Service Bus 자격 증명을 입력 합니다.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**조건 추가**를 선택합니다. 조건에서 고급 편집기로 전환 하 고 다음 코드를 입력 합니다. 바꿉니다 *APP_ID* 웹 앱의 실제 앱 ID를 사용 하 여:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

이 식은 기본적으로 반환 합니다 **false** 경우는 *appid* 되지 않습니다 (즉, Service Bus 메시지의 본문) 들어오는 이벤트에서를 *appid* 앱의 합니다.

이제 아래에서 작업을 만듭니다 **을 선택 하지 않으면 아무 작업도 하지**합니다.

![Azure Logic Apps 작업 선택](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

작업에 대해 선택 **Office 365-전자 메일 보내기**합니다. 정의된 조건에서 **false**를 반환하는 경우 보낼 전자 메일을 작성하도록 필드를 채웁니다. Office 365가 없는 경우 동일한 결과 얻으려면 대안을 찾아보세요.

이제 1 분 마다 새로운 key vault 감사 로그에 대 한 보이는 종단 간 파이프라인이 있습니다. Service Bus 큐에 새 로그가 발견 밀어넣습니다. 새 메시지가 큐에 도착하면 논리 앱이 트리거됩니다. 경우는 *appid* 내의 이벤트에는 호출 응용 프로그램의 앱 ID와 일치 하지 않습니다, 전자 메일을 보냅니다.
