---
title: "Visual Studio용 Azure Data Lake 도구 및 Hortonworks 샌드박스 | Microsoft Docs"
description: "Hortonworks Sandbox(로컬 VM에서 실행됨)와 Visual Studio용 Azure Data Lake 도구를 사용하는 방법을 알아봅니다. 이러한 도구로 샌드박스에 대한 Hive 및 Pig 작업을 만들고 실행하며 작업 출력 및 기록을 볼 수 있습니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 599aeb1f38c804c2edf6590140e739c9705ab1ab
ms.lasthandoff: 03/07/2017


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Hortonworks Sandbox와 Visual Studio용 Azure Data Lake 도구 사용

Visual Studio용 Azure Data Lake 도구에는 Azure Data Lake 및 HDInsight를 사용하기 위한 도구 외에도 일반 Hadoop 클러스터를 사용하기 위한 도구가 포함됩니다. 이 문서에서는 로컬 가상 컴퓨터에서 실행되는 Hortonworks Sandbox와 Azure Data Lake 도구를 사용하는 데 필요한 단계를 제공합니다.

Hortonworks Sandbox를 사용하여 개발 환경에서 로컬로 Hadoop를 사용할 수 있습니다. 솔루션을 개발하여 대규모로 배포했으므로 HDInsight 클러스터를 살펴볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 개발 환경의 가상 컴퓨터에서 실행되는 Hortonworks Sandbox입니다. 이 문서는 [Hadoop 에코시스템에서 시작](hdinsight-hadoop-emulator-get-started.md) 문서의 정보를 사용하여 구성된 Oracle VirtualBox에서 실행되는 샌드박스를 사용하여 작성되고 테스트됩니다.

* Visual Studio 2013, 2015 또는 2017의 모든 버전입니다.

