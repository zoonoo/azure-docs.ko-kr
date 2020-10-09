---
title: Azure HDInsight 저장소에 많은 파일이 있는 경우 Apache Spark 느림
description: Azure 저장소 컨테이너에 Azure HDInsight의 많은 파일이 포함 되어 있으면 Apache Spark 작업이 느리게 실행 됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894336"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure HDInsight에서 Azure Storage 컨테이너에 파일이 너무 많으면 Apache Spark 작업이 느리게 실행됨

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터를 실행 하는 경우 파일/하위 폴더가 많은 경우 Azure storage 컨테이너에 기록 하는 Apache Spark 작업 속도가 느려집니다. 예를 들어 새 컨테이너에 쓰는 데 20 초가 걸리지만 계의 파일이 있는 컨테이너에 쓰는 데 약 2 분이 걸립니다.

## <a name="cause"></a>원인

이것은 알려진 Spark 문제입니다. 속도 저하는 `ListBlob` Spark 작업을 실행 하는 동안 및 작업에서 제공 `GetBlobProperties` 됩니다.

파티션을 추적 하기 위해 Spark는 `FileStatusCache` 디렉터리 구조에 대 한 정보를 포함 하는를 유지 관리 해야 합니다. Spark는이 캐시를 사용 하 여 경로를 구문 분석 하 고 사용 가능한 파티션을 인식할 수 있습니다. 파티션을 추적 하는 경우 Spark는 데이터를 읽을 때 필요한 파일만 접촉 한다는 것입니다. 이 정보를 최신 상태로 유지 하기 위해 새 데이터를 작성할 때 Spark는 디렉터리 아래의 모든 파일을 나열 하 고이 캐시를 업데이트 해야 합니다.

Spark 2.1에서는 모든 쓰기 후 캐시를 업데이트할 필요가 없지만 Spark는 기존 파티션 열이 현재 쓰기 요청에서 제안 된 파티션 열과 일치 하는지 여부를 확인 하므로 모든 쓰기를 시작할 때 작업을 나열 하는 작업도 수행 합니다.

Spark 2.2에서 추가 모드를 사용 하 여 데이터를 작성할 때이 성능 문제를 해결 해야 합니다.

## <a name="resolution"></a>해결 방법

분할 된 데이터 집합을 만들 때를 업데이트 하기 위해 Spark에서 나열할 파일 수를 제한 하는 파티션 구성표를 사용 하는 것이 중요 합니다 `FileStatusCache` .

N %100 = = 0 (100만 예) 인 경우 n% = = 0 인 모든 n 번째 일괄 처리에 대해 기존 데이터를 다른 디렉터리로 이동 합니다 .이 디렉터리는 Spark에서 로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
