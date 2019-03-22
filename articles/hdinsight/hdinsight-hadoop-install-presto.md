---
title: Azure HDInsight Linux 클러스터에 Presto 설치
description: 스크립트 동작을 사용하여 Linux 기반 HDInsight Hadoop 클러스터에 Presto 및 Airpal을 설치하는 방법을 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 435c041bb5fb0a398f92914f943166108cc20080
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258346"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Hadoop 기반 HDInsight 클러스터에 Presto 설치 및 사용

이 문서에서는 스크립트 동작을 사용 하 여 Azure HDInsight Hadoop 기반 클러스터에 Presto를 설치 하는 방법에 설명 합니다. 또한 기존 Presto HDInsight 클러스터에 Airpal을 설치하는 방법에 대해 알아봅니다.

HDInsight는 또한 Apache Hadoop 클러스터에 대한 Starburst Presto 애플리케이션을 제공합니다. 자세한 내용은 [Azure HDInsight에 타사 Apache Hadoop 애플리케이션 설치](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)를 참조하세요.

> [!IMPORTANT]  
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 3.5 Hadoop 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전](hdinsight-component-versioning.md)을 참조하세요.

## <a name="what-is-presto"></a>Presto란?
[Presto](https://prestodb.io/overview.html)는 빅 데이터에 대한 빠른 분산형 SQL 쿼리 엔진입니다. Presto는 페타바이트의 데이터를 대화형으로 쿼리하는 데 적합합니다. Presto의 구성 요소 및 작동 방법에 대한 자세한 내용은 [Presto 개념](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)을 참조하세요.

> [!WARNING]  
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도움을 줍니다.
> 
> Presto와 같은 사용자 지정 구성 요소는 문제를 해결하는 데 도움이 되는 상업적으로 합리적인 지원을 받습니다. 이 지원을 통해 문제를 해결할 수 있습니다. 또는 깊은 전문 지식이 있는 오픈 소스 기술에 사용할 수 있는 채널에 참여하도록 요청할 수도 있습니다. 사용할 수 있는 커뮤니티 사이트가 많이 있습니다. 예를 들어 [HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) 및 [Stack Overflow](https://stackoverflow.com)에 대한 MSDN 포럼이 있습니다. 
>
> Apache 프로젝트에는 [Apache 웹 사이트](https://apache.org)의 프로젝트 사이트도 있습니다. 예를 들어 [Hadoop](https://hadoop.apache.org/)이 있습니다.


## <a name="install-presto-by-using-script-actions"></a>스크립트 동작을 사용하여 Presto 설치

이 섹션에서는 Azure Portal을 사용하여 새 클러스터를 만들 때 샘플 스크립트를 사용하는 방법에 대해 설명합니다. 

1. [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)의 단계를 수행하여 클러스터 프로비전을 시작합니다. **사용자 지정** 클러스터 만들기 흐름을 사용하여 클러스터를 만들어야 합니다. 클러스터에서 충족해야 하는 요구 사항은 다음과 같습니다.

   * HDInsight 버전 3.6을 사용하는 Hadoop 클러스터여야 합니다.

   * 데이터 저장소로 Azure Storage를 사용해야 합니다. 스토리지 옵션으로 Azure Data Lake Storage를 사용하는 클러스터에서는 Presto를 아직 사용할 수 없습니다.

     ![HDInsight, 사용자 지정(크기, 설정, 앱)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. **고급 설정** 영역에서 **스크립트 동작**을 선택합니다. 다음 정보를 제공합니다. 또한 스크립트 유형에 대해 **Presto 설치** 옵션을 선택할 수도 있습니다.
   
   * **이름**. 스크립트 동작의 이름을 입력합니다.
   * **Bash 스크립트 URI**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **헤드**. 이 옵션을 선택합니다.
   * **작업자**. 이 옵션을 선택합니다.
   * **Zookeeper**. 이 확인란은 비워 둡니다.
   * **매개 변수**. 이 필드는 공백으로 둡니다.


3. **스크립트 동작** 영역의 아래쪽에서 **선택** 단추를 선택하여 구성을 저장합니다. 마지막으로 **고급 설정** 영역의 아래쪽에서 **선택** 단추를 선택하여 구성 정보를 저장합니다.

4. [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

    > [!NOTE]  
    > Azure PowerShell, Azure 클래식 CLI, HDInsight .NET SDK 또는 Azure Resource Manager 템플릿을 사용하여 스크립트 동작을 적용할 수도 있습니다. 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>HDInsight와 함께 Presto 사용

HDInsight 클러스터에서 Presto를 사용하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
     

2. 다음 명령을 실행하여 Presto 셸을 시작합니다.
   
    `presto --schema default`

3. 모든 HDInsight 클러스터에서 기본적으로 사용할 수 있는 **hivesampletable** 샘플 테이블에 대한 쿼리를 실행합니다.
   
    `select count (*) from hivesampletable;`
   
    기본적으로 Presto에 대한 [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) 및 [TPCH](https://prestodb.io/docs/current/connector/tpch.html) 커넥터는 이미 구성되어 있습니다. Hive 커넥터는 기본 Hive 설치를 사용하도록 구성됩니다. 그러면 Hive의 모든 테이블이 Presto에 자동으로 표시됩니다.

    자세한 내용은 [Presto 설명서](https://prestodb.io/docs/current/index.html)를 참조하세요.

## <a name="use-airpal-with-presto"></a>Presto와 함께 Airpal 사용

[Airpal](https://github.com/airbnb/airpal#airpal)은 Presto에 대한 오픈 소스 웹 기반 쿼리 인터페이스입니다. Airpal에 대한 자세한 내용은 [Airpal 설명서](https://github.com/airbnb/airpal#airpal)를 참조하세요.

Airpal을 에지 노드에 설치하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 Presto가 설치된 HDInsight 클러스터의 헤드 노드에 연결합니다.
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 연결되면 다음 명령을 실행합니다.

    `sudo slider registry  --name presto1 --getexp presto` 
   
    다음 JSON과 비슷한 출력이 표시됩니다.

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 출력에서 **값** 속성에 대한 값을 적어 둡니다. 이 값은 Airpal을 클러스터 에지 노드에 설치하는 동안 필요합니다. 이전 출력에서 필요한 값은 **10.0.0.12:9090**입니다.

4. [이 템플릿](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)을 사용하여 HDInsight 클러스터 에지 노드를 만듭니다. 다음 스크린샷에 표시된 값을 제공합니다.

    ![사용자 지정 배포](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. **구매**를 선택합니다.

6. 변경 내용이 클러스터 구성에 적용되면 [Azure Portal](https://portal.azure.com)에서 다음 단계를 수행하여 Airpal 웹 인터페이스에 액세스합니다.

    1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    1. **ANALYTICS** 아래에서 **HDInsight 클러스터**를 선택합니다.

    1. 목록에서 클러스터를 선택합니다. 그러면 기본 보기가 열립니다.

    1. 기본 보기의 **설정** 아래에서 **애플리케이션**을 선택합니다.

        ![HDInsight, 애플리케이션](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. **설치된 앱** 페이지에서 **airpal**에 대한 테이블 항목을 찾습니다. **포털**을 선택합니다.

        ![설치된 앱](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. 대화 상자가 표시되면 Hadoop 기반 HDInsight 클러스터를 만들 때 지정한 관리자 자격 증명을 입력합니다.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>HDInsight 클러스터에서 Presto 설치 사용자 지정

설치를 사용자 지정하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 Presto가 설치된 HDInsight 클러스터의 헤드 노드에 연결합니다.
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 파일에서 구성을 변경합니다. Presto 구성에 대한 자세한 내용은 [YARN 기반 클러스터에 대한 Presto 구성 옵션](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)을 참조하세요.

3. 현재 실행 중인 Presto의 인스턴스를 중지하고 종료합니다.

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. 사용자 지정을 사용하여 Presto의 새 인스턴스를 시작합니다.

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. 새 인스턴스가 준비될 때까지 기다립니다. Presto 코디네이터 주소를 확인합니다.

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Presto를 실행하는 HDInsight 클러스터에 대한 벤치마크 데이터를 생성합니다.

TPC-DS는 빅 데이터 시스템을 포함하여 다양한 의사 결정 지원 시스템의 성능을 측정하기 위한 업계 표준입니다. Presto를 사용하여 데이터를 생성하고 사용자 고유의 HDInsight 벤치마크 데이터와 비교하는 방법을 평가합니다. 자세한 내용은 [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계
* [HDInsight Hadoop 클러스터에 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md). Hue는 Apache Pig 및 Hive 작업을 쉽게 만들고 실행하고 저장할 수 있도록 하는 웹 UI입니다.

* [HDInsight Hadoop 클러스터에 Apache Giraph 설치 및 Giraph를 사용하여 대규모 그래프 처리](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 Hadoop 기반 HDInsight 클러스터에 Giraph를 설치합니다. Giraph를 사용하면 Hadoop을 사용하여 그래프 처리를 수행할 수 있습니다. Azure HDInsight에서도 사용할 수 있습니다.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
