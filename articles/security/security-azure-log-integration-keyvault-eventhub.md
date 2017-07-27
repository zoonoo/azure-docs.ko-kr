---
title: "Event Hubs를 사용하여 Key Vault의 로그 통합 - Azure  | Microsoft Docs"
description: "Azure 로그 통합을 사용하여 Key Vault 로그를 SIEM에 제공하는 데 필요한 단계를 안내하는 자습서"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 06/29/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 9d1e5505ad6da7e24d51d2301f806695e0d9e2c2
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017



---

# <a name="azure-log-integration-tutorial---processing-azure-key-vault-events-using-event-hubs"></a>Azure 로그 통합 자습서 - Event Hubs를 사용하여 Azure Key Vault 이벤트 처리

AzLog(Azure 로그 통합)를 사용하면 기록된 이벤트를 검색하고 SIEM(보안 정보 및 이벤트 관리)에 제공할 수 있습니다. 이 자습서에서는 Key Vault 활동이 이벤트 허브에 기록되도록 하고 JSON 파일로 SIEM에 제공하는 프로세스를 안내합니다. 그런 다음 JSON 파일을 처리하도록 SIEM을 구성할 수 있습니다.

>[!NOTE]
이 자습서에 설명된 대부분의 단계는 Key Vault, 저장소 계정 및 이벤트 허브 구성과 관련이 있습니다. 특정 Azure 로그 통합 단계는 이 문서의 끝에 나와 있습니다.

각 단계의 이유 및 특정 항목에 대한 세부 정보를 제공하기 위해 다른 문서에 대한 적절한 링크가 포함되는 경우를 파악할 수 있는 정보가 제공됩니다.

이러한 단계의 대부분은 Event Hub 및 Key Vault의 구성을 수반합니다.  

