---
title: Event Hubs를 사용하여 Azure Key Vault의 로그 통합 | Microsoft Docs
description: Azure 로그 통합을 사용하여 Key Vault 로그를 SIEM에 제공하는 데 필요한 단계를 안내하는 자습서
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 5614cc6fa01ddd10d670fdf429051a8e024550fc
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298198"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration 자습서: Event Hubs를 사용하여 Azure Key Vault 이벤트 처리

>[!IMPORTANT]
> Azure 로그 통합 기능을 06/15/2019 지원이 중지 될 예정입니다. AzLog 다운로드는 2018년 6월 27일부로 사용할 수 없습니다. 향후 작업 진행 방향에 대한 지침은 게시물 [Azure Monitor를 사용하여 SIEM 도구와 통합](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)을 검토하세요. 

Azure 로그 통합을 사용하여 기록된 이벤트를 검색하고 SIEM(보안 정보 및 이벤트 관리) 시스템에 제공할 수 있습니다. 이 자습서는 Azure 로그 통합을 사용하여 Azure Event Hubs를 통해 획득한 로그를 처리하는 방법의 예제를 보여 줍니다.

Azure 로그를 통합하는 가장 좋은 방법은 SIEM 공급업체의 Azure Monitor 커넥터를 사용하고 다음 [지침](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)을 따르는 것입니다. 하지만, SIEM 공급업체에서 Azure Monitor에 대한 커넥터를 제공하지 않는 경우에는 해당 커넥터를 사용할 수 있을 때까지 임시 해결책으로 Azure Log Integration을 사용할 수 있습니다(Azure Log Integration에서 해당 SIEM이 지원되는 경우).

 
이 자습서를 사용하여 예제 단계를 따르고 각 단계가 솔루션을 어떻게 지원하는지 이해하면 Azure 로그 통합 및 Event Hubs가 연동하는 방식에 익숙해질 수 있습니다. 그런 다음, 여기서 학습한 내용을 바탕으로 회사 고유의 요구 사항을 지원하는 자체 단계를 만들 수 있습니다.

> [!WARNING]
> 이 자습서의 단계와 명령은 복사하여 붙여넣을 수 없습니다. 예제일 뿐입니다. PowerShell 명령을 라이브 환경에서 "있는 그대로" 사용하지 마세요. 이러한 명령은 고유 환경에 맞게 사용자 지정해야 합니다.


이 자습서에서는 Azure Key Vault 활동이 이벤트 허브에 기록되도록 하고 JSON 파일로 SIEM 시스템에 제공하는 프로세스를 안내합니다. 그런 다음 JSON 파일을 처리하도록 SIEM 시스템을 구성할 수 있습니다.

>[!NOTE]
>이 자습서의 단계는 대부분 Key Vault, 저장소 계정 및 이벤트 허브 구성과 관련이 있습니다. 특정 Azure 로그 통합 단계는 이 자습서의 끝에 나와 있습니다. 프로덕션 환경에서 다음 단계를 수행하지 마세요. 랩 환경 전용으로 작성되었습니다. 프러덕션 환경에서 사용하기 전에 단계를 사용자 지정해야 합니다.

함께 제공되는 정보는 각 단계를 수행하는 이유를 이해하는 데 도움이 됩니다. 다른 아티클에 대한 링크는 특정 토픽에 대한 자세한 정보를 제공합니다.

