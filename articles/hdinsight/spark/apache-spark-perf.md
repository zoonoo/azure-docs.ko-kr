---
title: 성능을 위한 Spark 작업 최적화 - Azure HDInsight
description: Azure HDInsight에서 최상의 Apache Spark 클러스터 성능을 얻기 위한 일반적인 전략을 보여 줍니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780102"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>HDInsight에서 Apache Spark 작업 최적화

이 문서에서는 Azure HDInsight에서 Apache Spark 작업을 최적화하기 위한 제반 전략의 개요를 제공합니다.

## <a name="overview"></a>개요

Apache Spark 작업의 성능은 여러 요인에 따라 달라집니다. 이러한 성능 요소에는 데이터가 저장되는 방법, 클러스터가 구성되는 방식, 데이터를 처리할 때 사용되는 작업 등이 포함됩니다.

여기서 직면할 수 있는 일반적인 문제에는 크기가 다른 실행기, 장기 실행 작업, 카티전 작업으로 이어지는 작업 등으로 인한 메모리 제약이 포함됩니다.

이러한 문제(예: 캐싱)를 해결하는 데 도움이 될 뿐만 아니라 데이터 기울이기를 허용하는 다양한 전략들도 있습니다.

다음의 각 문서에서는 Spark 최적화의 다른 측면에 대한 일반적인 문제 및 해결 방법을 확인할 수 있습니다.

* [데이터 스토리지 최적화](optimize-data-storage.md)
* [데이터 처리 최적화](optimize-data-processing.md)
* [메모리 사용량 최적화](optimize-memory-usage.md)
* [클러스터 구성 최적화](optimize-cluster-configuration.md)

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그](apache-spark-job-debugging.md)
* [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
* [Apache Spark 설정 구성](apache-spark-settings.md)
