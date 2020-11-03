---
title: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드 됩니다.
description: Apache Ambari Tez 뷰가 느리게 로드 되거나 Azure HDInsight에서 전혀 로드 되지 않을 수 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288846"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드 됩니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Ambari Tez 뷰가 느리게 로드 되거나 전혀 로드 되지 않을 수 있습니다. Ambari Tez 뷰를 로드 하는 경우 헤드 노드의 프로세스가 응답 하지 않는 것을 볼 수 있습니다.

## <a name="cause"></a>원인

Yarn ATS Api에 액세스 하는 경우 클러스터에 많은 수의 Hive 작업이 실행 될 때 10 월 2017 일 이전에 생성 된 클러스터의 성능이 저하 될 수 있습니다.

## <a name="resolution"></a>해결 방법

이 문제는 Oct 2017에서 해결 된 문제입니다. 클러스터를 다시 만들면이 문제가 다시 실행 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]