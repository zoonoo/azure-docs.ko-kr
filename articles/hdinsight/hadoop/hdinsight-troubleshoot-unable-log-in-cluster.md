---
title: Azure HDInsight 클러스터에 로그인 할 수 없습니다.
description: Azure HDInsight에서 Apache Hadoop 클러스터에 로그인 할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289018"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터에 로그인 할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Azure HDInsight 클러스터에 로그인 할 수 없습니다.

## <a name="cause"></a>원인

이유는 다를 수 있습니다. 클러스터 또는 앱 대시보드에 로그인 하는 경우 "클러스터 로그인" 또는 HTTP 자격 증명을 사용 합니다. 원격에서 연결하는 경우, SSH(Secure Shell) 또는 원격 데스크톱 자격 증명을 사용합니다.

## <a name="resolution"></a>해결 방법

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

* 비공개 모드의 새 브라우저 탭에서 클러스터 대시보드를 열어봅니다.

* SSH 자격 증명을 회수할 수 없는 경우 [AMBARI UI 내에서 자격 증명을 다시 설정할](../hdinsight-administer-use-portal-linux.md#change-passwords)수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]