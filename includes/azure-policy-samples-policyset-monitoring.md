---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: e62ad440eb3f9af41f2a2cddd6d882887638e799
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262474"
---
|속성 |Description |정책 |버전 |
|---|---|---|---|
|[Virtual Machine Scale Sets에 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 Virtual Machine Scale Sets에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 VM에 확장을 적용해야 합니다. CLI에서 이는 az vmss update-instances입니다. |6 |1.0.0-미리 보기 |
|[VM용 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 VM(Virtual Machines)에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |6 |1.0.0-미리 보기 |
