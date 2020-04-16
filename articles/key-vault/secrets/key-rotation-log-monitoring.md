---
title: 엔드투엔드 키 회전 및 감사를 사용하여 Azure Key Vault 설정 | Microsoft Docs
description: 이 방법 가이드를 사용하여 키 회전을 설정하고 키 볼트 로그를 모니터링할 수 있습니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430254"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>키 회전 및 감사를 사용하여 Azure Key Vault 설정

## <a name="introduction"></a>소개

Key Vault가 있으면 이를 사용하여 키 및 암호를 저장할 수 있습니다. 사용자 애플리케이션에서는 키 또는 암호 정보를 더 이상 유지할 필요가 없으며, 필요에 따라 자격 증명 모음에서 요청할 수 있습니다. 키 자격 증명 모음을 사용하면 응용 프로그램의 동작에 영향을 주지 않고 키와 비밀을 업데이트할 수 있으므로 키 및 비밀 관리에 대한 다양한 가능성이 열립니다.

이 문서에서는 저장소 계정 키의 예약된 회전을 구현하고, 키 자격 증명 모음 감사 로그를 모니터링하고, 예기치 않은 요청이 있을 때 경고를 발생시 하는 방법을 설명합니다. 

먼저 선택한 방법을 사용하여 키 자격 증명 모음을 만들어야 합니다.

- [Azure CLI를 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](quick-create-cli.md)
- [Azure PowerShell을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색](quick-create-powershell.md)
- [Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색](quick-create-portal.md)


## <a name="store-a-secret"></a>비밀 저장

애플리케이션을 통해 Azure Key Vault에서 비밀을 검색하려면 먼저 비밀을 만들어 Key Vault에 업로드해야 합니다.

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.

```powershell
Connect-AzAccount
```

