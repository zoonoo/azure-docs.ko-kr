---
title: Azure HDInsight 스토리지에 많은 파일이 있을 때 아파치 스파크 가 느려집니다.
description: Azure 저장소 컨테이너Azure HDInsight에 많은 파일이 포함되어 있을 때 아파치 스파크 작업이 느리게 실행됩니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894336"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure HDInsight에서 Azure Storage 컨테이너에 파일이 너무 많으면 Apache Spark 작업이 느리게 실행됨

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터를 실행할 때 많은 파일/하위 폴더가 있을 때 Azure 저장소 컨테이너에 쓰는 아파치 스파크 작업이 느려집니다. 예를 들어 새 컨테이너에 쓸 때 20초가 걸리지만 200k 파일이 있는 컨테이너에 쓸 때는 약 2분이 걸립니다.

## <a name="cause"></a>원인

알려진 스파크 문제입니다. 느림은 Spark `ListBlob` `GetBlobProperties` 작업 실행 중 및 작업에서 비롯됩니다.

파티션을 추적하려면 Spark는 `FileStatusCache` 디렉터리 구조에 대한 정보가 포함된 을 유지 관리해야 합니다. 이 캐시를 사용하여 Spark는 경로를 구문 분석하고 사용 가능한 파티션을 인식할 수 있습니다. 파티션을 추적하면 Spark가 데이터를 읽을 때 필요한 파일만 터치한다는 이점이 있습니다. 이 정보를 최신 상태로 유지하려면 새 데이터를 작성할 때 Spark는 디렉터리 아래에 모든 파일을 나열하고 이 캐시를 업데이트해야 합니다.

Spark 2.1에서 모든 쓰기 후에 캐시를 업데이트할 필요는 없지만 Spark는 기존 파티션 열이 현재 쓰기 요청에서 제안된 항목과 일치하는지 여부를 확인하므로 모든 쓰기를 시작할 때 목록 작업으로 이어질 수도 있습니다.

Spark 2.2에서 부속 모드로 데이터를 작성할 때이 성능 문제를 해결해야 합니다.

## <a name="resolution"></a>해결 방법

분할된 데이터 집합을 만들 때 Spark가 `FileStatusCache`을 업데이트하기 위해 나열해야 하는 파일 수를 제한하는 분할 체계를 사용하는 것이 중요합니다.

N % 100 == 0 (100은 예에 불과함)이 있는 모든 N번째 마이크로 일괄 처리의 경우 기존 데이터를 Spark에서 로드할 수 있는 다른 디렉터리로 이동합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
