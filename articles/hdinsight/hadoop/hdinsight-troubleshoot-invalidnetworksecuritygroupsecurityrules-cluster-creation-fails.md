---
title: 잘못된 네트워크 보안그룹보안규칙 오류 - Azure HDInsight
description: 오류 코드 유효 하지 않은 네트워크 보안 그룹 보안 규칙으로 클러스터 생성 실패
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894140"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>시나리오: 잘못된네트워크보안그룹보안규칙 - Azure HDInsight에서 클러스터 생성 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

"서브넷으로 `InvalidNetworkSecurityGroupSecurityRules` 구성된 네트워크 보안 그룹의 보안 규칙에서 필요한 인바운드 및/또는 아웃바운드 연결을 허용하지 않음"과 유사한 설명이 있는 오류 코드가 나타납니다.

## <a name="cause"></a>원인

클러스터에 대해 구성된 인바운드 [네트워크 보안 그룹](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 규칙에 문제가 있는 것일 수 있습니다.

## <a name="resolution"></a>해결 방법

Azure 포털로 이동하여 클러스터가 배포되는 서브넷과 연결된 NSG를 식별합니다. **인바운드 보안 규칙** 섹션에서 [여기에](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)언급된 IP 주소에 대해 포트 443에 대한 인바운드 액세스를 허용하는 규칙이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
