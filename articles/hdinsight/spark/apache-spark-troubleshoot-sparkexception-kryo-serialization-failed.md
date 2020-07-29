---
title: JDBC/ODBC 및 Apache Thrift 프레임워크 관련 문제 - Azure HDInsight
description: Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임워크를 사용하여 대용량 데이터 세트를 다운로드할 수 없음
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653590"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임워크를 사용하여 대용량 데이터 세트를 다운로드할 수 없음

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Azure HDInsight에서 JDBC/ODBC 및 Apache Thrift 소프트웨어 프레임워크를 사용하여 대용량 데이터 세트를 다운로드하려고 하면 다음과 유사한 오류 메시지가 표시됩니다.

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>원인

이 예외는 serialization 프로세스에서 허용되는 것보다 많은 버퍼 공간을 사용하려는 경우에 발생합니다. Spark 2.0.0에서 클래스 `org.apache.spark.serializer.KryoSerializer`는 Apache Thrift 소프트웨어 프레임워크를 통해 데이터에 액세스할 때 개체를 직렬화하는 데 사용됩니다. 다른 클래스는 네트워크를 통해 전송되거나 직렬화된 형식으로 캐시되는 데이터에 사용됩니다.

## <a name="resolution"></a>해결 방법

`Kryoserializer` 버퍼 값을 늘립니다. `spark.kryoserializer.buffer.max`라는 키를 추가하고 `Custom spark2-thrift-sparkconf` 아래의 spark2 config에서 `2047`로 설정합니다. 영향을 받는 모든 구성 요소를 다시 시작합니다.

> [!IMPORTANT]
> `spark.kryoserializer.buffer.max`의 값은 2048보다 작아야 합니다. 소수 값은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
