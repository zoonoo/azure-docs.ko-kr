---
title: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드됩니다
description: Azure HDInsight에서 Apache Ambari Tez 보기가 느리게 로드되거나 로드되지 않을 수 있습니다
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0078a01e691844e6ad2e3b4133fa4d0913b240db
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290578"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>시나리오: Azure HDInsight Apache Ambari Tez 보기가 느리게 로드됩니다

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Ambari Tez 보기가 느리게 로드되거나 로드되지 않을 수 있습니다. Ambari Tez 보기를 로드할 때 헤드 노드의 프로세스가 응답하지 않는 것을 볼 수 있습니다.

## <a name="cause"></a>원인

2017년 10월 이전에 생성한 클러스터에서 실행되는 Hive 작업이 많은 경우, Yarn ATS API 엑세스 성능이 저하될 수 있습니다.

## <a name="resolution"></a>해결 방법

이는 2017년 10월에 해결된 문제입니다. 클러스터를 다시 생성하면 이 문제가 다시 발생하지 않습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]