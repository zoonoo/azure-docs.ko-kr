---
title: InvalidNetworkSecurityGroupSecurityRules 오류-Azure HDInsight
description: 클러스터 만들기가 실패 하 고 오류 오류 InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 1662be3bedb9ec0f2dc21e98ffbd57d8f8ed5777
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540621"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>시나리오: InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight에서 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

`InvalidNetworkSecurityGroupSecurityRules`"서브넷으로 구성 된 네트워크 보안 그룹의 보안 규칙은 필요한 인바운드 및/또는 아웃 바운드 연결을 허용 하지 않습니다."와 비슷한 설명이 포함 된 오류 코드를 수신 합니다.

## <a name="cause"></a>원인

클러스터에 대해 구성 된 인바운드 [네트워크 보안 그룹](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 규칙에 문제가 있을 수 있습니다.

## <a name="resolution"></a>해결 방법

Azure Portal로 이동 하 여 클러스터가 배포 되는 서브넷과 연결 된 NSG를 식별 합니다. **인바운드 보안 규칙** 섹션에서 규칙이 [여기](../control-network-traffic.md)에 언급 된 IP 주소에 대 한 포트 443에 대 한 인바운드 액세스를 허용 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.