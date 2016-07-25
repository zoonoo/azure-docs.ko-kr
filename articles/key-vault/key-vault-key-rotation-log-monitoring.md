<properties
	pageTitle="종단 간 키 회전 및 감사를 사용하여 주요 자격 증명 모음을 설정하는 방법 | Microsoft Azure"
	description="키 회전 및 주요 자격 증명 모음 로그의 모니터링을 통해 설정을 가져오는 데 이 방법을 사용합니다."
	services="key-vault, automation,app-service\logic"
	documentationCenter=""
	authors="swgriffith"
	manager=""
	tags=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="jodehavi;stgriffi"/>
#종단 간 키 회전 및 감사를 사용하여 주요 자격 증명 모음을 설정하는 방법

##소개

Azure 주요 자격 증명 모음을 만든 후에는 키와 암호 정보를 저장하는 데 자격 증명 모음을 활용할 수 있게 됩니다. 사용자 응용 프로그램에서는 키 또는 암호 정보를 더 이상 유지할 필요가 없으며 대신, 필요에 따라 주요 자격 증명 모음에서 요청합니다. 이렇게 하면 응용 프로그램의 동작에 영향을 주지 않고 키 및 암호 정보를 업데이트할 수 있으며 키 및 암호 정보 관리 동작에 대한 다양한 가능성이 제공됩니다.

이 문서에서는 암호 정보(이 경우에는 응용 프로그램에서 액세스하는 Azure 저장소 계정 키)를 저장하는 데 Azure 주요 자격 증명 모음을 활용하는 예제를 안내합니다. 또한 해당 저장소 계정 키의 예약된 회전의 구현에 대해서도 살펴봅니다. 마지막으로, 예기치 않은 요청이 있을 때 주요 자격 증명 모음 감사 로그를 모니터하고 경고를 생성하는 방법도 살펴봅니다.

> [AZURE.NOTE] 이 자습서는 Azure 주요 자격 증명 모음의 초기 설정에 대해서는 자세히 다루지 않습니다. 이에 대한 설명은 [Azure 주요 자격 증명 모음 시작](key-vault-get-started.md)을 참조하세요. 또는 플랫폼 간 명령줄 인터페이스 지침에 대한 참조는[이 해당 자습서](key-vault-manage-with-cli.md)를 참조하세요.

##KeyVault 설정

응용 프로그램을 통해 Azure 주요 자격 증명 모음에서 암호 정보를 검색하기 위해서는 먼저 암호 정보를 만들어 자격 증명 모음에 업로드해야 합니다. 이 작업은 아래와 같이 PowerShell을 통해 손쉽게 수행할 수 있습니다.

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.

```powershell
Login-AzureRmAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 받고 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Azure 키 자격 증명 모음을 만드는 데 사용된 특정된 하나를 지정해야 합니다. 사용자 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzureRmSubscription
```

그런 다음 로깅하려는 사용자 키 자격 증명 모음과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

이 문서에서는 저장소 계정 키를 암호 정보로 저장하는 것에 대해 설명하므로 해당 저장소 계정 키를 가져와야 합니다.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

암호 정보(이 경우에는 저장소 계정 키)를 검색한 후에는 이를 보안 문자열로 변환한 후 주요 자격 증명 모음에 해당 값과 함께 암호 정보를 만들어야 합니다.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
그런 다음 방금 만든 암호 정보에 대한 URI를 가져오려고 합니다. 이 내용은 암호 정보를 검색하기 위해 주요 자격 증명 모음을 호출하는 이후 단계에서 사용됩니다. 다음 PowerShell 명령을 실행하고 암호 정보 URI인 'Id' 값을 기록해 둡니다.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##응용 프로그램 설정

이제 암호 정보가 저장되어 있으며 해당 암호 정보를 검색하고 코드에서 사용하려고 합니다. 이를 위해서는 몇 가지 단계를 거쳐야 하며 첫 번째 가장 중요한 단계는 응용 프로그램을 Azure Active Directory에 등록한 후 Azure 주요 자격 증명 모음에 응용 프로그램 정보를 전달하여 해당 응용 프로그램에서 요청을 허용할 수 있도록 하는 것입니다.