이 자습서에 언급된 서비스에 대한 자세한 내용은 다음을 참조하세요. 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure 로그 통합](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>초기 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 아티클의 단계를 완료하려면 다음이 필요합니다.

* Azure 구독 및 관리자 권한이 있는 해당 구독의 계정. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
 
* Azure 로그 통합을 설치하기 위한 요구 사항을 충족하는, 인터넷에 액세스할 수 있는 시스템. 시스템은 클라우드 서비스에 있거나 온-프레미스에 호스트될 수 있습니다.

* Azure Log Integration이 설치되어 있어야 합니다. 설치하려면:

   a. 원격 데스크톱을 사용하여 2단계에서 언급한 시스템에 연결합니다.   
   b. Azure 로그 통합 설치 관리자를 시스템에 복사합니다. 다. 설치 관리자를 시작하고 Microsoft 소프트웨어 사용 조건에 동의합니다.

* 원격 분석 정보를 입력하는 경우 확인란을 선택한 상태로 둡니다. 사용 정보를 Microsoft로 보내지 않으려면 확인란의 선택을 취소합니다.

   Azure 로그 통합 및 설치 방법에 대한 자세한 내용은 [Azure 로그 통합, Azure Diagnostics 로깅 및 Windows 이벤트 전달](security-azure-log-integration-get-started.md)을 참조하세요.

* 최신 PowerShell 버전입니다.

   Windows Server 2016이 설치되어 있는 경우 PowerShell 5.0 이상이 설치되어 있는 것입니다. 다른 버전의 Windows Server를 사용 중인 경우 이전 버전의 PowerShell이 설치되어 있을 수 있습니다. PowerShell 창에서 ```get-host```를 입력하면 버전을 확인할 수 있습니다. PowerShell 5.0이 설치되어 있지 않으면 [다운로드](https://www.microsoft.com/download/details.aspx?id=50395)할 수 있습니다.

   이상에서 만든 후 PowerShell 5.0의 지침에 따라 최신 버전 설치를 진행할 수 있습니다 [Azure PowerShell 설치](/powershell/azure/install-az-ps)합니다.


## <a name="create-supporting-infrastructure-elements"></a>지원 인프라 요소 만들기

1. 관리자 권한 PowerShell 창을 열고 **C\Program Files\Microsoft Azure Log Integration**으로 이동합니다.
1. LoadAzLogModule.ps1 스크립트를 실행하여 AzLog cmdlet을 가져옵니다. `.\LoadAzLogModule.ps1` 명령을 입력합니다. (해당 명령에서 ".\" 확인) 다음과 유사한 결과가 표시됩니다.</br>

   ![로드된 모듈 목록](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. `Connect-AzAccount` 명령을 입력합니다. 로그인 창에서 이 자습서에 사용할 구독에 대한 자격 증명 정보를 입력합니다.

   >[!NOTE]
   >이 컴퓨터에서 Azure에 처음 로그인하는 경우 Microsoft에서 PowerShell 사용 데이터를 수집하도록 허용하라는 메시지가 표시됩니다. 이 기능은 Azure PowerShell을 개선하는 데 사용되므로, 이 데이터 수집을 사용하도록 설정하는 것이 좋습니다.

1. 인증이 성공 하면 로그인 되어 있습니다. 구독 ID 및 구독 이름을 메모해 두세요. 이후 단계를 완료하는 데 필요합니다.

1. 나중에 사용할 값을 저장하는 변수를 만듭니다. 다음 각 PowerShell 줄을 입력합니다. 환경에 맞게 값을 조정해야 할 수도 있습니다.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(구독 이름은 다를 수 있습니다. 이는 이전 명령의 출력에서 볼 수 있습니다.)
    - ```$location = 'West US'```(이 변수는 리소스를 만들 위치를 전달하는 데 사용됩니다. 이 변수를 선택한 위치로 변경할 수 있습니다.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(이름은 임의로 지정할 수 있지만 소문자와 숫자만 포함해야 합니다.)
    - ```$storageName = $name```(이 변수는 저장소 계정 이름에 사용됩니다.)
    - ```$rgname = $name```(이 변수는 리소스 그룹 이름에 사용됩니다.)
    - ```$eventHubNameSpaceName = $name```(이벤트 허브 네임스페이스의 이름입니다.)
1. 작업할 구독을 지정합니다.
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. 리소스 그룹 만들기:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   이때 `$rg`를 입력하면 이 스크린샷과 유사한 출력이 나타납니다.

   ![리소스 그룹 생성 후 출력](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. 상태 정보를 추적하는 데 사용할 저장소 계정을 만듭니다.
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. 이벤트 허브 네임스페이스를 만듭니다. 이 네임스페이스는 이벤트 허브를 만드는 데 필요합니다.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. 정보 공급자와 함께 사용할 규칙 ID를 가져옵니다.
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. 가능한 모든 Azure 위치를 가져오고 이후 단계에서 사용할 수 있는 변수에 이름을 추가합니다.
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    입력 하면 `$locations` 이때 AzLocation Get에서 반환 되는 추가 정보 없이 위치 이름이 표시 합니다.
1. Azure Resource Manager 로그 프로필을 만듭니다. 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Azure 로그 프로필에 대한 자세한 내용은 [Azure 활동 로그 개요](../azure-monitor/platform/activity-logs-overview.md)를 참조하세요.

> [!NOTE]
> 로그 프로필을 만들려고 할 때 오류 메시지가 표시될 수도 있습니다. 다음 Get AzLogProfile 및 AzLogProfile 제거에 대 한 설명서를 검토할 수 있습니다. Get-AzLogProfile를 실행 하는 경우 로그 프로필에 대 한 정보를 표시 됩니다. ```Remove-AzLogProfile -name 'Log Profile Name'``` 명령을 입력하면 기존 로그 프로필을 삭제할 수 있습니다.
>
>![Resource Manager 프로필 오류](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

1. 키 자격 증명 모음을 만듭니다.

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. 키 자격 증명 모음에 대한 로깅을 구성합니다.

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>로그 작업 생성

로그 작업을 생성하려면 Key Vault에 요청을 보내야 합니다. 키 생성, 암호 저장 또는 Key Vault에서 암호 읽기 등의 작업은 로그 항목을 만듭니다.

1. 현재 저장소 키를 표시합니다.
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 새로운 **key2**를 생성합니다.
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. 키를 다시 표시하고 **key2**에 다른 값이 포함된 것을 확인합니다.
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 암호를 설정하고 읽어서 추가 로그 항목을 생성합니다.
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![반환된 암호](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure 로그 통합 구성

Key Vault에서 이벤트 허브에 기록하는 데 필요한 요소를 모두 구성했으므로 이제 Azure 로그 통합을 구성해야 합니다.

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

각 이벤트 허브에 대해 AzLog 명령을 실행합니다.

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

마지막 두 개의 명령을 1분 정도 실행하면 JSON 파일이 생성됩니다. **C:\users\AzLog\EventHubJson** 디렉터리를 모니터링하여 이를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 로그 통합 FAQ](security-azure-log-integration-faq.md)
- [Azure 로그 통합 시작](security-azure-log-integration-get-started.md)
- [Azure 리소스의 로그를 SIEM 시스템에 통합](security-azure-log-integration-overview.md)
