---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 59a47ca7d3b3b42a5822e61c37ee90cb238c0778
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170051"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[Batch 계정의 진단 로그를 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |2.0.0 |
|[Batch 계정에서 메트릭 경고 규칙을 구성해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |필요한 메트릭을 사용할 수 있도록 설정하는 Batch 계정에서 메트릭 경고 규칙 구성 감사 |AuditIfNotExists, 사용 안 함 |1.0.0 |