> [AZURE.NOTE] 응용 프로그램은 주요 자격 증명 모음과 동일한 Azure Active Directory에서 만들어야 합니다.

먼저 Azure Active Directory의 응용 프로그램 탭을 엽니다.

![Azure AD에서 응용 프로그램 열기](./media/keyvault-keyrotation/AzureAD_Header.png)

Azure AD에 새 응용 프로그램을 추가하려면 '추가'를 선택합니다.

![응용 프로그램 추가 선택](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

응용 프로그램 유형을 '웹 응용 프로그램 및/또는 웹 API'로 두고 응용 프로그램 이름을 지정합니다.

![응용 프로그램 이름 지정](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

응용 프로그램에 '로그온 URL' 및 '앱 ID URI'를 제공합니다. 이 데모에서는 사용자가 원하는 어떤 것이라도 가능하며 필요한 경우 나중에 변경할 수 있습니다.

![필요한 URI 제공](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

응용 프로그램이 Azure AD에 추가되었으면 응용 프로그램 페이지로 이동합니다. 해당 지점에서 '구성' 탭을 클릭한 후 '클라이언트 ID' 값을 찾아 복사합니다. 이후 단계에서 클라이언트 ID를 기록해 둡니다.

다음으로 Azure AD와 상호 작용할 수 있도록 응용 프로그램에 대한 키를 생성해야 합니다. '구성' 탭의 '키' 섹션 아래에서 만들 수 있습니다. 이후 단계에서 사용하기 위해 Azure AD 응용 프로그램에서 새로 생성된 키를 기록해 둡니다.

![Azure AD 앱 키](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

응용 프로그램에서 주요 자격 증명 모음으로 모든 호출을 설정하기 전에 주요 자격 증명 모음에 응용 프로그램 및 해당 권한에 대한 정보를 알려야 합니다. 다음 명령은 Azure AD 앱에서 자격 증명 모음 이름 및 클라이언트 ID를 가져와 주요 자격 증명 모음에 응용 프로그램에 대한 'Get' 액세스 권한을 부여합니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

이때 응용 프로그램 호출의 빌드를 시작할 준비가 되었습니다. 응용 프로그램에서 Azure 주요 자격 증명 모음 및 Azure Active Directory와 상호 작용하는 데 필요한 NuGet 패키지를 먼저 설치해야 합니다. Visual Studio 패키지 관리자 콘솔에서 다음 명령을 입력합니다. 이 문서를 작성할 당시 ActiveDirectory 패키지의 현재 버전은 3.10.305231913이므로 최신 버전을 확인하고 그에 따라 업데이트해야 할 수 있습니다.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

응용 프로그램 코드에서 Active Directory 인증에 대한 메서드를 보유할 클래스를 만듭니다. 이 예제에서 이 클래스는 'Utils'입니다. 그런 다음 아래 using을 추가해야 합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

다음으로, Azure AD에서 JWT 토큰을 검색하는 다음 메서드를 추가합니다. 유지 관리를 위해 하드 코딩된 문자열 값을 웹 또는 응용 프로그램 구성으로 이동하려고 할 수 있습니다.

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

마지막으로, 주요 자격 증명 모음을 호출하고 암호 정보 값을 검색하는 데 필요한 코드를 추가할 수 있습니다. 먼저 다음 using 문을 추가해야 합니다.

```csharp
using Microsoft.Azure.KeyVault;
```

다음으로, 주요 자격 증명 모음을 호출하고 암호 정보를 검색하는 메서드 호출을 추가합니다. 이 메서드에 이전 단계에서 저장한 암호 정보 URI를 제공합니다. 위에서 만든 Utils 클래스의 GetToken 메서드를 사용합니다.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

응용 프로그램을 실행하면 Azure Active Directory에 인증된 후 Azure 주요 자격 증명 모음에서 암호 정보 값을 검색합니다.

##Azure 자동화를 사용하여 키 회전

Azure 주요 자격 증명 모음 암호 정보로 저장하는 값을 위한 회전 전략을 구현하는 다양한 옵션이 있습니다. 수동 프로세스의 일부로 암호를 회전할 수 있으며 API 호출을 활용하여 프로그래밍 방식으로 회전하거나 자동화 스크립트 방식으로 회전할 수 있습니다. 이 문서의 목적에 따라 Azure 자동화와 결합된 Azure PowerShell을 활용하여 Azure 저장소 계정 액세스 키를 변경한 후 주요 자격 증명 모음 암호 정보를 새 키로 업데이트합니다.

Azure 자동화로 주요 자격 증명 모음의 암호 정보 값을 설정할 수 있도록 허용하기 위해서는 Azure 자동화 인스턴스를 설정할 때 'AzureRunAsConnection'이라는 이름으로 생성된 연결에 대한 클라이언트 ID를 가져와야 합니다. Azure 자동화 인스턴스에서 '자산'을 선택하여 이 ID를 가져올 수 있습니다. 여기에서 '연결'을 선택한 후 'AzureRunAsConnection' 서비스 사용자를 선택합니다. '응용 프로그램 ID'를 기록해 둡니다.

![Azure 자동화 클라이언트 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

자산 창에 잇는 동안 '모듈'을 선택할 수도 있습니다. 모듈에서 '갤러리'를 선택한 후 다음 각 모듈의 업데이트된 버전을 검색하여 '가져오기'합니다.

	Azure
	Azure.Storage	
	AzureRM.Profile
	AzureRM.KeyVault
	AzureRM.Automation
	AzureRM.Storage
	
> [AZURE.NOTE] 이 문서를 작성할 당시 위에 명시된 모듈만 아래 공유된 스크립트에 대해 업데이트되어야 합니다. 자동화 작업이 실패하는 것으로 확인되면 필요한 모든 모듈이 있고 해당 종속성을 가져왔는지 확인해야 합니다.

Azure 자동화 연결에 대한 응용 프로그램 ID를 검색한 후 이 응용 프로그램에서 자격 증명 모음의 암호 정보를 업데이트할 권한이 있음을 Azure 주요 자격 증명 모음에 알려야 합니다. 다음 PowerShell 명령을 사용하여 이 작업을 수행할 수 있습니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

다음으로 Azure 자동화 인스턴스 아래에서 'Runbooks' 리소스를 선택하고 'Runbook 추가'를 선택합니다. '빠른 생성'을 선택합니다. runbook의 이름을 지정하고 runbook 유형으로 'PowerShell'을 선택합니다. 선택적으로 설명을 추가합니다. 마지막으로 '만들기'를 클릭합니다.

![Runbook 만들기](./media/keyvault-keyrotation/Create_Runbook.png)

새 runbook에 대한 편집기 창에서 다음 PowerShell 스크립트를 붙여넣습니다.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

편집기 창에서 '테스트 창'을 선택하여 스크립트를 테스트할 수 있습니다. 스크립트가 오류 없이 실행되면 '게시' 옵션을 선택한 후 runbook 구성 창에서 runbook에 대한 일정을 다시 적용할 수 있습니다.

##주요 자격 증명 모음 감사 파이프라인

Azure 주요 자격 증명 모음을 설정할 때 감사를 켜서 주요 자격 증명 모음에 대해 발생하는 액세스 요청에 대한 로그를 수집할 수 있습니다. 이러한 로그는 지정된 Azure 저장소 계정에 저장된 후 끌어내기되고 모니터링 및 분석될 수 있습니다. 아래에서는 자격 증명 모음의 암호 정보가 웹앱의 앱 ID와 일치하는 앱에서 검색되는 경우 Azure Functions, Azure Logic Apps 및 주요 자격 증명 모음을 활용하여 전자 메일을 보내는 파이프라인을 만드는 시나리오를 안내합니다.

먼저 주요 자격 증명 모음에서 로깅을 사용하도록 설정해야 합니다. 이 작업은 다음 PowerShell 명령을 통해 수행할 수 있습니다(전체 설명은 [여기](key-vault-logging.md)에서 확인할 수 있음).

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

이 작업이 활성화되면 감사 로그가 지정된 저장소 계정으로 수집되기 시작합니다. 이러한 로그에는 주요 자격 증명 모음을 어떻게, 언제, 누가 액세스하는지에 대한 이벤트가 포함됩니다.

> [AZURE.NOTE] 주요 자격 증명 모음 작업 후 최대 10분 후에 로깅 정보에 액세스할 수 있습니다. 대부분의 경우 이것보다 빠릅니다.

다음은 [Azure 서비스 버스 큐를 만드는](../service-bus/service-bus-dotnet-get-started-with-queues.md) 단계입니다. 여기에 주요 자격 증명 모음 감사 로그가 푸시됩니다. 큐에서 논리 앱이 로그를 선택하고 작업을 수행합니다. 서비스 버스를 비교적 간단하게 만들기 위한 간략한 단계는 다음과 같습니다.

1. 서비스 버스 네임스페이스를 만듭니다(사용할 네임스페이스가 이미 있는 경우 2단계로 건너뜀).
2. 포털에서 서비스 버스를 찾아 안에 큐를 만들 네임스페이스를 선택합니다.
3. 새로 만들기를 선택하고 서비스 버스 -> 큐를 선택하고 필요한 세부 정보를 입력합니다.
4. 네임스페이스를 선택하고 _연결 정보_를 클릭하여 서비스 버스 연결 정보를 얻습니다. 다음 부분에 이 정보가 필요합니다.

다음으로, [Azure Function을 만들어](../azure-functions/functions-create-first-azure-function.md) 저장소 계정 내에서 주요 자격 증명 모음 로그를 폴링하고 새 이벤트를 선택합니다. 그러면 일정에 따라 트리거되는 함수가 됩니다.

Azure Function을 만듭니다(포털에서 새로 만들기 -> 함수 앱 선택). 만들기 중에 기존 호스팅 계획을 사용하거나 새 계획을 만들 수 있습니다. 동적 호스팅을 선택할 수도 있습니다. 함수 호스팅 옵션에 대한 자세한 내용은 [여기](../azure-functions/functions-scale.md)에서 찾을 수 있습니다.

Azure Function을 만든 경우 함수로 이동하여 타이머 함수 및 C#을 선택한 후 시작 화면에서 **만들기**를 클릭합니다.

![Azure Functions 시작 블레이드](./media/keyvault-keyrotation/Azure_Functions_Start.png)

_개발_ 탭에서 run.csx 코드를 다음 코드로 바꿉니다.

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

            //required to order by time as they may not be in the file
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
> [AZURE.NOTE] 위의 코드에 있는 변수를 주요 자격 증명 모음 로그가 기록된 저장소 계정, 이전에 생성한 서비스 버스 및 주요 자격 증명 모음 저장소 로그에 대한 특정 경로를 가리키도록 대체해야 합니다.

이 함수는 주요 자격 증명 모음 로그가 기록된 저장소 계정에서 최신 로그 파일을 선택하고 해당 파일에서 최신 이벤트를 가져와 서비스 버스 큐에 푸시합니다. 단일 파일에는 여러 이벤트(예: 한 시간 동안)가 있을 수 있으므로 함수가 선택된 최신 이벤트의 타임스탬프를 결정하기 위해 확인하는 _sync.txt_ 파일을 만듭니다. 그러면 동일한 이벤트를 여러 번 푸시하지 않게 됩니다. 이 _sync.txt_ 파일은 최근에 발생한 이벤트에 대한 타임스탬프를 포함합니다. 로그(로드된 경우)는 바르게 정렬되도록 타임스탬프를 기반으로 정렬해야 합니다.

이 함수에서는 Azure Functions에서 바로 사용할 수 없는 몇 가지 추가 라이브러리를 참조합니다. 이를 포함하려면 nuget을 사용하여 끌어오기 위해 Azure Functions가 필요합니다. _보기 파일_ 옵션을 선택합니다.

![보기 파일 옵션](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

_project.json_이라는 새 파일에 다음 콘텐츠를 추가합니다.

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
_저장_ 시 Azure Functions가 트리거되어 필요한 이진 파일이 다운로드됩니다.

**통합** 탭으로 전환하고 타이머 매개 변수에 함수 내에서 사용할 의미 있는 이름을 지정합니다. 위의 코드에서 타이머는 _myTimer_로 호출될 것으로 예상됩니다. 타이머에 대한 [CRON 식](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON)을 0 * * * * *로 지정하면 함수가 1분에 한 번 실행됩니다.

동일한 **통합** 탭에서 _Azure Blob 저장소_ 형식의 입력을 추가합니다. 그러면 함수에서 확인하는 최신 이벤트의 타임스탬프를 포함하는 _sync.txt_ 파일을 가리키게 됩니다. 함수 내에서 매개 변수 이름으로 사용할 수 있게 됩니다. 위의 코드에서 Azure Blob 저장소 입력에 대한 매개 변수 이름은 _inputBlob_로 예상됩니다. _sync.txt_ 파일이 있는 저장소 계정(동일하거나 다른 저장소 계정일 수 있음)을 선택하고 경로 필드에서 파일이 거주하는 경로를 {container-name}/path/to/sync.txt 형식으로 제공합니다.

_Azure Blob 저장소_ 형식의 출력을 추가합니다. 마찬가지로 입력에서 방금 정의한 _sync.txt_ 파일을 가리키게 됩니다. 이 파일은 함수에서 확인하는 최신 이벤트의 타임스탬프를 작성하는 데 사용됩니다. 위의 코드에서는 이 매개 변수를 _outputBlob_라고 합니다.

이제 함수가 준비되었습니다. **개발** 탭으로 다시 전환하고 코드를 _저장_해야 합니다. 출력 창에서 컴파일 오류를 확인하고 적절히 수정합니다. 컴파일된 경우 코드가 실행되며 분마다 주요 자격 증명 모음 로그를 확인하고 모든 새로운 이벤트를 정의된 서비스 버스 큐에 푸시합니다. 함수가 트리거될 때마다 로깅 정보가 로그 창에 기록된 것을 확인하게 됩니다.

###Azure 논리 앱

다음으로 함수가 서비스 버스 큐에 푸시하고 있는 이벤트를 선택할 Azure 논리 앱을 만들고 콘텐츠를 구문 분석한 후 일치하는 조건에 따라 전자 메일을 보내야 합니다.

새로 만들기 -> 논리 앱으로 이동하여 [논리 앱을 만듭니다](../app-service-logic/app-service-logic-create-a-logic-app.md).

논리 앱을 만든 후에는 이동하여 _편집_을 선택합니다. 논리 앱 편집기 내에서 _서비스 버스 큐_에서 관리되는 API를 선택하고 큐에 연결하기 위한 서비스 버스 자격 증명을 입력합니다.

![Azure 논리 앱 서비스 버스](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

다음으로, _조건 추가_를 선택합니다. 조건에서 _고급 편집기_로 전환한 후 다음을 입력합니다. 이때 APP\_ID를 웹앱의 실제 APP\_ID로 바꿉니다.

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

이 식은 기본적으로 들어오는 이벤트(서비스 버스 메시지의 본문)의 **appid** 속성이 앱의 **appid**가 아닌 경우 **false**를 반환합니다.

이제 _아니요인 경우, 아무 작업도 수행하지 않습니다..._ 옵션 아래에서 작업을 만듭니다.

![Azure 논리 앱 선택 작업](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

작업의 경우 _Office 365 - 전자 메일 보내기_를 선택합니다. 필드를 채워 정의된 조건에서 false를 반환하는 경우 보낼 전자 메일을 작성합니다. Office 365가 없는 경우 동일한 작업을 수행할 대안을 살펴볼 수 있습니다.

현재는 1분에 한 번 새로운 주요 자격 증명 모음 감사 로그를 확인하는 종단 간 파이프라인이 있습니다. 발견한 모든 새로운 로그를 서비스 버스 큐에 푸시합니다. 새 메시지가 큐에 도착하는 즉시 논리 앱이 트리거되며 이벤트 내의 appid가 호출 응용 프로그램의 앱 ID와 일치하지 않으면 전자 메일을 보냅니다.

<!---HONumber=AcomDC_0713_2016-->