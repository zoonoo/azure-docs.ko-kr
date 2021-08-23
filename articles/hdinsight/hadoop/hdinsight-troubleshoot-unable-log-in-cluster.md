---
title: HDInsight 클러스터에 로그인할 수 없음
description: Azure HDInsight에서 Apache Hadoop 클러스터에 로그인할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: eab5097ef27865b2d5d46370f60aebdac3739d24
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299684"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>시나리오: HDInsight 클러스터에 로그인할 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터에 로그인할 수 없습니다.

## <a name="cause"></a>원인

이유는 다를 수 있습니다. 클러스터 또는 앱 대시보드에 로그인할 때는 "클러스터 로그인" 또는 HTTP 자격 증명을 사용해야 합니다. 원격에서 연결하는 경우, SSH(Secure Shell) 또는 원격 데스크톱 자격 증명을 사용합니다.

## <a name="resolution"></a>해결 방법

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

* 비공개 모드의 새 브라우저 탭에서 클러스터 대시보드를 열어봅니다.

* SSH 자격 증명을 기억할 수 없으면 [Ambari UI 내에서 자격 증명을 다시 설정](../hdinsight-administer-use-portal-linux.md#change-passwords)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]