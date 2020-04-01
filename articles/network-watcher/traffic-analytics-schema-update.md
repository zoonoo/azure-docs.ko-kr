---
title: Azure 트래픽 분석 스키마 업데이트 - 2020년 3월 | 마이크로 소프트 문서
description: 트래픽 분석 스키마에서 새 필드가 있는 쿼리를 샘플링합니다.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396620"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>트래픽 분석 스키마에 새 필드가 있는 검색 쿼리(2019년 8월 스키마 업데이트)

[트래픽 분석 로그 스키마에는](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) **SrcPublicIPs_s,** **DestPublicIPs_s**, **NSGRule_s**. 새 필드는 원본 및 대상 IP에 대한 정보를 제공하며 쿼리를 단순화합니다.

앞으로 몇 개월 동안 **VMIP_s,** **Subscription_g,** **Region_s,** **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s FlowCount_d** **등**이전 분야는 더 이상 사용되지 않습니다.

다음 세 가지 예제는 이전 필드를 새 필드로 대체하는 방법을 보여 주었습니다.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>예제 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s 및 PublicIPs_s 필드

AzurePublic 및 ExternalPublic 흐름에 대한 **FlowDirection_s** 필드에서 소스 및 대상 사례를 유추할 필요가 없습니다. 또한 네트워크 가상 어플라이언스에 **FlowDirection_s** 필드를 사용하는 것은 부적절할 수 있습니다.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>예 2: NSGRules_s 필드

이전 필드는 형식을 사용했습니다.

<지수 값 0)>|<NSG_ 규칙 이름>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

더 이상 NSG(네트워크 보안 그룹)에서 데이터를 집계하지 않습니다. 업데이트된 스키마에서 **NSGList_s** NSG가 하나만 포함되어 있습니다. 또한 **NSGRules는** 하나의 규칙을 포함합니다. 예제와 같이 복잡한 서식을 여기와 다른 필드에서 제거했습니다.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>예 3: FlowCount_d 필드

우리는 NSG전반에 걸쳐 클럽 데이터를하지 않기 때문에, **FlowCount_d** 단순히 :

**AllowedInFlows_d** + **DeniedInFlows_d** + **AllowedOutFlows_d** + **DeniedOutFlows_d**

4개의 필드 중 하나만 0이 아닙니다. 나머지 세 필드는 0이 됩니다. 필드가 채워져 흐름이 캡처된 NIC의 상태 및 개수를 나타냅니다.

이러한 조건을 설명하려면 다음을 수행하십시오.

- 흐름이 허용된 경우 "허용된" 접두사 필드 중 하나가 채워집니다.
- 흐름이 거부된 경우 "거부됨" 접두사 필드 중 하나가 채워집니다.
- 흐름이 인바운드된 경우 "InFlows_d" 접미사 필드 중 하나가 채워집니다.
- 흐름이 아웃바운드인 경우 "OutFlows_d" 접미사 필드 중 하나가 채워집니다.

조건에 따라 4개 필드 중 어느 필드가 채워질지 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 자주 묻는 질문에 대한 답변은 [트래픽 분석 FAQ](traffic-analytics-faq.md)를 참조하세요.
- 기능에 대한 자세한 내용은 [트래픽 분석 설명서를](traffic-analytics.md)참조하십시오.
