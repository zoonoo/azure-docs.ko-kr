---
title: Azure 트래픽 분석 스키마 업데이트-3 월 2020 일 | Microsoft Docs
description: 트래픽 분석 스키마에 새 필드가 있는 샘플 쿼리입니다.
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
ms.openlocfilehash: be69e7fc0c836257b92aa8573826fe1e8a36d770
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948445"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>트래픽 분석 스키마에 새 필드가 있는 샘플 쿼리 (8 월 2019 스키마 업데이트)

[트래픽 분석 로그 스키마](./traffic-analytics-schema.md) 는 **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s** 의 새 필드를 포함 합니다. 새 필드는 원본 및 대상 Ip에 대 한 정보를 제공 하 고 쿼리를 단순화 합니다.

다음 몇 개월 후에는 **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d** 와 같은 오래 된 필드가 사용 되지 않습니다.

다음 세 가지 예제에서는 이전 필드를 새 필드로 바꾸는 방법을 보여 줍니다.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>예 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s 및 PublicIPs_s 필드

AzurePublic 및 ExternalPublic 흐름의 **FlowDirection_s** 필드에서 원본 및 대상 사례를 유추할 필요가 없습니다. 네트워크 가상 어플라이언스에 대 한 **FlowDirection_s** 필드를 사용 하는 것은 적합 하지 않을 수도 있습니다.

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

## <a name="example-2-nsgrules_s-field"></a>예제 2: NSGRules_s 필드

이전 필드는 다음 형식을 사용 합니다.

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

더 이상 NSG (네트워크 보안 그룹)를 통해 데이터를 집계 하지 않습니다. 업데이트 된 스키마에는 하나의 NSG만 포함 **NSGList_s** . 또한 **NSGRules** 에는 하나의 규칙만 포함 되어 있습니다. 예제에 표시 된 대로 다른 필드에서 복잡 한 서식 지정을 제거 했습니다.

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

## <a name="example-3-flowcount_d-field"></a>예제 3: FlowCount_d 필드

NSG를 통해 데이터를 클럽 하지 않으므로 **FlowCount_d** 는 간단 합니다.

**AllowedInFlows_d**  +  **DeniedInFlows_d**  +  **AllowedOutFlows_d**  +  **DeniedOutFlows_d**

4 개 필드 중 하나만 0이 아닙니다. 다른 세 필드는 0이 됩니다. 필드는 흐름이 캡처된 NIC의 상태와 카운트를 표시 하도록 채워집니다.

다음 조건을 설명 합니다.

- 흐름이 허용 된 경우 "허용 되는" 접두사 필드 중 하나가 채워집니다.
- 흐름이 거부 된 경우 "거부 됨" 이라는 접두사가 있는 필드 중 하나가 채워집니다.
- 흐름이 인바운드 인 경우에는 "InFlows_d" 필드 중 하나가 채워집니다.
- 흐름이 아웃 바운드 인 경우 "OutFlows_d" 접미사로 된 필드 중 하나가 채워집니다.

조건에 따라 채울 4 개 필드 중 어떤 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 질문과 대답을 보려면 [트래픽 분석 FAQ](traffic-analytics-faq.md)를 참조 하십시오.
- 기능에 대 한 자세한 내용은 [트래픽 분석 설명서](traffic-analytics.md)를 참조 하세요.