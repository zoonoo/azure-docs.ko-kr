---
title: IntelliJ Azure 도구 키트를 사용 하 여 Spark 작업 디버그 (미리 보기)-HDInsight
description: 응용 프로그램 디버깅을 위한 Azure Toolkit for IntelliJ의 HDInsight 도구 사용 지침
keywords: IntelliJ 원격으로 디버그, IntelliJ 원격 디버깅, SSH, IntelliJ, HDInsight, IntelliJ 디버그, 디버깅
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: ebed7d87ba538b2f886155527bb89c1ffd2bcf58
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545704"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Azure Toolkit for IntelliJ를 사용 하 여 spark 작업 디버깅 실패 (미리 보기)

이 문서에서는 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) 의 HDInsight 도구를 사용 하 여 **Spark 오류 디버그** 응용 프로그램을 실행 하는 방법에 대 한 단계별 지침을 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Oracle Java Development 키트](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). 이 자습서에서는 Java 버전 8.0.202를 사용합니다.
  
* IntelliJ IDEA. 이 문서에서는 [INTELLIJ 아이디어 커뮤니티 버전 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)을 사용 합니다.
  
* Azure Toolkit for IntelliJ. [Azure Toolkit for IntelliJ 설치](/azure/developer/java/toolkit-for-intellij/installation)를 참조하세요.

* HDInsight 클러스터에 연결 합니다. [HDInsight 클러스터에 연결을](apache-spark-intellij-tool-plugin.md)참조 하세요.

* Microsoft Azure Storage 탐색기. [다운로드 Microsoft Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 참조 하세요.

## <a name="create-a-project-with-debugging-template"></a>디버깅 템플릿을 사용 하 여 프로젝트 만들기

이 문서에서 오류 디버그, 실패 작업 디버깅 샘플 파일을 계속 하려면 spark 2.3.2 프로젝트를 만듭니다.

1. IntelliJ IDEA를 엽니다. **새 프로젝트** 창을 엽니다.

   a. 왼쪽 창에서 **Azure Spark/HDInsight** 를 선택합니다.

   b. 주 창에서 **실패 태스크 디버깅 샘플 (미리 보기) (Scala)을 사용 하 여 Spark 프로젝트** 를 선택 합니다.

     ![Intellij 디버그 프로젝트 만들기](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. **다음** 을 선택합니다.

2. **새 프로젝트** 창에서 다음 단계를 수행합니다.

   ![Intellij 새 프로젝트 Spark 버전 선택](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 프로젝트 이름과 프로젝트 위치를 입력합니다.

   b. **프로젝트 SDK** 드롭다운 목록에서 **Spark 2.3.2** 클러스터용 **Java 1.8** 를 선택 합니다.

   c. **Spark 버전** 드롭다운 목록에서 **Spark 2.3.2 (Scala 2.11.8)** 를 선택 합니다.

   d. **마침** 을 선택합니다.

3. **Src**  >  **main**  >  **scala** 을 선택 하 여 프로젝트에서 코드를 엽니다. 이 예에서는 **AgeMean_Div ()** 스크립트를 사용 합니다.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 Spark Scala/Java 응용 프로그램 실행

Spark Scala/Java 응용 프로그램을 만든 후 다음 단계를 수행 하 여 Spark 클러스터에서 응용 프로그램을 실행 합니다.

1. **구성 추가** 를 클릭 하 여 **실행/디버그 구성** 창을 엽니다.

   ![HDI Intellij 구성 추가](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. **실행/디버깅 구성** 대화 상자에서 더하기 기호( **+** )를 선택합니다. 그런 다음 **HDInsight에서 Apache Spark** 옵션을 선택 합니다.

   ![Intellij 새 구성 추가](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. **클러스터에서 원격으로 실행** 탭으로 전환 합니다. **이름** , **Spark 클러스터** 및 **주 클래스 이름** 에 대 한 정보를 입력 합니다. 이 도구는 **실행기** 를 사용하여 디버그를 지원합니다. **NumExectors** 기본값은 5이 고 3 보다 큰 값을 설정 하지 않는 것이 좋습니다. 실행 시간을 줄이기 위해 **작업 구성** 에 **yarn** 를 추가 하 고 값을 1로 설정할 수 있습니다. **확인** 단추를 클릭 하 여 구성을 저장 합니다.

   ![Intellij 디버그 구성 새로 실행](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 이제 사용자가 입력한 이름으로 구성이 저장됩니다. 구성 세부 정보를 보려면 구성 이름을 선택합니다. 변경하려면 **구성 편집** 을 선택합니다.

5. 구성 설정을 완료 한 후에는 원격 클러스터에 대해 프로젝트를 실행할 수 있습니다.

   ![Intellij Debug 원격 Spark 작업 원격 실행 단추](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 출력 창에서 응용 프로그램 ID를 확인할 수 있습니다.

   ![Intellij Debug 원격 Spark 작업 원격 실행 결과](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>다운로드 실패 작업 프로필

작업 제출에 실패 하는 경우 추가 디버깅을 위해 실패 한 작업 프로필을 로컬 컴퓨터에 다운로드할 수 있습니다.

1. **Microsoft Azure Storage 탐색기** 를 열고 실패 한 작업에 대 한 클러스터의 HDInsight 계정을 찾은 다음 해당 위치: **\hdp\spark2-events \\ \\ \<application ID>** 에서 로컬 폴더로 실패 한 작업 리소스를 다운로드 합니다. **작업** 창에 다운로드 진행률이 표시 됩니다.

   ![Azure Storage 탐색기 다운로드 실패](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure Storage 탐색기 다운로드 성공](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>로컬 디버깅 환경 구성 및 오류 발생 시 디버그

1. 원본 프로젝트를 열거나 새 프로젝트를 만든 다음 원래 소스 코드와 연결 합니다. 현재 오류 디버깅에는 spark 2.3.2 버전만 지원 됩니다.

1. IntelliJ 아이디어에서 **Spark 실패 디버그** 구성 파일을 만들고, **Spark 작업 실패 컨텍스트 위치** 필드에 대해 이전에 다운로드 한 실패 한 작업 리소스에서 ftd 파일을 선택 합니다.

   ![만들고 실패 구성](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. 도구 모음에서 로컬 실행 단추를 클릭 하면 실행 창에 오류가 표시 됩니다.

   ![실행-오류-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![실행-오류-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. 로그에서 중단점을 설정 하 고 로컬 디버그 단추를 클릭 하 여 IntelliJ의 일반 Scala/Java 프로젝트와 마찬가지로 로컬 디버깅을 수행 합니다.

1. 디버깅 후 프로젝트가 성공적으로 완료 되 면 HDInsight 클러스터의 spark에 실패 한 작업을 다시 제출할 수 있습니다.

## <a name="next-steps"></a><a name="seealso"></a>다음 단계

* [개요: 응용 프로그램 디버그 Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>데모

* Scala 프로젝트 만들기(비디오): [Apache Spark Scala 애플리케이션 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터에서 원격으로 Apache Spark 애플리케이션 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오

* [BI를 사용 하 여 Apache Spark: BI 도구와 함께 HDInsight에서 Spark를 사용 하 여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](./apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](./apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)