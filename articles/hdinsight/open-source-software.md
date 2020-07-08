---
title: Azure HDInsight의 오픈 소스 소프트웨어 지원
description: Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f1e1518cce060374f14f1db7d5f4e594e3de8f85
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086265"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Azure HDInsight의 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Apache Hadoop을 기반으로 형성된 오픈 소스 기술의 환경을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure Support FAQ](https://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션을 참조하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

## <a name="components"></a>구성 요소

HDInsight 서비스에서는 두 가지 유형의 오픈 소스 구성 요소를 사용할 수 있습니다.

### <a name="built-in-components"></a>기본 제공 구성 요소

이러한 구성 요소는 HDInsight 클러스터에 미리 설치되어 있으며 클러스터의 핵심 기능을 제공합니다. 이 범주에 속하는 구성 요소는 다음과 같습니다.

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager
* [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) Hive 쿼리 언어
* [Apache Mahout](https://mahout.apache.org/)

클러스터 구성 요소의 전체 목록은 [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전은?](hdinsight-component-versioning.md)에서 사용할 수 있습니다.

### <a name="custom-components"></a>사용자 지정 구성 요소

클러스터 사용자는 커뮤니티에서 사용할 수 있거나 직접 만든 구성 요소를 워크로드에 설치하거나 사용할 수 있습니다.

> [!WARNING]  
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제를 해결하는 데 도움이 되는 상업적으로 합리적인 지원을 받습니다. Microsoft 지원에서 이 문제를 해결할 수 있습니다. 또는 깊은 전문 지식이 있는 오픈 소스 기술에 사용할 수 있는 채널에 참여하도록 요청할 수도 있습니다. 다양한 커뮤니티 사이트를 사용할 수 있습니다. 예를 들어 [Microsoft HDInsight 관련 질문과 답변 페이지](https://docs.microsoft.com/answers/topics/azure-hdinsight.html) 및 [Stack Overflow](https://stackoverflow.com)가 있습니다.
>
> Apache 프로젝트에는 [Apache 웹 사이트](https://apache.org)의 프로젝트 사이트도 있습니다. 예를 들어 [Hadoop](https://hadoop.apache.org/)이 있습니다.

## <a name="component-usage"></a>구성 요소 사용법

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 클러스터에서 구성 요소를 사용하거나 설치하는 방법과 상관없이 동일한 수준의 지원이 적용됩니다. 다음 표에는 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용하는 가장 일반적인 방법이 나와 있습니다.

|사용 |Description |
|---|---|
|작업 제출|사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 다른 유형의 작업을 클러스터에 제출할 수 있습니다.|
|클러스터 사용자 지정|클러스터를 만드는 동안 클러스터 노드에 설치되는 추가 설정 및 사용자 지정 구성 요소를 지정할 수 있습니다.|
|샘플|인기 있는 사용자 지정 구성 요소의 경우 Microsoft 및 다른 사용자는 이러한 구성 요소를 HDInsight 클러스터에서 사용하는 방법을 보여 주는 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.|

## <a name="next-steps"></a>다음 단계

* [스크립트 동작을 사용하여 Azure HDInsight 클러스터 사용자 지정](./hdinsight-hadoop-customize-cluster-linux.md)
* [HDInsight용 스크립트 동작 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
* [스크립트 작업을 사용하여 Azure HDInsight에서 Python 환경을 안전하게 관리](./spark/apache-spark-python-package-installation.md)
