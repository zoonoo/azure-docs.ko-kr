---
title: Azure HDInsight에서 DomainNotFound 오류가 발생 하 여 클러스터를 만들지 못했습니다.
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285560"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 DomainNotFound 오류가 발생 하 여 클러스터를 만들지 못했습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

HDI Secure (Enterprise Security Package) 클러스터 만들기는 오류 메시지와 함께 실패 `DomainNotFound` 합니다.

## <a name="cause"></a>원인

DNS 설정이 잘못 되었습니다.

## <a name="resolution"></a>해결 방법

도메인에 가입 된 클러스터를 배포 하는 경우 HDI는 각 클러스터에 대해 AAD DS에서 내부 사용자 이름 및 암호를 만들고 모든 클러스터 노드를이 도메인에 조인 합니다. 도메인 조인은 Samba 도구를 사용 하 여 수행 됩니다. 다음 필수 조건이 충족되는지 확인합니다.

* 도메인 이름은 DNS를 통해 확인 해야 합니다.
* 도메인 컨트롤러의 IP 주소는 클러스터가 배포 되는 가상 네트워크에 대 한 DNS 설정에서 설정 해야 합니다.
* 가상 네트워크가 AAD DS의 가상 네트워크와 피어 링 되는 경우 수동으로 수행 해야 합니다.
* DNS 전달자를 사용 하는 경우에는 가상 네트워크 내에서 도메인 이름이 올바르게 확인 되어야 합니다.
* NSGs (보안 정책)는 도메인 가입을 차단 하지 않아야 합니다.

### <a name="additional-debugging-steps"></a>추가 디버깅 단계

* 동일한 서브넷에 windows VM을 배포 하 고, 사용자 이름 및 암호를 사용 하 여 컴퓨터에 도메인 가입 (제어판 UI를 통해 수행 가능) 하거나

* 동일한 서브넷에 ubuntu VM을 배포 하 고 컴퓨터에 도메인 가입
  * 컴퓨터에 SSH
  * sudo su
  * 사용자 이름 및 암호를 사용 하 여 스크립트 실행
  * 스크립트가 ping을 수행 하 고 필요한 구성 파일을 만든 다음 도메인을 만듭니다. 성공 하면 DNS 설정이 양호 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]