---
title: Apache Spark 앱의 RequestBodyTooLarge 오류-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge는 Azure HDInsight의 Apache Spark 스트리밍 앱에 대 한 로그에 표시 됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 2d2e929335f6af2ee24a81e719d9d0d899f7b8ef
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241847"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "HDInsight의 Apache Spark 스트리밍 앱 로그에 표시 됩니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

오류: `NativeAzureFileSystem ... RequestBodyTooLarge`은 Apache Spark 스트리밍 앱에 대 한 드라이버 로그에 표시 됩니다.

## <a name="cause"></a>원인

Spark 이벤트 로그 파일은 WASB에 대 한 파일 길이 제한에 도달할 수 있습니다.

Spark 2.3에서 각 Spark 앱은 하나의 Spark 이벤트 로그 파일을 생성 합니다. Spark 스트리밍 앱에 대 한 Spark 이벤트 로그 파일은 앱이 실행 되는 동안 계속 증가 합니다. 현재 WASB의 파일은 5만 블록 제한을 가지 며 기본 블록 크기는 4mb입니다. 따라서 기본 구성에서 최대 파일 크기는 195 GB입니다. 그러나 Azure storage는 최대 블록 크기를 100 MB로 늘려 효과적으로 단일 파일 제한을 4.75 TB까지 가져왔습니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)를 참조하세요.

## <a name="resolution"></a>해상도

이 오류에 사용할 수 있는 세 가지 솔루션이 있습니다.

* 블록 크기를 최대 100 MB로 늘립니다. Ambari UI에서 HDFS 구성 속성 `fs.azure.write.request.size` 수정 하거나 `Custom core-site` 섹션에서 만듭니다. 속성을 더 큰 값 (예: 33554432)으로 설정 합니다. 업데이트 된 구성을 저장 하 고 영향을 받는 구성 요소를 다시 시작 합니다.

* 정기적으로 spark-스트리밍 작업을 중지 하 고 다시 제출 합니다.

* HDFS를 사용 하 여 Spark 이벤트 로그를 저장 합니다. 저장소에 HDFS를 사용 하면 클러스터 크기 조정 또는 Azure 업그레이드 중에 Spark 이벤트 데이터가 손실 될 수 있습니다.

    1. Ambari UI를 통해 `spark.eventlog.dir` 및 `spark.history.fs.logDirectory`를 변경 합니다.

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. HDFS에서 디렉터리를 만듭니다.

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Ambari UI를 통해 영향을 받는 모든 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
