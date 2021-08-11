---
title: InvalidNetworkSecurityGroupSecurityRules 오류 - Azure HDInsight
description: ErrorCode InvalidNetworkSecurityGroupSecurityRules를 나타내며 클러스터 만들기 실패
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944375"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>시나리오: InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight에서 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

"서브넷으로 구성된 네트워크 보안 그룹의 보안 규칙에서 필요한 인바운드 및/또는 아웃바운드 연결을 허용하지 않습니다."와 비슷한 설명을 포함하는 `InvalidNetworkSecurityGroupSecurityRules` 오류 코드를 수신했습니다.

## <a name="cause"></a>원인

클러스터에 대해 인바운드 [네트워크 보안 그룹](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 규칙이 구성된 것이 문제인 것 같습니다.

## <a name="resolution"></a>해결 방법

Azure Portal로 이동하여 클러스터를 배포하려는 서브넷과 연결된 NSG를 식별합니다. **인바운드 보안 규칙** 섹션에서 이 규칙이 [여기](../control-network-traffic.md)에 언급된 IP 주소의 포트 443에 대한 인바운드 액세스를 허용하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]