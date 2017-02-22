---
title: "HDInsight에서 Windows 기반 Hadoop 클러스터 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 HDInsight에 Windows 기반 Hadoop 클러스터를 만드는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight에 Windows 기반 Hadoop 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure Portal을 사용하여 HDInsight에 Windows 기반 Hadoop 클러스터를 만드는 방법을 알아봅니다. 

이 문서의 정보는 Windows 기반 HDInsight 클러스터에만 적용됩니다. Linux 기반 클러스터 생성에 대한 자세한 내용은 [Azure Portal을 사용하여 HDInsight에 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

## <a name="prerequisites"></a>필수 조건:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

### <a name="access-control-requirements"></a>액세스 제어 요구 사항
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>클러스터 만들기
**HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **인텔리전스 + 분석** 및 **HDInsight**를 차례로 클릭합니다.
3. 다음 값을 입력하거나 선택합니다.
   
   * **클러스터 이름**: 클러스터 이름을 지정합니다.
   * **구독**: 이 클러스터에 사용되는 Azure 구독을 선택합니다.
   * **클러스터 구성**:

     * **클러스터 유형**: 이 자습서에서는 **hadoop**을 선택합니다.
     * **운영 체제**: 이 자습서에서는 **Windows**를 선택합니다.
     * **버전**: **Hadoop 2.7.0(HDI 3.3)**을 선택합니다.  Windows 기반 Hadoop 클러스터에 대한 최신 버전입니다.
     * **클러스터 계층**: 이 자습서에서는 **표준**을 선택합니다.

   * **자격 증명**:

     * **클러스터 로그인 사용자 이름**: 기본 이름은 **admin**입니다. 
     * **클러스터 로그인 암호**: 암호를 입력합니다.
     * **원격 데스크톱 사용**: 이 자습서에서는 원격 데스크톱이 필요하지 않습니다.

   * **데이터 원본**:

     * **선택 방법**: **모든 구독에서**를 선택합니다.
     * **저장소 계정 선택**: **새로 만들기**를 클릭하고 기본 저장소 계정의 이름을 입력합니다.
     * **기본 컨테이너**: 기본적으로 기본 컨테이너는 클러스터와 같은 이름을 사용합니다.
     * **위치**: 본인에게 가까운 위치를 선택합니다.  이 위치는 클러스터와 기본 저장소 계정 둘 다에서 사용됩니다.
   * **클러스터 크기**:

     * **작업자 노드 수**: 이 자습서에는 노드가 1개만 필요합니다.
   * **고급 구성**: 이 자습서에서는 이 부분을 건너뛰어도 됩니다.
   * **리소스 그룹**: **새로 만들기**를 선택하고 이름을 입력합니다.

4. **대시보드에 고정**을 선택하고 **만들기**를 클릭합니다. **시작 보드에 고정**을 선택하면 포털의 시작 보드에 클러스터 타일이 추가됩니다. 클러스터를 만드는 데는 15-20분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.
5. 생성이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.

## <a name="customize-clusters"></a>클러스터 사용자 지정
* [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.
* [스크립트 작업을 사용하여 Windows 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight 클러스터를 시작하는 방법을 알아봅니다.
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md) - 프로그래밍 방식으로 작업을 HDInsight에 제출하는 방법을 알아봅니다.
* [Azure Portal을 사용하여 HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


