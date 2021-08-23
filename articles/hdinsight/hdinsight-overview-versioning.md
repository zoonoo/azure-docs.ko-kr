---
title: 버전 관리 소개 - Azure HDInsight
description: Azure HDInsight에서 버전 관리가 작동하는 방식에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: b7b23a1d7e549d5e1e5b712d2290722158d49f38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535479"
---
# <a name="how-versioning-works-in-hdinsight"></a>HDInsight에서 버전 관리가 작동하는 방식

HDInsight 서비스에는 리소스 공급자와 Apache Hadoop 구성 요소(클러스터에 배포)라는 두 가지 주요 구성 요소가 있습니다. 

## <a name="hdinsight-resource-provider"></a>HDInsight 리소스 공급자

Azure의 리소스는 리소스 공급자를 통해 제공됩니다. HDInsight 리소스 공급자는 클러스터를 만들고 관리하고 삭제하는 역할을 담당합니다.

## <a name="hdinsight-images"></a>HDInsight 이미지

HDInsight는 이미지를 사용하여 클러스터에 배포할 수 있는 OSS(오픈 소스 소프트웨어) 구성 요소를 결합합니다. 해당 이미지에는 기본 Ubuntu 운영 체제 및 Spark, Hadoop, Kafka, HBase 또는 Hive와 같은 핵심 구성 요소가 포함되어 있습니다.

## <a name="versioning-in-hdinsight"></a>HDInsight의 버전 관리

Microsoft는 새로운 개선 사항 및 기능이 포함되도록 이미지 및 HDInsight 리소스 공급자를 정기적으로 업그레이드합니다.

다음 중 하나 이상에 해당하는 경우 새 HDInsight 버전이 만들어질 수 있습니다.

- HDInsight 리소스 공급자 기능에 대한 주요 변경 또는 업데이트
- OSS 구성 요소의 주요 릴리스
- Ubuntu 운영 체제에 대한 주요 변경

다음 중 하나 이상에 해당하는 경우 새 이미지 버전이 만들어집니다.

- OSS 구성 요소의 주요 또는 기타 릴리스 및 업데이트
- 이미지의 구성 요소에 대한 패치 또는 수정

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache 구성 요소 및 버전](./hdinsight-component-versioning.md)
