---
title: Azure HDInsight의 WASB 파일 작업 디버그
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938747"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Azure HDInsight의 WASB 파일 작업 디버그

WASB 드라이버가 Azure Storage에서 시작한 작업을 이해하려는 경우가 있습니다. 클라이언트 쪽의 경우 WASB 드라이버는 **DEBUG** 수준에서 각 파일 시스템 작업에 대한 로그를 생성합니다. WASB 드라이버는 log4j를 사용하여 로깅 수준을 제어하고 기본값은 **INFO** 수준입니다. Azure Storage 서버 쪽 분석 로그는 [Azure Storage 분석 로깅](../../storage/common/storage-analytics-logging.md)을 참조하세요.

생성된 로그는 다음과 유사합니다.

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>파일 작업에 WASB 디버그 로그 켜기

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`로 이동합니다. 여기서 `CLUSTERNAME`은 Spark 클러스터의 이름입니다.

1. **고급 spark2-log4j-properties** 로 이동합니다.

    1. `log4j.appender.console.Threshold=INFO`을 `log4j.appender.console.Threshold=DEBUG`로 수정합니다.

    1. `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`를 추가합니다.

1. **고급 livy2-log4j-properties** 로 이동합니다.

    `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`를 추가합니다.

1. 변경 내용을 저장합니다.

## <a name="additional-logging"></a>추가 로깅

위의 로그는 파일 시스템 작업에 대한 높은 수준의 이해를 제공해야 합니다. 위의 로그가 여전히 유용한 정보를 제공하지 않거나 Blob Storage API 호출을 조사하려는 경우 `core-site`에 `fs.azure.storage.client.logging=true`를 추가합니다. 이 설정은 wasb 스토리지 드라이버에 대한 java sdk 로그를 사용하도록 설정하고 Blob Storage 서버에 대한 각 호출을 출력합니다. 조사 후 디스크를 빠르게 채울 수 있고 프로세스 속도가 느려질 수 있으므로 설정을 제거합니다.

백 엔드가 Azure Data Lake 기반인 경우 구성 요소에 대해 다음 log4j 설정을 사용합니다(예: spark/tez/hdfs).

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

로그에 대한 `/var/log/adl/adl.log` 로그인을 찾습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.