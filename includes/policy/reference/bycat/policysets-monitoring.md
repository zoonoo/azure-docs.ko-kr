---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dc886c780a5400128f0e7396743c71e0584676cb
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311617"
---
|Name |Description |정책 |버전 |
|---|---|---|---|
|[Virtual Machine Scale Sets에 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 Virtual Machine Scale Sets에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 VM에 확장을 적용해야 합니다. CLI에서 이는 az vmss update-instances입니다. |6 |1.0.1 |
|[VM용 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 VM(가상 머신)에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |10 |2.0.0 |
