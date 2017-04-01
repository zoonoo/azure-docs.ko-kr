---
title: "패킷 캡처를 사용하여 Azure Functions로 자동 관리 네트워크 모니터링 수행 | Microsoft Docs"
description: "이 문서에서는 Azure Network Watcher에서 경고로 트리거된 패킷 캡처를 만드는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>패킷 캡처를 사용하여 Azure Functions로 자동 관리 네트워크 모니터링 수행

Network Watcher 패킷 캡처는 가상 컴퓨터 간에 트래픽을 추적하는 캡처 세션을 만듭니다. 캡처 파일은 모니터링할 트래픽만 추적하도록 정의된 필터를 포함할 수 있습니다. 이 데이터는 저장소 BLOB이나 게스트 컴퓨터에 로컬로 저장됩니다. 이 기능은 Azure Functions와 같은 다른 자동화 시나리오에서 원격으로 시작할 수 있습니다. 패킷 캡처는 정의된 네트워크 예외에 따라 사전 캡처를 실행하는 기능을 제공합니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다.

Azure에 배포된 리소스는 연중 무휴(24/7) 실행됩니다. 사용자 또는 직원은 연중 무휴 실행되는 모든 리소스의 상태를 적극적으로 모니터링할 수 없습니다. 오전 2시에 문제가 발생하면 어떻게 하나요?

Azure 에코시스템 내에서 Network Watcher, Alerting 및 Functions를 사용하면 데이터와 도구를 사용하여 네트워크의 문제에 사전에 대체하고 문제를 해결할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 예제에서는 VM이 평소보다 더 많은 TCP 세그먼트를 보내고 있으며 이를 경고하려고 합니다. TCP 세그먼트는 예로 사용되며 경고 조건을 사용할 수 있습니다. 경고를 보낸 경우, 통신이 증가한 이유를 알기 위해 패킷 수준 데이터를 포함하려고 하므로 컴퓨터를 일반 통신으로 반환하는 단계를 수행할 수 있습니다.
이 시나리오에서는 Network Watcher의 기존 인스턴스가 있고 유효한 가상 컴퓨터가 사용되는 리소스 그룹이 있다고 가정합니다.

## <a name="scenario"></a>시나리오

이 프로세스를 자동화하려면, 인시던트가 발생할 때 VM에서 트리거할 경고를 만들고 연결하며 Network Watcher를 호출할 Azure Function을 만들고 연결합니다.

이 시나리오의 경우

* 패킷 캡처를 시작하는 Azure 함수를 만듭니다.
* 가상 컴퓨터에 경고 규칙을 만듭니다.
* Azure 함수를 호출할 경고 규칙을 구성합니다.

## <a name="creating-an-azure-function-and-overview"></a>Azure Function 만들기 및 개요

첫 번째 단계는 경고를 처리할 Azure Function을 만들고 패킷 캡처를 만드는 것입니다. 

다음 목록은 수행되는 워크플로 개요입니다.

1. 경고는 VM에서 트리거됩니다.
1. 경고는 웹후크를 통해 Azure Function을 호출합니다.
1. Azure Function은 경고를 처리하고 Network Watcher 패킷 캡처 세션을 시작합니다.
1. 패킷 캡처는 VM에서 실행되고 트래픽을 수집합니다. 
1. 이 캡처 파일은 검토 및 진단을 위해 저장소 계정에 업로드됩니다. 

Azure Function 만들기는 [첫 번째 Azure Function 만들기](../azure-functions/functions-create-first-azure-function.md)에 따라 포털에서 수행할 수 있습니다. 이 예제에서는 HttpTrigger-PowerShell 형식 함수를 선택했습니다. 이 예제에는 사용자 지정이 필요하며 다음 단계에서 설명합니다.

![함수 예제][functions1]

> [!NOTE]
> PowerShell 템플릿은 실험적이며 완전한 지원을 제공하지 않습니다.

## <a name="adding-modules"></a>모듈 추가

Network Watcher PowerShell cmdlet을 사용하려면 최신 PowerShell 모듈을 함수 앱에 업로드해야 합니다.

