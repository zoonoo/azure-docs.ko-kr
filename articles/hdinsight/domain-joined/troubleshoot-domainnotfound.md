---
title: Azure HDInsight에서 DomainNotFound 오류로 클러스터 생성실패
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 해결 가능성 해결
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776236"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 DomainNotFound 오류로 클러스터 생성실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

HDI 보안(엔터프라이즈 보안 패키지) `DomainNotFound` 클러스터 생성에 오류 메시지가 표시됩니다.

## <a name="cause"></a>원인

잘못된 DNS 설정.

## <a name="resolution"></a>해결 방법

도메인 에 가입된 클러스터가 배포되면 HDI는 AAD DS(각 클러스터에 대해)에서 내부 사용자 이름과 암호를 만들고 모든 클러스터 노드를 이 도메인에 조인합니다. 도메인 조인은 Samba 도구를 사용하여 수행됩니다. 다음 필수 조건이 충족되는지 확인합니다.

* 도메인 이름은 DNS를 통해 확인되어야 합니다.
* 도메인 컨트롤러의 IP 주소는 클러스터가 배포되는 가상 네트워크의 DNS 설정에 설정되어야 합니다.
* 가상 네트워크가 AAD DS의 가상 네트워크로 피어있는 경우 수동으로 수행해야 합니다.
* DNS 전달자를 사용하는 경우 도메인 이름이 가상 네트워크 내에서 올바르게 확인되어야 합니다.
* NSG(보안 정책)는 도메인 조인을 차단해서는 안 됩니다.

### <a name="additional-debugging-steps"></a>추가 디버깅 단계

* 동일한 서브넷에 Windows VM을 배포하거나, 사용자 이름과 암호를 사용하여 컴퓨터에 도메인을 조인하거나(제어판 UI를 통해 수행할 수 있음)

* 동일한 서브넷에 우분투 VM을 배포하고 도메인이 컴퓨터에 가입합니다.
  * 기계에 SSH
  * sudo su
  * 사용자 이름 및 암호로 스크립트 실행
  * 스크립트는 ping, 필요한 구성 파일을 만든 다음 도메인을 만듭니다. 성공하면 DNS 설정이 양호합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