팝업 브라우저 창에서 Azure 계정의 사용자 이름과 암호를 입력합니다. PowerShell이 이 계정과 연결된 모든 구독을 가져옵니다. PowerShell은 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Key Vault을 만드는 데 사용된 구독을 지정해야 할 수도 있습니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzSubscription
```

로깅할 키 자격 증명 모음과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

이 문서에서는 스토리지 계정 키를 비밀로 저장하는 방법을 설명하므로 해당 스토리지 계정 키를 가져와야 합니다.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

이 경우 저장소 계정 키를 검색한 후 해당 키를 보안 문자열로 변환한 다음 키 자격 증명 모음에 해당 값으로 비밀을 만들어야 합니다.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

그런 다음, 만든 비밀의 URI를 가져옵니다. 키 자격 증명 모음을 호출하고 비밀을 검색하려면 이후 단계에서 이 URI가 필요합니다. 다음 PowerShell 명령을 실행하고 비밀의 URI인 ID 값을 기록합니다.

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>애플리케이션 설정

이제 암호를 저장한 후 코드를 사용하여 몇 가지 단계를 수행한 후 검색하고 사용할 수 있습니다.

먼저 응용 프로그램을 Azure Active Directory에 등록해야 합니다. 그런 다음 Key Vault에 응용 프로그램의 요청을 허용할 수 있도록 응용 프로그램 정보를 알려줍니다.

> [!NOTE]
> 애플리케이션은 Key Vault와 동일한 Azure Active Directory에서 만들어야 합니다.

1. Azure 활성 디렉터리 를 **엽니다.**
2. **앱 등록을 선택합니다.** 
3. Azure Active Directory에 응용 프로그램을 추가하려면 **새 응용 프로그램 등록을** 선택합니다.

    ![Azure Active Directory에서 애플리케이션 열기](../media/keyvault-keyrotation/azure-ad-application.png)

4. **만들기에서**응용 프로그램 유형을 **웹 앱/API로** 두고 응용 프로그램에 이름을 지정합니다. 응용 프로그램에 **사인온 URL을**제공합니다. 이 URL은 이 데모에 원하는 모든 것이 될 수 있습니다.

    ![애플리케이션 등록 만들기](../media/keyvault-keyrotation/create-app.png)

5. 응용 프로그램이 Azure Active Directory에 추가되면 응용 프로그램 페이지가 열립니다. **설정을**선택한 다음 **속성**을 선택합니다. **애플리케이션 ID** 값을 복사합니다. 이후 단계에서 필요합니다.

그런 다음 Azure Active Directory와 상호 작용할 수 있도록 응용 프로그램에 대한 키를 생성합니다. 키를 만들려면 **설정**에서 **키를** 선택합니다. Azure Active Directory 응용 프로그램에 대해 새로 생성된 키를 기록합니다. 이후 단계에서 필요합니다. 이 섹션을 떠난 후에는 키를 사용할 수 없습니다. 

![Azure Active Directory 앱 키](../media/keyvault-keyrotation/create-key.png)

응용 프로그램에서 키 자격 증명 모음으로 호출을 설정하기 전에 키 자격 증명 모음에 응용 프로그램 및 해당 사용 권한에 대해 알려야 합니다. 다음 명령은 Azure Active Directory 앱의 볼트 이름과 응용 프로그램 ID를 사용하여 **응용** 프로그램에 키 자격 증명 모음에 대한 액세스 권한을 부여합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

이제 응용 프로그램 호출 빌드를 시작할 준비가 되었습니다. 응용 프로그램에서 Azure 키 자격 증명 모음 및 Azure Active Directory와 상호 작용하는 데 필요한 NuGet 패키지를 설치해야 합니다. Visual Studio 패키지 관리자 콘솔에서 다음 명령을 입력합니다. 이 문서의 작성에서 Azure Active Directory 패키지의 현재 버전은 3.10.305231913이므로 필요에 따라 최신 버전 및 업데이트를 확인합니다.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

애플리케이션 코드에서 Azure Active Directory 인증에 대한 메서드를 보유할 클래스를 만듭니다. 이 예제에서 이 클래스는 **Utils**입니다. 다음 `using` 문을 추가합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

다음으로, Azure Active Directory에서 JWT 토큰을 검색하는 다음 메서드를 추가합니다. 유지 관리용을 위해 하드 코딩된 문자열 값을 웹 또는 응용 프로그램 구성으로 이동할 수 있습니다.

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

Key Vault를 호출하고 비밀 값을 검색하는 데 필요한 코드를 추가할 수 있습니다. 먼저 다음 `using` 문을 추가해야 합니다.

```csharp
using Microsoft.Azure.KeyVault;
```

Key Vault를 호출하고 비밀을 검색하는 메서드 호출을 추가합니다. 이 메서드에 이전 단계에서 저장한 비밀 URI를 입력합니다. 이전에 만든 **Utils** 클래스에서 **GetToken** 메서드를 사용 하 여 참고합니다.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

애플리케이션을 실행하면 Azure Active Directory에 인증된 후 Azure Key Vault에서 비밀 값을 검색합니다.

## <a name="key-rotation-using-azure-automation"></a>Azure Automation을 사용하여 키 회전

> [!IMPORTANT]
> Azure 자동화 런북은 여전히 모듈을 사용해야 합니다. `AzureRM`

이제 키 볼트 비밀로 저장하는 값에 대한 회전 전략을 설정할 준비가 되었습니다. 비밀은 여러 가지 방법으로 회전할 수 있습니다.

- 수동 프로세스의 일부로
- API 호출을 사용하여 프로그래밍 방식으로
- Azure 자동화 스크립트를 통해

이 문서에서는 Azure 자동화와 결합된 PowerShell을 사용하여 Azure 저장소 계정의 액세스 키를 변경합니다. 그런 다음 새 키로 키 자격 증명 모음 비밀을 업데이트합니다.

Azure Automation이 키 자격 증명 모음에서 비밀 값을 설정할 수 있도록 하려면 **AzureRunAsConnection**이라는 연결에 대한 클라이언트 ID를 받아야 합니다. 이 연결은 Azure 자동화 인스턴스를 설정했을 때 만들어졌습니다. 이 ID를 찾으려면 Azure 자동화 인스턴스에서 **자산을** 선택합니다. 거기에서 **연결을**선택한 다음 **AzureRunAsConnection** 서비스 주체를 선택합니다. ApplicationId 값을 **기록해** 둡습니다.

![Azure Automation 클라이언트 ID](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**자산에서** **모듈을 선택합니다.** **갤러리를**선택한 다음 다음 각 모듈의 업데이트된 버전을 검색하고 가져옵니다.

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 이 문서를 작성할 당시 위에 명시된 모듈만 다음 스크립트에 대해 업데이트되어야 합니다. 자동화 작업이 실패하면 필요한 모든 모듈과 해당 종속성을 가져왔는지 확인합니다.

Azure 자동화 연결에 대한 응용 프로그램 ID를 검색한 후에는 이 응용 프로그램에 볼트의 비밀을 업데이트할 수 있는 권한이 있음을 키 자격 증명 모음에 알려야 합니다. 다음과 같은 PowerShell 명령을 사용합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

그런 다음 Azure 자동화 인스턴스에서 **Runbook을** 선택한 다음 **Runbook 추가를**선택합니다. **빠른 만들기를 선택합니다.** Runbook의 이름을 지정하고 **PowerShell을** Runbook 유형으로 선택합니다. 설명을 추가할 수 있습니다. 마지막으로 **만들기**를 선택합니다.

![runbook 만들기](../media/keyvault-keyrotation/Create_Runbook.png)

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

편집기 창에서 **테스트 창을** 선택하여 스크립트를 테스트합니다. 스크립트가 오류 없이 실행된 후 **게시**를 선택한 다음 Runbook 구성 창에서 Runbook에 대한 일정을 적용할 수 있습니다.

## <a name="key-vault-auditing-pipeline"></a>Key Vault 감사 파이프라인

키 자격 증명 모음을 설정할 때 감사를 켜서 키 자격 증명 모음에 대해 발생하는 액세스 요청에 대한 로그를 수집할 수 있습니다. 이러한 로그는 지정된 Azure 저장소 계정에 저장되며 꺼내, 모니터링 및 분석할 수 있습니다. 다음 시나리오에서는 Azure 함수, Azure 논리 앱 및 키 자격 증명 모음 감사 로그를 사용하여 웹 앱의 앱 ID와 일치하지 않는 앱이 볼트에서 비밀을 검색할 때 전자 메일을 보내는 파이프라인을 만듭니다.

먼저 Key Vault에서 로깅을 사용하도록 설정해야 합니다. 다음 PowerShell 명령을 사용합니다. (키 [볼트 로깅에 대한 자세한](../general/logging.md)내용은 이 문서에서 볼 수 있습니다.)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

로깅을 사용하도록 설정하면 감사 로그가 지정된 저장소 계정에 저장되기 시작합니다. 이러한 로그에는 키 자격 증명 모음을 어떻게, 언제, 누가 액세스하는지에 대한 이벤트가 포함됩니다.

> [!NOTE]
> Key Vault 작업 후 10분 내에 로깅 정보에 액세스할 수 있습니다. 그것은 종종 그 보다 더 빨리 사용할 수 있을 것입니다.

다음은 [Azure Service Bus 큐를 만드는](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)단계입니다. 이 큐는 키 자격 증명 모음 감사 로그가 푸시되는 곳입니다. 감사 로그 메시지가 큐에 있으면 논리 앱에서 메시지를 선택하고 이에 대해 작동합니다. 다음 단계를 사용하여 서비스 버스 인스턴스를 만듭니다.

1. Service Bus 네임스페이스를 만듭니다(사용하려는 것이 이미 있는 경우 2단계로 건너뜁니다).
2. Azure 포털에서 Service Bus 인스턴스를 찾아큐를 만들 네임스페이스를 선택합니다.
3. 리소스 > **엔터프라이즈 통합** > **서비스 버스** **만들기를**선택한 다음 필요한 세부 정보를 입력합니다.
4. 네임스페이스를 선택한 다음 **연결 정보를**선택하여 서비스 버스 연결 정보를 찾습니다. 다음 섹션에 대 한이 정보가 필요 합니다.

다음으로 [Azure 함수를 만들어](../../azure-functions/functions-create-first-azure-function.md) 저장소 계정 내의 키 자격 증명 모음 로그를 폴링하고 새 이벤트를 선택합니다. 이 함수는 일정에 따라 트리거됩니다.

Azure 함수 앱을 만들려면 **리소스 만들기를**선택하고 **마켓플레이스에서 함수 앱을**검색한 다음 을 **선택합니다.** 만들기 중에 기존 호스팅 계획을 사용하거나 새 계획을 만들 수 있습니다. 동적 호스팅을 선택할 수도 있습니다. Azure 함수에 대한 호스팅 옵션에 대한 자세한 내용은 [Azure 함수의 확장 방법을](../../azure-functions/functions-scale.md)참조하십시오.

Azure 함수 앱을 만든 후 이동하여 언어에 대한 **타이머** 시나리오 및 **C를\# ** 선택합니다. 그런 다음 **이 함수 만들기를**선택합니다.

![Azure Functions 시작 블레이드](../media/keyvault-keyrotation/Azure_Functions_Start.png)

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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
> 이전 코드의 변수를 변경하여 키 자격 증명 모음 로그가 기록된 저장소 계정, 이전에 만든 Service Bus 인스턴스 및 키 자격 증명 모음 저장소 로그에 대한 특정 경로를 가리킵니다.

이 함수는 Key Vault 로그가 기록된 스토리지 계정에서 최신 로그 파일을 선택하고 해당 파일에서 최신 이벤트를 가져와 Service Bus 큐에 푸시합니다. 

단일 파일에여러 개의 이벤트가 있을 수 있으므로 함수가 선택한 마지막 이벤트의 타임스탬프를 결정하기 위해 함수가 보는 sync.txt 파일을 만들어야 합니다. 이 파일을 사용하면 동일한 이벤트를 여러 번 푸시하지 않습니다. 

sync.txt 파일에는 마지막으로 발생한 이벤트에 대한 타임스탬프가 포함되어 있습니다. 로그가 로드되면 타임스탬프에 따라 로그가 올바르게 정렬되었는지 확인해야 합니다.

이 함수의 경우 Azure Functions에서 즉시 사용할 수 없는 몇 가지 추가 라이브러리를 참조합니다. 이러한 라이브러리를 포함하려면 NuGet을 사용하여 라이브러리를 끌어오기 위해 Azure 함수가 필요합니다. **코드** 상자에서 **파일 보기를**선택합니다.

!["파일 보기" 옵션](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

project.json이라는 파일을 다음 내용으로 추가합니다.

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

**저장을**선택하면 Azure 함수가 필요한 바이너리를 다운로드합니다.

**통합** 탭으로 전환하고 타이머 매개 변수에 함수 내에서 사용할 의미 있는 이름을 지정합니다. 앞의 코드에서 함수는 타이머를 *myTimer*라고 할 것으로 예상합니다. 타이머에 대한 [CRON 식을](../../app-service/webjobs-create.md#CreateScheduledCRON) 다음과 `0 * * * * *`같이 지정합니다. 이 식은 함수가 1분에 한 번 실행되도록 합니다.

동일한 **통합** 탭에서 Azure Blob 저장소 형식의 입력을 **추가합니다.** 이 입력은 함수에서 살펴본 마지막 이벤트의 타임스탬프가 포함된 sync.txt 파일을 가리킵니다. 이 입력은 매개 변수 이름을 사용하여 함수 내에서 액세스됩니다. 앞의 코드에서 Azure Blob 저장소 입력은 매개 변수 이름을 *inputBlob로*예상합니다. sync.txt 파일이 있는 저장소 계정을 선택합니다(동일하거나 다른 저장소 계정일 수 있음). 경로 필드에서 형식의 `{container-name}/path/to/sync.txt`파일에 대한 경로를 제공합니다.

Azure Blob 저장소 형식의 출력을 **추가합니다.** 이 출력은 입력에 정의 한 sync.txt 파일을 가리킵니다. 이 출력은 함수에서 마지막으로 살펴본 이벤트의 타임스탬프를 작성하는 데 사용됩니다. 위의 코드에서는 이 매개 변수를 *outputBlob*이라고 합니다.

이제 함수가 준비되었습니다. 다시 **개발** 탭으로 전환하고 코드를 저장합니다. 출력 창에서 컴파일 오류가 있는지 확인하고 필요에 따라 수정합니다. 코드가 컴파일되면 코드는 1분마다 키 자격 증명 모음 로그를 확인하고 새 이벤트를 정의된 Service Bus 큐로 푸시해야 합니다. 함수가 트리거될 때마다 로깅 정보가 로그 창에 기록되는 것이 보입니다.

### <a name="azure-logic-app"></a>Azure 논리 앱

다음으로 함수가 Service Bus 큐로 푸시하는 이벤트를 선택하고 콘텐츠를 구문 분석한 다음 일치하는 조건에 따라 전자 메일을 보내는 Azure 논리 앱을 만들어야 합니다.

**리소스** > ** 통합** > ** 논리 앱 만들기**를 선택하여 [논리 앱을 만듭니다](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

논리 앱을 만든 후 로직 앱으로 이동하여 **편집을**선택합니다. 논리 앱 편집기에서 **서비스 버스 큐를** 선택하고 Service Bus 자격 증명을 입력하여 큐에 연결합니다.

![Azure Logic App Service Bus](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**조건 추가**를 선택합니다. 조건에서 고급 편집기로 전환하고 다음 코드를 입력합니다. *APP_ID* 웹 앱의 실제 앱 ID로 바꿉꿉을 바꿉꿉입니다.

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

이 식은 들어오는 이벤트(Service Bus 메시지의 본문)의 *appid가* 앱의 *appid가* 아닌 경우 기본적으로 **false를** 반환합니다.

이제 **아무 작업도 수행하지 않으면**에서 작업을 만듭니다.

![Azure 논리 앱에서 작업을 선택합니다.](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

작업의 경우 **Office 365 - 전자 메일 보내기를**선택합니다. 정의된 조건에서 **false**를 반환하는 경우 보낼 전자 메일을 작성하도록 필드를 채웁니다. Office 365가 없는 경우 동일한 결과를 얻을 수 있는 대안을 찾습니다.

이제 1분에 한 번 새 키 자격 증명 모음 감사 로그를 찾는 종단 간 파이프라인이 생겼습니다. 찾은 새 로그를 Service Bus 큐에 푸시합니다. 새 메시지가 큐에 도착하면 논리 앱이 트리거됩니다. 이벤트 내의 *appid가* 호출 응용 프로그램의 앱 ID와 일치하지 않으면 전자 메일을 보냅니다.