- [Azure 키 자격 증명 모음](../key-vault/key-vault-whatis.md)
- [이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure 로그 통합](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>초기 설치

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. Azure 구독 및 관리자 권한이 있는 해당 구독의 계정. 구독이 없는 경우 [무료 구독](https://azure.microsoft.com/free/)을 이용할 수 있습니다.
2. Azure 로그 통합을 설치하기 위한 요구 사항을 충족하는, 인터넷에 액세스할 수 있는 시스템. 시스템은 클라우드 서비스에 있거나 온-프레미스에 호스트될 수 있습니다.
3. [Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) 설치됨.
  - 원격 데스크톱을 사용하여 2단계에서 언급한 시스템에 연결합니다.
  - Azure 로그 통합 설치 관리자를 복사합니다. [설치 파일을 다운로드](https://www.microsoft.com/download/details.aspx?id=53324)할 수 있습니다.
  - 설치 관리자를 시작하고 사용권 계약 내용에 동의합니다.
  - 원격 분석 정보의 제공 여부를 결정하고 확인란을 선택된 상태로 두거나, 사용 정보를 Microsoft에 전송하지 않으려는 경우 선택을 취소합니다.
4. PowerShell 버전을 확인합니다.
   - Windows Server 2016이 설치되어 있으면 PowerShell 5.0 이상이 있습니다. 다른 버전의 Windows 서버를 사용하는 경우 더 낮은 버전의 PowerShell이 설치되어 있을 수 있습니다. PowerShell 창에서 ```get-host```를 입력하면 버전을 확인할 수 있습니다.
   - PowerShell 5.0이 설치되어 있지 않으면 [여기](https://www.microsoft.com/download/details.aspx?id=50395)서 다운로드할 수 있습니다.
   - PowerShell 5.0 이상이 있는 경우 최신 버전의 Azure PowerShell 설치를 진행할 수 있습니다.
    - PowerShell 창을 열고
   </br>```Install-Module Azure```를 입력한 다음 **Enter** 키를 누릅니다. 설치를 실행하고, 설치가 완료되면 다음 단계로 이동합니다. </br>
   ```Install-Module AzureRM```을 입력하고 **Enter** 키를 누릅니다. 설치 단계를 진행합니다.

이제 최신 버전의 Azure PowerShell 및 Azure 로그 통합이 시스템에 설치되었으며 자습서의 이후 단계를 진행할 준비가 되었습니다.

>[!NOTE]
- Azure PowerShell 설치에 대한 자세한 내용과 단계는 [Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0) 문서를 참조하세요.
- Azure 로그 통합에 대한 자세한 설치 단계는 [Azure 로그 통합 Azure 진단 로깅 및 Windows 이벤트 전달](security-azure-log-integration-get-started.md) 문서를 참조하세요. 이 문서에서는 Azure 로그 통합 설치에 대해 설명할 뿐 아니라 일반적인 정보도 제공합니다.

## <a name="creating-supporting-infrastructure-elements"></a>지원 인프라 요소 만들기

1. 관리자 권한 PowerShell 창을 열고 **c:\Program Files\Microsoft Azure Log Integration**으로 이동합니다.
2. 제일 먼저 수행할 단계는 AzLog Cmdlet을 가져오는 것입니다. LoadAzLogModule.ps1 스크립트를 실행하면 됩니다(다음 명령의 ".\" 주의). **.\LoadAzLogModule.ps1**을 입력하고 <Enter> 키를 누릅니다.
아래 그림과 같이 표시됩니다. </br>

    ![로드된 모듈 목록](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. **Login-AzureRmAccount**를 입력하고 Enter 키를 누릅니다. 로그인 창이 시작됩니다. 이 자습서에 사용할 구독에 대한 자격 증명 정보를 입력합니다.

    >[!NOTE]
    이 컴퓨터에서 Azure에 처음 로그인하는 경우 Microsoft에서 PowerShell 사용 데이터를 수집하도록 허용하라는 메시지가 표시됩니다. 이 기능은 Azure PowerShell을 개선하는 데 사용되므로, 사용하도록 설정하는 것이 좋습니다.

4. 데이터 수집 프롬프트에서 진행 방법을 결정하고 성공적으로 인증하면 로그온되고 아래와 같이 일부 정보가 화면에 표시됩니다. 구독 정보를 적어둡니다.

    ![로드된 모듈 목록](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. 나중에 사용되는 일부 값을 저장할 몇 개의 변수를 만들어야 합니다.
    - 아래의 PowerShell 줄을 각각 입력하고 **Enter** 키를 누릅니다. 각 줄 옆에 있는 주석에 주의하세요. 환경에 맞게 값을 조정해야 할 수도 있습니다.
        - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’```(구독 이름은 다를 수 있으며, 이전 명령의 출력에서 확인할 수 있습니다.)
        - ```$location = 'West US'```(이 변수는 리소스를 만들 위치를 전달하는 데 사용됩니다. 선택한 다른 위치로 변경할 수 있습니다.)
        - ```$random = Get-Random```
        - ``` $name = 'azlogtest' + $random```(이름은 임의로 지정할 수 있지만 소문자와 숫자만 포함해야 합니다.)
        - ``` $storageName = $name```(저장소 계정 이름에 사용됩니다.)
        - ```$rgname = $name ```(리소스 그룹 이름에 사용됩니다.)
        - ``` $eventHubNameSpaceName = $name```(이벤트 허브 네임스페이스 이름입니다.)
6. 다음으로, 작업할 구독을 지정해야 합니다.
    - ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```을 입력하고 **Enter** 키를 누릅니다.
7. 리소스 그룹을 만듭니다.
    - ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```을 입력하고 **Enter** 키를 누릅니다. 리소스 그룹이 생성됩니다. 이때 $rg를 입력하고 **Enter** 키를 누르면 아래 표시된 정보와 유사한 출력이 나타납니다.

        ![로드된 모듈 목록](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. 다음으로, 상태 정보를 추적하는 데 사용할 저장소 계정을 만듭니다.
    - ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. 이벤트 허브 네임스페이스를 만듭니다. 이 네임스페이스는 이벤트 허브를 만드는 데 필요합니다.
    - ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. 정보 공급자와 함께 사용할 규칙 ID를 가져옵니다.
    - ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. 이제 가능한 모든 Azure 위치를 가져오고 이후 단계에서 사용할 수 있는 변수에 이름을 추가합니다.
    - ```$locationObjects = Get-AzureRMLocation```
    - ```$locations = @('global') + $locationobjects.location```이때 $locations를 입력하고 **Enter** 키를 누르면 get-AzureRmLocation에서 반환되는 추가 정보 없이 위치 이름이 표시됩니다.
12. 이제 Azure Resource Manager 로그 프로필을 만들 수 있습니다. [Azure 활동 로그 개요](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) 문서에서 Azure 로그 프로필에 대한 자세한 정보를 얻을 수 있습니다.
    - ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```

>[!NOTE]
로그 프로필을 만드는 중 오류 메시지가 표시될 수도 있습니다. Get-AzureRmLogProfile 및 Remove-AzureRmLogProfile에 대한 설명서를 검토할 수 있습니다. Get-AzureRmLogProfile을 실행하는 경우 로그 프로필에 대한 정보가 표시됩니다. ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```을 입력하면 기존 로그 프로필을 삭제할 수 있습니다.


![Resource Manager 프로필 오류](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="creating-a-key-vault"></a>Key Vault 만들기

먼저 다음을 입력하여 Azure Key Vault를 만듭니다.

```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

그런 다음 Key Vault에 대한 로깅을 구성합니다.

```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generating-log-activity"></a>로그 작업 생성

 로그 작업을 생성하려면 Key Vault에 요청을 보내야 합니다. 키 생성, 암호 저장 또는 Key Vault에서 암호 읽기 등의 작업은 로그 항목을 만듭니다.

 - 다음 단계에서는 단순히 현재 저장소 키를 표시합니다.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
 - 다음 줄에서는 새 **key2**를 생성합니다.
    - ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
 - 이제 키를 다시 표시하고 **key2**에 다른 값이 포함된 것을 확인할 수 있습니다.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
- 최종적으로, 암호를 설정하고 읽으면 추가 로그 항목이 생성됩니다.
    - ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```
    - ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

        ![반환된 암호](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configuring-azure-log-integration"></a>Azure 로그 통합 구성

Key Vault에서 이벤트 허브에 기록하는 데 필요한 요소를 모두 구성했으므로 이제 Azure 로그 통합을 구성하는 단계를 수행해야 합니다.

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

각 이벤트 허브에 대해 AzLog 명령을 실행합니다.

1.  ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2.  ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

마지막 몇 개의 명령을 1분 정도 실행하면 json 파일이 생성됩니다. **c:\users\AzLog\EventHubJson** 디렉터리를 모니터링하여 이를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 로그 통합 FAQ](security-azure-log-integration-faq.md)
- [Azure 로그 통합 시작](security-azure-log-integration-get-started.md)
- [Azure 리소스의 로그를 SIEM 시스템에 통합](security-azure-log-integration-overview.md)

