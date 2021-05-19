---
title: Azure HDInsight 스토리지에 많은 파일이 있는 경우 Apache Spark가 느려짐
description: Azure HDInsight에서 Azure Storage 컨테이너에 파일이 너무 많으면 Apache Spark 작업이 느리게 실행됨
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: d256292956b42c65722ea5f9c87d6835297d1d40
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219096"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure HDInsight에서 Azure Storage 컨테이너에 파일이 너무 많으면 Apache Spark 작업이 느리게 실행됨

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight 클러스터를 실행할 때 파일/하위 폴더가 많으면 Azure 스토리지 컨테이너에 쓰는 Apache Spark 작업이 느려집니다. 예를 들어 새 컨테이너에 쓸 때는 20초가 걸리지만 200k 파일이 있는 컨테이너에 쓸 때는 약 2분이 걸립니다.

## <a name="cause"></a>원인

이는 알려진 Spark 문제입니다. 속도 저하는 Spark 작업 실행 중 `ListBlob` 및 `GetBlobProperties` 작업에서 비롯됩니다.

파티션을 추적하기 위해 Spark는 디렉터리 구조에 대한 정보가 포함된 `FileStatusCache`를 유지 관리해야 합니다. Spark는 이 캐시를 사용하여 경로를 구문 분석하고 사용 가능한 파티션을 인식할 수 있습니다. 파티션 추적의 이점은 Spark가 데이터를 읽을 때 필요한 파일만 접촉한다는 것입니다. 이 정보를 최신 상태로 유지하기 위해 새 데이터를 작성할 때 Spark는 디렉터리 아래의 모든 파일을 나열하고 이 캐시를 업데이트해야 합니다.

Spark 2.1에서는 쓰기가 끝날 때마다 캐시를 업데이트할 필요가 없지만 Spark는 기존 파티션 열이 현재 쓰기 요청에서 제안된 파티션 열과 일치하는지 여부를 확인하므로 모든 쓰기를 시작할 때 작업을 나열하게 됩니다.

Spark 2.2에서는 추가 모드로 데이터를 작성할 때 이 성능 문제를 해결해야 합니다.

Spark 2.3에서는 Spark 2.2와 동일한 동작이 예상됩니다.

## <a name="resolution"></a>해결 방법

분할된 데이터 세트를 만들 때는 `FileStatusCache`를 업데이트하기 위해 Spark에서 나열해야 하는 파일 수를 제한하는 파티션 구성표를 사용하는 것이 중요합니다.

N % 100 == 0(100은 예)인 경우 N번째 마이크로 일괄 처리에 대해 기존 데이터를 Spark로 로드할 수 있는 다른 디렉터리로 이동합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
