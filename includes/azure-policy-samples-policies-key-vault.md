---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170131"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[Key Vault의 진단 설정을 Event Hub에 배포](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |이 진단 설정이 누락된 Key Vault를 만들거나 업데이트할 때 Key Vault의 진단 설정을 배포하여 지역별 Event Hub로 스트림합니다. |deployIfNotExists |1.0.0 |
|[Key Vault의 진단 로그를 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[Key Vault 개체를 복구할 수 있어야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |이 정책은 Key Vault 개체를 복구할 수 없는 경우 감사합니다. Soft Delete 기능은 DELETE 작업 후에도 지정된 보존 기간(90일) 동안 리소스를 효율적으로 유지하는 동시에 개체가 삭제된 것처럼 보이게 합니다. '제거 보호'가 켜져 있으면 보존 기간인 90일이 지날 때까지 삭제된 상태의 자격 증명 모음이나 개체를 제거할 수 없습니다. 이러한 자격 증명 모음 및 개체는 계속 복구할 수 있으며, 고객에게 보존 정책을 준수하도록 합니다. |감사, 사용 안 함 |1.0.0 |
