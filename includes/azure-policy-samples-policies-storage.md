---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170291"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[허용되는 스토리지 계정 SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |이 정책을 사용하면 조직에서 배포할 수 있는 스토리지 계정 SKU 세트를 지정할 수 있습니다. |거부 |1.0.0 |
|[스토리지 계정에 대한 무제한 네트워크 액세스 감사](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |스토리지 계정 방화벽 설정에서 무제한 네트워크 액세스를 감사합니다. 또는, 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크에서의 트래픽 또는 공용 인터넷 IP 주소 범위에 액세스 권한을 부여할 수 있습니다. |감사, 사용 안 함 |1.0.0 |
|[스토리지 계정에 Advanced Threat Protection 배포](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |이 정책은 스토리지 계정에서 Advanced Threat Protection을 사용하도록 설정합니다. |DeployIfNotExists, 사용 안 함 |1.0.0 |
|[스토리지 계정에 대해 지역 중복 스토리지를 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |이 정책은 지역 중복 스토리지가 활성화되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |1.0.0 |
|[스토리지 계정은 신뢰할 수 있는 Microsoft 서비스의 액세스를 허용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |스토리지 계정과 상호 작용하는 일부 Microsoft 서비스는 네트워크 규칙을 통해 액세스 권한을 부여할 수 없는 네트워크에서 작동합니다. 이러한 유형의 서비스가 의도한 대로 작동하도록 하려면 신뢰할 수 있는 Microsoft 서비스 세트에서 네트워크 규칙을 무시하도록 허용합니다. 그러면 이러한 서비스에서 강력한 인증을 사용하여 스토리지 계정에 액세스합니다. |감사, 거부, 사용 안 함 |1.0.0 |
|[스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |스토리지 계정에 새로운 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 Key Vault에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |감사, 거부, 사용 안 함 |1.0.0 |
