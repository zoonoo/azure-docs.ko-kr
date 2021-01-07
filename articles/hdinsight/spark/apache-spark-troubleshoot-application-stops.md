---
title: Azure HDInsight에서 24 일 후에 스트리밍 응용 프로그램이 중지 Apache Spark
description: 24 일간 실행 한 후 Apache Spark 스트리밍 응용 프로그램이 중지 되 고 로그 파일에 오류가 없습니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288029"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 24 일간 실행 한 후 스트리밍 응용 프로그램이 중지 Apache Spark

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

24 일간 실행 한 후 Apache Spark 스트리밍 응용 프로그램이 중지 되 고 로그 파일에 오류가 없습니다.

## <a name="cause"></a>원인

이 `livy.server.session.timeout` 값은 Apache Livy가 세션이 완료 될 때까지 대기 해야 하는 시간을 제어 합니다. 세션 길이가 값에 도달 하면 `session.timeout` Livy 세션과 응용 프로그램이 자동으로 종료 됩니다.

## <a name="resolution"></a>해결 방법

장기 실행 작업의 경우 `livy.server.session.timeout` AMBARI UI를 사용 하 여 값을 늘립니다. URL을 사용 하 여 Ambari UI에서 Livy 구성에 액세스할 수 있습니다 `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

`<yourclustername>`을 포털에 표시 된 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]