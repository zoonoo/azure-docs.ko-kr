---
title: Cmdlet New-AzRecoveryServicesAsrVMNicConfig의 IPConfig 매개 변수 사용 중단 | Microsoft Docs
description: Cmdlet New-AzRecoveryServicesAsrVMNicConfig의 IPConfig 매개 변수 사용 중단에 대한 세부 정보 및 새 cmdlet New-AzRecoveryServicesAsrVMNicIPConfig 사용에 대한 정보
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2021
ms.author: rishjai
ms.openlocfilehash: 569687a6efca34e631b096b448d988c18b21a6c9
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326931"
---
# <a name="deprecation-of-ip-config-parameters-for-the-cmdlet-new-azrecoveryservicesasrvmnicconfig"></a>Cmdlet New-AzRecoveryServicesAsrVMNicConfig의 IP 구성 매개 변수 사용 중단

이 문서는 다음 시나리오의 고객에 사용할 수 있는 사용 중단, 사용 중단으로 인한 영향 및 대체 옵션을 설명합니다.

장애 조치(Failover) 또는 테스트 장애 조치(failover)를 위한 기본 IP 구성 설정 구성 이 cmdlet은 AzRecoveryServicesAsrVMNicConfig cmdlet을 사용하여 Azure DR 시나리오에 대한 모든 Azure 고객에게 영향을 줍니다.

> [!IMPORTANT]
> 고객은 최대한 빨리 수정 단계를 수행하여 해당 환경에서 중단을 방지하는 것이 좋습니다. 

## <a name="what-changes-should-you-expect"></a>어떤 사항이 변경되나요?

New-AzRecoveryServicesAsrVMNicConfig는 다음 매개 변수를 사용하여 FO/TFO의 IP 구성 값을 구성합니다.
- RecoveryVMSubnetName
- RecoveryNicStaticIPAddress
- RecoveryPublicIPAddressId
- RecoveryLBBackendAddressPoolId
- TfoVMSubnetName
- TfoNicStaticIPAddress
- TfoPublicIPAddressId
- TfoLBBackendAddressPoolId

이러한 매개 변수는 cmdlet에서 더 이상 허용되지 않습니다.

- 2021년 5월 4일부터 cmdlet AzRecoveryServicesAsrVMNicConfig에서 IP 구성 매개 변수 사용 중단에 대하여 Azure Portal 알림 및 전자 메일을 수신하게 됩니다.

- 기존 스크립트를 사용하는 경우에는 더 이상 지원되지 않습니다.
 
## <a name="alternatives"></a>대안 

대안으로 IP 구성의 FO/TFO 설정 구성에 대한 새로운 [AzRecoveryServicesAsrVMNicIPConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesasrvmnicipconfig) cmdlet이 도입되었습니다. 


## <a name="remediation-steps"></a>수정 단계

이러한 매개 변수를 제거하려면 스크립트를 수정해야 합니다. 대신, 새 cmdlet **AzRecoveryServicesAsrVMNicIPConfig** 를 사용하여 IP 구성 개체를 만듭니다. 다음 일러스트레이션을 참조하세요.

**기존 스크립트** 는 다음과 같이 작성되었습니다.
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -RecoveryVMSubnetName "default" -TfoVMSubnetName "default" -RecoveryNicStaticIPAddress "10.1.40.223" -TfoNicStaticIPAddress "10.33.0.223"

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

**아래와 같이** 스크립트를 수정합니다.
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# Create the config object for Primary IP Config
$ipConfig = New-AzRecoveryServicesAsrVMNicIPConfig  -IpConfigName <ipConfigName> -RecoverySubnetName "default" -TfoSubnetName "default" -RecoveryStaticIPAddress "10.1.40.223" -TfoStaticIPAddress "10.33.0.223"

$ipConfigs = @($ipConfig)

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -IPConfig $ipConfigs

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

## <a name="next-steps"></a>다음 단계
위에서 설명한 대로 스크립트를 수정합니다. 이 사용 중단에 대한 문의 사항이 있는 경우 Microsoft 지원에 문의하세요.