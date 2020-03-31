---
title: 요청바디아파치 스파크 앱에서 큰 오류 - Azure HDInsight
description: 네이티브 Azure파일시스템 ... RequestBodyTooLarge Azure HDInsight에서 아파치 스파크 스트리밍 응용 프로그램에 대한 로그에 나타납니다
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894394"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"네이티브 AzureFile 시스템... RequestBodyTooLarge"는 HDInsight의 아파치 스파크 스트리밍 앱 로그인에 나타납니다.

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

오류: `NativeAzureFileSystem ... RequestBodyTooLarge` 아파치 스파크 스트리밍 앱의 드라이버 로그에 나타납니다.

## <a name="cause"></a>원인

Spark 이벤트 로그 파일이 WASB의 파일 길이 제한에 부딪혔을 수 있습니다.

Spark 2.3에서 각 Spark 앱은 하나의 스파크 이벤트 로그 파일을 생성합니다. Spark 스트리밍 앱의 Spark 이벤트 로그 파일은 앱이 실행되는 동안 계속 증가합니다. 현재 WASB의 파일에는 50000 블록 제한이 있으며 기본 블록 크기는 4MB입니다. 따라서 기본 구성에서 최대 파일 크기는 195GB입니다. 그러나 Azure Storage는 최대 블록 크기를 100MB로 늘려 단일 파일 제한을 4.75TB로 효과적으로 늘렸습니다. 자세한 내용은 [Azure 스토리지의 확장성 및 성능 목표](../../storage/blobs/scalability-targets.md)를 참조하세요.

## <a name="resolution"></a>해결 방법

이 오류에는 세 가지 해결 방법이 있습니다.

* 블록 크기를 최대 100MB까지 늘립니다. Ambari UI에서 HDFS 구성 `fs.azure.write.request.size` 속성을 수정하거나 `Custom core-site` 섹션에서 만듭니다. 속성을 더 큰 값(예: 33554432)으로 설정합니다. 업데이트된 구성을 저장하고 영향을 받는 구성 요소를 다시 시작합니다.

* 스파크 스트리밍 작업을 주기적으로 중지하고 다시 제출합니다.

* HDFS를 사용하여 스파크 이벤트 로그를 저장합니다. 스토리지에 HDFS를 사용하면 클러스터 크기 조정 또는 Azure 업그레이드 중에 Spark 이벤트 데이터가 손실될 수 있습니다.

    1. Ambari `spark.eventlog.dir` `spark.history.fs.logDirectory` UI를 통해 변경합니다.

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. HDFS에서 디렉터리 만들기:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Ambari UI를 통해 영향을 받는 모든 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
