---
title: InvalidNetworkSecurityGroupSecurityRules 오류-Azure HDInsight
description: 클러스터 만들기가 실패 하 고 오류 오류 InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289100"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]