* [.NET용 Azure SDK](https://azure.microsoft.com/downloads/) 2.7.1 이상

* [Visual Studio용 Azure Data Lake 도구](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>샌드박스의 암호 구성

Hortonworks Sandbox가 실행되고 있는지 확인하고 [Hadoop 에코시스템에서 시작](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords)의 단계에 따릅니다. 이러한 단계에서 SSH `root` 계정 및 Ambari `admin` 계정의 암호를 구성합니다. Visual Studio에서 샌드박스에 연결할 때 이러한 암호를 사용합니다.

## <a name="connect-the-tools-to-the-sandbox"></a>샌드박스에 도구 연결

1. Visual Studio를 열고 **보기** 및 **서버 탐색기**를 차례로 선택합니다.

2. **서버 탐색기**에서 **HDInsight** 항목을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight Emulator에 연결**을 선택합니다.

    ![HDInsight Emulator에 연결](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. **HDInsight Emulator에 연결** 대화 상자에서 Ambari에 대해 구성한 암호를 입력합니다.

    ![Ambari 암호 입력](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    **다음**을 선택하여 계속합니다.

4. **암호** 필드를 사용하여 `root` 계정에 대해 구성한 암호를 입력합니다. 다른 필드는 기본값으로 둡니다.

    ![루트 암호 입력](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    **다음**을 선택하여 계속합니다.

5. 서비스의 유효성 검사가 완료되기를 기다립니다. 경우에 따라 유효성 검사가 실패하고 구성을 업데이트하라는 메시지가 표시될 수 있습니다. 유효성 검사에 실패할 경우 **업데이트** 단추를 선택하고 서비스의 구성 및 확인이 완료되기를 기다립니다.

    ![오류 및 업데이트 단추](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > 업데이트 프로세스는 Ambari를 사용하여 Visual Studio용 Azure Data Lake 도구에서 예상한 Hortonworks Sandbox 구성을 수정합니다.

    유효성 검사가 완료되면 **마침** 을 선택하여 구성을 완료합니다.

    ![연결 완료](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > 개발 환경의 속도 및 가상 컴퓨터에 할당된 메모리 양에 따라 서비스를 구성하고 유효성을 검사하는 데 몇 분 정도 걸릴 수 있습니다.

이러한 단계를 따르면 HDInsight 섹션의 서버 탐색기에서 "HDInsight 로컬 클러스터" 항목이 생성됩니다.

## <a name="write-a-hive-query"></a>Hive 쿼리 작성

Hive에서는 구조화된 데이터로 작업하기 위한 SQL과 유사한 쿼리 언어(HiveQL)를 제공합니다. 다음 단계를 사용하여 로컬 클러스터에 대해 임시 쿼리를 실행하는 방법을 알아봅니다.

1. **서버 탐색기**에서 이전에 추가한 로컬 클러스터에 대한 항목을 마우스 오른쪽 단추로 클릭한 다음 **Hive 쿼리 작성**을 선택합니다.

    ![Hive 쿼리 작성](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    그러면 로컬 클러스터에는 쿼리를 신속하게 입력하고 제출할 수 있는 새 쿼리 창이 열립니다.

2. 새 쿼리 창에서 다음 명령을 입력합니다.

        select count(*) from sample_08;

    쿼리 창의 위쪽에서 로컬 클러스터에 대한 구성이 선택되었는지 확인한 다음 **제출**을 선택합니다. 다른 값(**Batch** 및 서버 이름)을 기본값으로 그대로 둡니다.

    ![쿼리 창 및 제출 단추](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    **제출** 옆의 드롭다운 메뉴를 사용하여 **고급**을 선택할 수도 있습니다. 고급 옵션을 사용하여 작업을 제출할 때 추가 옵션을 제공할 수 있습니다.

    ![고급 제출](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. 쿼리를 제출하면 작업 상태가 표시됩니다. 작업 상태에는 Hadoop에서 처리되는 작업에 대한 정보가 표시됩니다. **작업 상태** 항목은 작업의 상태를 제공합니다. 상태는 정기적으로 업데이트됩니다. 또는 새로 고침 아이콘을 사용하여 상태를 수동으로 새로 고칠 수 있습니다.

    ![작업 상태](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    **작업 상태**가 **완료됨**으로 변경되면 DAG(방향성 비순환 그래프)가 표시됩니다. 이 다이어그램은 Tez(로컬 클러스터의 Hive에 대한 기본 실행 엔진)에서 결정된 실행 경로를 설명합니다.

    > [!NOTE]
    > 또한 Linux 기반 HDInsight 클러스터를 사용하는 경우 Tez는 기본값입니다. Windows 기반 HDInsight에 대한 기본값이 아닙니다. 여기서 사용하려면 Hive 쿼리 시작 부분에 `set hive.execution.engine = tez;` 줄을 추가해야 합니다.

    출력을 보려면 **작업 출력** 링크를 사용합니다. 이 경우 **823**이며, 이는 sample_08 테이블의 행 수입니다. **작업 로그** 및 **YARN 로그 다운로드** 링크를 사용하여 작업에 대한 진단 정보를 볼 수 있습니다.

4. **Batch** 필드를 **대화형**으로 변경한 다음 **실행**을 선택하면 Hive 작업을 대화형으로 실행할 수도 있습니다.

    ![대화형 쿼리](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    대화형 쿼리는 처리 중에 생성된 출력 로그를 **HiveServer2 출력** 창으로 스트리밍합니다.

    > [!NOTE]
    > 이 정보는 작업 완료 후 **작업 로그** 링크에서 사용할 수 있는 것과 동일한 정보입니다.

    ![HiveServer2 출력](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Hive 프로젝트 만들기

또한 여러 Hive 스크립트를 포함하는 프로젝트를 만들 수 있습니다. 프로젝트는 버전 제어 시스템을 사용하여 함께 보관하거나 유지 관리해야 하는 관련 스크립트가 있는 경우에 유용합니다.

1. Visual Studio에서 **파일**, **새로 만들기** 및 __프로젝트__를 차례로 선택합니다.

2. 프로젝트 목록에서 **템플릿**, **Azure Data Lake**를 차례로 확장한 다음 **HIVE(HDInsight)**를 선택합니다. 템플릿 목록에서 **Hive 샘플**을 선택합니다. 이름과 위치를 입력한 다음 **확인**을 선택합니다.

    ![HIVE(HDInsight) 템플릿](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive 샘플** 프로젝트에는 **WebLogAnalysis.hql** 및 **SensorDataAnalysis.hql**이라는 두 개의 스크립트가 포함되어 있습니다. 창 위쪽의 **제출** 단추를 사용하여 이러한 스크립트를 제출할 수 있습니다.

## <a name="create-a-pig-project"></a>Pig 프로젝트 만들기

Hive에서는 구조화된 데이터로 작업하기 위한 SQL과 유사한 쿼리 언어(HiveQL)를 제공하지만 Pig는 데이터에 대한 변환을 수행하는 방식으로 작동합니다. Pig는 변환의 파이프라인을 개발할 수 있는 언어(Pig Latin)를 제공합니다. 다음 단계를 사용하여 로컬 클러스터에서 Pig를 사용합니다.

1. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다. 프로젝트 목록에서 **템플릿**, **Azure Data Lake**를 차례로 확장한 다음 **Pig(HDInsight)**를 선택합니다. 템플릿 목록에서 **Pig 응용 프로그램**을 선택합니다. 이름과 위치를 입력한 다음 **확인**을 선택합니다.

    ![Pig(HDInsight) 프로젝트](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. 이 프로젝트로 만든 **script.pig** 파일의 콘텐츠로 다음 텍스트를 입력합니다.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig에서 Hive와 다른 언어를 사용하는 동안 **제출** 단추를 통해 두 언어 간에 작업을 실행하는 방법은 일치합니다. **제출** 옆에 있는 드롭다운을 선택하면 Pig의 [고급 제출] 대화 상자가 표시됩니다.

    ![Pig 고급 제출](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. 또한 작업 상태 및 출력은 Hive 쿼리와 동일하게 표시됩니다.

    ![완료된 Pig 작업 이미지](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>작업 보기

또한 Azure Data Lake 도구를 사용하면 Hadoop에서 실행된 작업에 대한 정보를 쉽게 볼 수 있습니다. 다음 단계를 사용하여 로컬 클러스터에서 실행된 작업을 확인합니다.

1. **서버 탐색기**에서 로컬 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **작업 보기**를 선택합니다. 클러스터에 제출된 작업 목록이 표시됩니다.

    ![작업 보기](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. 작업 목록에서 작업 세부 정보를 볼 작업을 선택합니다.

    ![작업 선택](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    표시되는 정보는 Hive 또는 Pig 쿼리를 실행한 후에 표시되는 정보와 비슷합니다. 출력 및 로그 정보를 보는 링크를 사용하여 완료합니다.

3. 또한 여기에서 작업을 수정하고 다시 제출할 수 있습니다.

## <a name="view-hive-databases"></a>Hive 데이터베이스 보기

1. **서버 탐색기**에서 **HDInsight 로컬 클러스터** 항목, **Hive 데이터베이스**를 차례로 확장합니다. 로컬 클러스터에 있는 **Default** 및 **xademo** 데이터베이스가 표시됩니다. 데이터베이스 확장하면 데이터베이스 내의 테이블이 표시됩니다.

    ![확장된 데이터베이스](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. 테이블을 확장하면 해당 테이블에 대한 열을 표시합니다. 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 100개 행 보기** 를 선택하여 신속하게 데이터를 볼 수 있습니다.

    ![Hive 데이터베이스 보기](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>데이터베이스 및 테이블 속성

데이터베이스 또는 테이블에 대한 **속성**을 보도록 선택할 수 있습니다. **속성**을 선택하면 [속성] 창에서 선택한 항목에 대한 세부 정보가 표시됩니다.

![속성](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>테이블 만들기

새 테이블을 만들려면 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **테이블 만들기**를 선택합니다.

![테이블 만들기](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

그런 다음 형식을 사용하여 테이블을 만들 수 있습니다. 이 페이지의 맨 아래에서 테이블을 만드는 데 사용할 원시 HiveQL를 볼 수 있습니다.

![테이블 형식 만들기](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>다음 단계

* [Hortonworks Sandbox의 방법 알아보기](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 자습서 - HDP 시작](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

