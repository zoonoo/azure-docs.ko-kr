---
title: 버전 관리 소개-Azure HDInsight
description: Azure HDInsight에서 버전 관리가 작동 하는 방식에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493869"
---
# <a name="how-versioning-works-in-hdinsight"></a>HDInsight에서 버전 관리 작동 방법

HDInsight 서비스에는 두 가지 주요 구성 요소인 리소스 공급자와 클러스터에 배포 되는 Apache Hadoop 구성 요소가 있습니다. 

## <a name="hdinsight-resource-provider"></a>HDInsight 리소스 공급자

리소스 공급자는 Azure의 리소스를 사용할 수 있습니다. HDInsight 리소스 공급자는 클러스터를 만들고 관리 하 고 삭제 하는 일을 담당 합니다.

## <a name="hdinsight-images"></a>HDInsight 이미지

HDInsight는 이미지를 사용 하 여 클러스터에 배포할 수 있는 오픈 소스 소프트웨어 (OSS) 구성 요소를 함께 배치 합니다. 이러한 이미지에는 기본 Ubuntu 운영 체제 및 Spark, Hadoop, Kafka, HBase 또는 Hive와 같은 핵심 구성 요소가 포함 되어 있습니다.

## <a name="versioning-in-hdinsight"></a>HDInsight의 버전 관리

Microsoft는 새로운 향상 된 기능 및 기능을 포함 하도록 이미지 및 HDInsight 리소스 공급자를 정기적으로 업그레이드 합니다.

다음 중 하나 이상에 해당 하는 경우 새 HDInsight 버전이 만들어질 수 있습니다.

- HDInsight 리소스 공급자 기능에 대 한 주요 변경 내용 또는 업데이트
- OSS 구성 요소의 주요 릴리스
- Ubuntu 운영 체제의 주요 변경 내용

다음 중 하나 이상에 해당 하는 경우 새 이미지 버전이 만들어집니다.

- OSS 구성 요소 주 또는 부 릴리스 및 업데이트
- 이미지의 구성 요소에 대 한 패치 또는 픽스

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache 구성 요소 및 버전](./hdinsight-component-versioning.md)
