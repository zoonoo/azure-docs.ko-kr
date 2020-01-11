---
title: JDBC/ODBC & Apache Thrift framework 관련 문제-Azure HDInsight
description: Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드할 수 없음
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894268"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임 워크를 사용 하 여 대량 데이터 집합을 다운로드 하려고 하면 다음과 유사한 오류 메시지가 표시 됩니다.

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>원인

이 예외는 serialization 프로세스에서 허용 되는 것 보다 많은 버퍼 공간을 사용 하려는 경우에 발생 합니다. Spark 2.0.0에서 클래스 `org.apache.spark.serializer.KryoSerializer`는 Apache Thrift 소프트웨어 프레임 워크를 통해 데이터에 액세스할 때 개체를 직렬화 하는 데 사용 됩니다. 다른 클래스는 네트워크를 통해 전송 되거나 serialize 된 형식으로 캐시 되는 데이터에 사용 됩니다.

## <a name="resolution"></a>해상도

`Kryoserializer` 버퍼 값을 늘립니다. `spark.kryoserializer.buffer.max` 라는 키를 추가 하 고 `Custom spark2-thrift-sparkconf`의 spark2 config에서 `2048`로 설정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
