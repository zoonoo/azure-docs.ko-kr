---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170371"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |감사, 사용 안 함 |1.0.0 |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 사용 안 함 |1.0.0 |
