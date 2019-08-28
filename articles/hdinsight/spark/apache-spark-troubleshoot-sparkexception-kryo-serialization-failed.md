---
title: Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드할 수 없음
description: Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드할 수 없음
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 4283de9d9046cc63ee10731c05b70850648ff981
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668886"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드할 수 없음

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드 하려고 하면 다음과 유사한 오류 메시지가 표시 됩니다.

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>원인

이 예외는 serialization 프로세스에서 허용 되는 것 보다 많은 버퍼 공간을 사용 하려는 경우에 발생 합니다. Spark 2.0.0에서 클래스 `org.apache.spark.serializer.KryoSerializer` 는 Apache Thrift 소프트웨어 프레임 워크를 통해 데이터에 액세스 하는 경우 개체를 직렬화 하는 데 사용 됩니다. 다른 클래스는 네트워크를 통해 전송 되거나 serialize 된 형식으로 캐시 되는 데이터에 사용 됩니다.

## <a name="resolution"></a>해결 방법

버퍼 값 `Kryoserializer` 을 늘립니다. 라는 `spark.kryoserializer.buffer.max` 키를 추가 하 고의 spark2 `2048` config `Custom spark2-thrift-sparkconf`에서로 설정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
