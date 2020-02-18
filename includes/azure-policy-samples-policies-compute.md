---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170101"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[허용되는 가상 머신 SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |이 정책을 통해 조직에서 배포할 수 있는 가상 컴퓨터 SKU 집합을 지정할 수 있습니다. |거부 |1.0.0 |
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 https://aka.ms/asr-doc 를 참조하세요. |auditIfNotExists |1.0.0 |
|[관리 디스크를 사용하지 않는 VM 감사](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |이 정책은 관리 디스크를 사용하지 않는 VM을 감사합니다. |감사 |1.0.0 |
|[Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |이 정책은 VM이 맬웨어 방지 프로그램 확장으로 구성되지 않은 경우 기본 구성으로 Microsoft IaaSAntimalware 확장을 배포합니다. |deployIfNotExists |1.0.0 |
|[Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[보호 서명을 자동으로 업데이트하려면 Azure용 Microsoft Antimalware를 구성해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |이 정책은 Microsoft Antimalware 보호 서명의 자동 업데이트로 구성되지 않은 Windows 가상 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |이 정책은 Microsoft IaaSAntimalware 확장이 배포되지 않은 Windows Server VM을 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[승인된 VM 확장만 설치해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |이 정책은 승인되지 않은 가상 머신 확장을 관리합니다. |감사, 거부, 사용 안 함 |1.0.0 |
|[Virtual Machine Scale Sets에 자동 OS 이미지 패치 필요](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |이 정책은 매달 최신 보안 패치를 안전하게 적용하여 항상 Virtual Machines의 보안을 유지하도록 Virtual Machine Scale Sets에 자동 OS 이미지 패치를 사용하도록 설정합니다. |deny |1.0.0 |
|[연결되지 않은 디스크는 암호화되어야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |이 정책은 암호화를 사용하지 않고 연결되지 않은 모든 디스크를 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |가상 머신에 대해 새 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, ARM 기반 배포 및 거버넌스, 관리 ID 액세스, 비밀을 위해 Key Vault에 액세스, AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |감사, 거부, 사용 안 함 |1.0.0 |
