---
title: Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그
description: YARN UI, Spark UI 및 Spark 기록 서버를 사용하여 Azure HDInsight의 Spark 클러스터에서 실행 중인 작업을 추적하고 디버깅합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 817b8976f5d014d990945816c2df1c7e8ed729fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084888"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그

이 문서에서는 HDInsight 클러스터에서 실행 되는 Apache Spark 작업을 추적 하 고 디버그 하는 방법에 대해 알아봅니다. Apache Hadoop YARN UI, Spark UI 및 Spark 기록 서버를 사용 하 여 디버그 합니다. Spark 클러스터에서 사용할 수 있는 Notebook을 통해 Spark 작업(**Machine Learning: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석**)을 시작합니다. 다음 단계를 사용 하 여 다른 방법 (예: **spark-제출)** 을 사용 하 여 제출한 응용 프로그램을 추적할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

* Notebook, 즉 **[기계 학습: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석](apache-spark-machine-learning-mllib-ipython.md)** 을 실행하기 시작했어야 합니다. 이 Notebook을 실행하는 방법은 링크를 따라갑니다.  

## <a name="track-an-application-in-the-yarn-ui"></a>YARN UI에서 애플리케이션 추적

1. YARN UI를 시작합니다. **클러스터 대시보드**아래에서 **Yarn** 을 선택 합니다.

    ![YARN UI를 시작 Azure Portal](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > 또는 Ambari UI에서 YARN UI를 시작할 수도 있습니다. Ambari UI를 시작 하려면 **클러스터 대시보드**에서 **Ambari 홈** 을 선택 합니다. Ambari ui에서 **YARN**  >  **빠른 링크** 로 이동 하 여 활성 리소스 관리자 > **리소스 관리자 UI**> 합니다.

2. Jupyter 노트북을 사용 하 여 Spark 작업을 시작 했으므로 응용 프로그램의 이름은 **remotesparkmagics** (노트북에서 시작 된 모든 응용 프로그램의 이름)입니다. 응용 프로그램 이름에 대 한 응용 프로그램 ID를 선택 하 여 작업에 대 한 자세한 정보를 가져옵니다. 이 작업을 수행 하면 응용 프로그램 보기가 시작 됩니다.

    ![Spark 기록 서버 Spark 응용 프로그램 ID 찾기](./media/apache-spark-job-debugging/find-application-id1.png)

    Jupyter Notebook에서 시작된 이러한 애플리케이션의 경우 Notebook을 끝낼 때까지 상태는 항상 **실행 중** 입니다.

3. 애플리케이션 보기에서 애플리케이션 및 로그 (stdout/stderr)와 연결된 컨테이너에 대해 알아보기 위해 더 자세히 살펴볼 수 있습니다. 아래와 같이 **추적 URL**에 해당하는 연결을 클릭하여 Spark UI를 시작할 수도 있습니다.

    ![Spark 기록 서버 다운로드 컨테이너 로그](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spark UI에서 애플리케이션 추적

Spark UI에서 이전에 시작한 애플리케이션에 의해 생성된 Spark 작업을 더 자세히 살펴볼 수 있습니다.

1. Spark UI를 시작 하려면 응용 프로그램 보기에서 위의 화면 캡처에 표시 된 것 처럼 **추적 URL**에 대 한 링크를 선택 합니다. Jupyter Notebook에서 실행 중인 애플리케이션에 의해 시작되는 모든 Spark 작업을 확인할 수 있습니다.

    ![Spark 기록 서버 작업 탭](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. **실행자** 탭을 선택 하 여 각 실행자에 대 한 처리 및 저장소 정보를 확인 합니다. **스레드 덤프** 링크를 선택 하 여 호출 스택을 검색할 수도 있습니다.

    ![Spark 기록 서버 실행자 탭](./media/apache-spark-job-debugging/view-spark-executors.png)

3. **단계** 탭을 선택 하 여 응용 프로그램과 관련 된 단계를 확인 합니다.

    ![Spark 기록 서버 단계 탭](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark 단계 보기")

    각 단계에서는 아래와 같이 실행 통계를 볼 수 있는 여러 작업이 있습니다.

    ![Spark 기록 서버 단계 탭 세부 정보](./media/apache-spark-job-debugging/view-spark-stages-details.png "Spark 단계 세부 정보 보기")

4. 단계 세부 정보 페이지에서 DAG 시각화를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **DAG 시각화** 링크를 확장합니다.

    ![Spark 단계 DAG 시각화 보기](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG 또는 Direct Aclyic Graph는 애플리케이션에서 다양한 단계를 나타냅니다. 그래프의 파란색 상자는 각각 애플리케이션에서 호출한 Spark 작업을 나타냅니다.

5. 단계 세부 정보 페이지에서 애플리케이션 타임라인 보기를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **이벤트 타임라인** 링크를 확장합니다.

    ![Spark 단계 이벤트 타임라인 보기](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    이 이미지는 타임 라인 형식의 Spark 이벤트를 표시 합니다. 타임라인 보기는 작업, 작업 내, 단계 내 등 세 가지 수준에서 사용할 수 있습니다. 위의 이미지는 지정된 단계에 대한 타임라인 보기를 캡처합니다.

   > [!TIP]  
   > **확대/축소 사용** 확인란을 선택한 경우 타임라인 보기를 좌우로 스크롤할 수 있습니다.

6. Spark UI에서 다른 탭도 Spark 인스턴스에 대한 유용한 정보를 제공합니다.

   * 저장소 탭-응용 프로그램이 RDD를 만드는 경우 저장소 탭에서 정보를 찾을 수 있습니다.
   * 환경 탭-이 탭은 다음과 같은 Spark 인스턴스에 대 한 유용한 정보를 제공 합니다.
     * Scala 버전
     * 클러스터와 연결된 이벤트 로그 디렉터리
     * 애플리케이션에 대한 실행자 코어 수

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Spark 기록 서버를 사용하여 완료된 작업에 대한 정보 찾기

작업이 완료되면 작업에 대한 정보는 Spark 기록 서버에 유지됩니다.

1. Spark 기록 서버를 시작 하려면 **개요** 페이지의 **클러스터 대시보드**아래에서 **spark 기록 서버** 를 선택 합니다.

    ![Spark 기록 서버를 시작 Azure Portal](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark 기록 Server1 시작")

   > [!TIP]  
   > 또는 Ambari UI에서 Spark 기록 서버를 시작할 수도 있습니다. Ambari UI를 시작 하려면 개요 블레이드에서 **클러스터 대시보드**아래의 **Ambari 홈** 을 선택 합니다. Ambari UI에서 **Spark2**  >  **Quick Links**  >  **Spark2 History Server UI**로 이동 합니다.

2. 완료된 애플리케이션이 모두 표시됩니다. 응용 프로그램 ID를 선택 하 여 자세한 정보를 확인할 수 있습니다.

    ![Spark 기록 서버 완료 응용 프로그램](./media/apache-spark-job-debugging/view-completed-applications.png "Spark 기록 시작")

## <a name="see-also"></a>참고 항목

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [확장된 Spark 기록 서버를 사용하여 Apache Spark 작업 디버그](apache-azure-spark-history-server.md)
* [SSH를 통해 Azure Toolkit for IntelliJ를 사용 하 여 Apache Spark 응용 프로그램 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