1. 최신 Azure PowerShell 모듈이 설치된 로컬 컴퓨터에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    이렇게 하면 Azure PowerShell 모듈의 로컬 경로가 제공됩니다. 이러한 폴더는 이후 단계에서 사용됩니다. 이 시나리오에서 사용되는 모듈은 다음과 같습니다.

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![PowerShell 폴더][functions5]

1. **함수 앱 설정** > **App Service 편집기로 이동**으로 차례로 이동합니다.

    ![Kudu 함수][functions2]

1. AlertPacketCapturePowershell 폴더를 마우스 오른쪽 단추로 클릭하고 **azuremodules**라는 폴더를 만듭니다. 필요한 각 모듈에 대한 하위 폴더를 계속 만듭니다.

    ![Kudu 함수][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. **AzureRM.Network** 하위 폴더를 마우스 오른쪽 단추로 클릭하고 **파일 업로드**를 클릭합니다. Azure 모듈이 설치된 위치로 이동하고 로컬 AzureRM.Network 폴더에서 폴더의 모든 파일을 선택하고 **확인**을 클릭합니다.  AzureRM.Profile 및 AzureRM.Resources에 대해 이러한 단계를 반복합니다.

    ![파일 업로드][functions6]

1. 완료되면 로컬 컴퓨터의 각 폴더에 PowerShell 모듈 파일이 있어야 합니다.

    ![PowerShell 파일][functions7]

## <a name="authentication"></a>인증

PowerShell cmdlet을 사용하려면 인증해야 합니다. 함수 앱에서 인증을 구성해야 합니다. 이렇게 하려면 환경 변수를 구성하고 암호화된 키 파일을 함수 앱에 업로드해야 합니다.

> [!note]
> 이 시나리오에서는 Azure Functions를 사용하여 인증을 구현하는 방법에 대한 하나의 예제를 제공하며, 이를 수행하는 다른 방법들이 있습니다.

### <a name="encrypted-credentials"></a>암호화된 자격 증명

다음 PowerShell 스크립트는 **PassEncryptKey.key**라는 키 파일을 만들고, 제공된 암호의 암호화된 버전을 제공합니다.  이 암호는 인증에 사용되는 Azure AD 응용 프로그램에 대해 정의된 것과 동일한 암호입니다.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

함수 앱의 App Service 편집기에서 **AlertPacketCapturePowerShell** 아래에 **keys**라는 폴더를 만들고, 이전의 PowerShell 샘플로 만든 **PassEncryptKey.key** 파일을 업로드합니다.

![키 함수][functions8]

### <a name="retrieving-values-for-environment-variables"></a>환경 변수 값 검색

필요한 최종 구성은 인증에 대한 값에 액세스하는 데 필요한 환경 변수를 설정하는 것입니다. 다음은 만드는 환경 변수의 목록입니다.

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

클라이언트 ID는 Azure Active Directory에 있는 응용 프로그램의 응용 프로그램 ID입니다.

1. 사용할 응용 프로그램이 아직 없으면 다음 예제를 실행하여 응용 프로그램을 만듭니다.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > 응용 프로그램을 만들 때 사용되는 암호는 이전에 키 파일을 저장할 때 만든 암호와 동일해야 합니다.

1. Azure Portal에서 **구독** > 사용할 구독 선택 > **액세스 제어(IAM)**로 차례로 이동합니다.

    ![IAM 함수][functions9]

1. 사용할 계정을 선택하고 [속성]을 클릭합니다. 응용 프로그램 ID를 복사합니다.

    ![응용 프로그램 ID 함수][functions10]

#### <a name="azuretenant"></a>AzureTenant

다음 PowerShell 샘플을 실행하여 테넌트 ID를 얻습니다.

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

AzureCredPassword 환경 변수의 값은 다음 PowerShell 샘플을 실행하여 얻는 값입니다. 이 예제는 이전의 **암호화된 자격 증명** 섹션에서 보여 준 것과 동일한 예제입니다. 필요한 값은 `$Encryptedpassword` 변수의 출력입니다.  이 암호는 PowerShell 스크립트를 사용하여 암호화한 서비스 주체 암호입니다.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>환경 변수 저장

1. 함수 앱으로 이동하여 **함수 앱 설정** > **앱 설정 구성**을 차례로 클릭합니다.

    ![앱 설정 구성][functions11]

1. 환경 변수와 해당 값을 앱 설정에 추가하고 **저장**을 클릭합니다.

    ![앱 설정][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>경고 처리 및 패킷 캡처 세션 시작

이제 Azure Function 내에서 Network Watcher를 호출할 차례입니다. 요구 사항에 따라 이 함수의 구현이 달라집니다. 하지만 코드의 일반적인 흐름은 다음과 같습니다.

1. 입력 매개 변수 처리
2. 기존 패킷 캡처를 쿼리하여 한도 확인 및 이름 충돌 해결
3. 적절한 매개 변수를 사용하여 패킷 캡처 만들기
4. 완료될 때까지 패킷 캡처를 주기적으로 폴링
5. 사용자에게 패킷 캡처 세션이 완료됨을 알림

다음 예제는 Azure 함수에서 사용할 수 있는 PowerShell입니다. subscriptionId, resourceGroupName 및 storageAccountName에 대해 바꿔야 하는 값이 있습니다.

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

함수를 만들었으면 함수와 연결된 URL을 호출하는 경고를 구성해야 합니다. 이 값을 얻으려면 **</> 함수 URL 가져오기**를 클릭합니다. 

![함수 URL 1 찾기][functions13]

함수 앱의 함수 URL을 복사합니다.

![함수 URL 2 찾기][2]

웹후크 POST 요청의 페이로드에 사용자 지정 속성이 필요한 경우 [Azure 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)을 참조하세요.

## <a name="configure-an-alert-on-a-vm"></a>VM에서 경고 구성

특정 메트릭이 여기에 할당된 임계값을 초과할 경우 개인에게 알리도록 경고를 구성할 수 있습니다. 이 예에서 경고는 전송된 TCP 세그먼트에 대해 이루어지지만 많은 다른 메트릭에 대해 경고를 트리거할 수 있습니다. 이 예에서는 함수를 호출하는 웹후크를 호출하도록 경고를 구성했습니다.

### <a name="create-the-alert-rule"></a>경고 규칙 만들기

기존 가상 컴퓨터로 이동하고 경고 규칙을 추가합니다. 경고 구성에 대한 보다 자세한 설명서는 [Azure Portal을 사용하여 Azure 서비스에 대한 경고 만들기](../monitoring-and-diagnostics/insights-alerts-portal.md)에서 확인할 수 있습니다. 

![가상 컴퓨터에 vm 경고 추가][1]

> [!NOTE]
> 일부 메트릭은 기본적으로 사용하지 않도록 설정되어 있습니다. [모니터링 및 진단 사용](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)을 방문하여 추가 메트릭을 설정하는 방법에 대해 자세히 알아보세요.

마지막으로 이전 단계의 URL을 경고에서 웹후크 텍스트 상자에 붙여넣습니다. **확인**을 클릭하여 경고 규칙을 저장합니다.

![URL을 경고 규칙에 붙여넣기][3]

## <a name="downloading-and-viewing-the-capture-file"></a>캡처 파일 다운로드 및 보기

저장소 계정에 대한 캡처를 저장하는 경우 포털을 통해서나 프로그래밍 방식으로 캡처 파일을 다운로드할 수 있습니다. 캡처 파일이 로컬에 저장된 경우 가상 컴퓨터에 로그인하여 캡처 파일을 검색합니다. 

Azure Storage 계정에서 파일을 다운로드하는 방법에 대한 지침은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/storage-dotnet-how-to-use-blobs.md)을 참조하세요. 사용할 수 있는 다른 도구는 저장소 탐색기입니다. 저장소 탐색기에 대한 자세한 내용은 여기에 있는 [저장소 탐색기](http://storageexplorer.com/) 링크에서 찾을 수 있습니다.

캡처를 다운로드했으면 **.cap** 파일을 읽을 수 있는 도구를 사용하여 볼 수 있습니다. 다음은 이러한 두 도구에 대한 링크입니다.

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>다음 단계

[Wireshark로 패킷 캡처 분석](network-watcher-alert-triggered-packet-capture.md)에서 패킷 캡처를 보는 방법을 자세히 알아보세요.

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
