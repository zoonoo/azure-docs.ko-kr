---
title: Azure HDInsight에서 24일 후에 Apache Spark 스트리밍 응용 프로그램이 중지
description: Apache Spark 스트리밍 응용 프로그램이 24일 동안 실행된 후 중지되었는데 로그 파일에는 오류가 없습니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 8d446063c7e8836a1163e0b6327539b055c41476
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288940"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>시나리오: Apache Spark 스트리밍 응용 프로그램이 Azure HDInsight에서 24일 동안 실행된 후 중지됨

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Spark 스트리밍 응용 프로그램이 24일 동안 실행된 후 중지되었는데 로그 파일에는 오류가 없습니다.

## <a name="cause"></a>원인

이 `livy.server.session.timeout` 값은 Apache Livy가 세션이 완료될 때까지 대기해야 하는 시간을 제어합니다. 세션 길이가 `session.timeout` 값에 도달하면 Livy 세션과 응용 프로그램이 자동으로 종료됩니다.

## <a name="resolution"></a>해결 방법

장기 실행 작업의 경우 Ambari UI를 사용하여 `livy.server.session.timeout` 값을 늘립니다. URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`을 사용하여 Ambari UI에서 Livy 구성에 액세스할 수 있습니다.

`<yourclustername>`을 포털에 표시된 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]