---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504901"
---
|Name |설명 |정책 |버전 |
|---|---|---|---|
|[\[미리 보기\]: 배포 - 가상 머신에서 Azure Monitor 및 Azure Security 에이전트를 사용하도록 필수 구성 요소 구성](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Azure Monitor 및 Azure Security 에이전트를 자동으로 설치하도록 머신을 구성합니다. Security Center는 에이전트에서 이벤트를 수집하고 이를 사용하여 보안 경고 및 맞춤형 강화 작업(권장 사항)을 제공합니다. 감사 레코드를 저장할 머신과 동일한 지역에 리소스 그룹 및 Log Analytics 작업 영역을 만듭니다. 이 정책은 일부 지역의 VM에만 적용됩니다. |5 |1.0.0-preview |
|[Virtual Machine Scale Sets에 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 Virtual Machine Scale Sets에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 VM에 확장을 적용해야 합니다. CLI에서 이는 az vmss update-instances입니다. |6 |1.0.1 |
|[VM용 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 VM(가상 머신)에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |10 |2.0.0 |
