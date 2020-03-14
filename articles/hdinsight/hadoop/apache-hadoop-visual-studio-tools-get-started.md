---
title: Apache Hadoop & Visual Studio Data Lake 도구-Azure HDInsight
description: Data Lake Tools for Visual Studio를 설치하고 사용하여 Azure HDInsight에서 Apache Hadoop 클러스터에 연결한 다음, Hive 쿼리를 실행하는 방법을 알아봅니다.
keywords: Hadoop 도구, Hive 쿼리, Visual Studio, Visual Studio Hadoop Hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272787"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행

Microsoft Azure Data Lake 및 Stream Analytics Tools for Visual Studio (Data Lake 도구 라고도 함)를 사용 하 여 [Azure HDInsight의 Apache Hadoop 클러스터](apache-hadoop-introduction.md) 에 연결 하 고 Hive 쿼리를 제출 하는 방법에 대해 알아봅니다.  

HDInsight를 사용 하는 방법에 대 한 자세한 내용은 [hdinsight 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조 하세요.  

Apache Storm 클러스터에 연결 하는 방법에 대 한 자세한 [내용은 C# Visual Studio 용 Data Lake 도구를 사용 하 여 Apache Storm에 대 한 토폴로지 개발](../storm/apache-storm-develop-csharp-visual-studio-topology.md)을 참조 하세요.

Data Lake Tools for Visual Studio를 사용하여 Azure Data Lake Analytics 및 HDInsight에 액세스할 수 있습니다. Data Lake Tools에 대한 자세한 내용은 [Data Lake Tools for Visual Studio를 사용하여 U-SQL 스크립트 개발](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료 하 고 Visual Studio 용 Data Lake 도구를 사용 하려면 다음 항목이 필요 합니다.

* Azure HDInsight 클러스터를 만듭니다. HDInsight 클러스터를 만들려면 [Azure HDInsight에서 Apache Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요. 대화형 Apache Hive 쿼리를 실행하려면 [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터가 필요합니다.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) 은 무료입니다. 여기에 표시 된 지침은 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)에 대 한 것입니다.

## <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구 설치  

적절 한 지침에 따라 Visual Studio 버전에 대 한 Data Lake 도구를 설치 합니다.

- Visual Studio 2017 또는 Visual Studio 2019의 경우:

    Visual Studio를 설치 하는 동안 **Azure 개발** 워크 로드 또는 **데이터 저장소 및 처리** 워크 로드를 포함 해야 합니다.  

    기존 Visual Studio 설치의 경우 IDE 메뉴 모음으로 이동 하 **고 도구를 선택 하** 여 Visual Studio 설치 관리자를 엽니다 > **도구 및 기능 가져오기** 를 선택 합니다. **작업** 탭에서 하나 이상의 **Azure 개발** 워크 로드 ( **웹 & 클라우드**) 또는 **데이터 저장소 및 처리** 워크 로드 ( **다른 도구 집합**아래)를 선택 합니다.

  ![작업 선택, Visual Studio 설치 관리자](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- For Visual Studio 2015:

    [Data Lake 도구를 다운로드](https://www.microsoft.com/download/details.aspx?id=49504)합니다. Visual Studio 버전과 일치하는 Data Lake Tools 버전을 선택하세요.

## <a name="update-data-lake-tools-for-visual-studio"></a>Visual Studio 용 업데이트 Data Lake 도구  

그런 다음 Data Lake 도구를 최신 버전으로 업데이트 해야 합니다.

1. Visual Studio를 엽니다.

2. **시작** 창에서 **코드를 제외**하 고 계속을 선택 합니다.

3. Visual Studio IDE 메뉴 모음에서 **확장 > 확장** **관리**를 선택 합니다.

4. **확장 관리** 대화 상자에서 **업데이트** 노드를 확장 합니다.

5. 사용 가능한 업데이트 목록에 **Azure Data Lake 및 Stream 분석 도구가**포함 되어 있는 경우 해당 업데이트를 선택 합니다. 그런 다음 해당 **업데이트** 단추를 선택 합니다. **다운로드 및 설치** 대화 상자가 표시 되 고 사라진 후 Visual Studio에서 **Azure Data Lake 및 Stream analytics 도구** 확장을 업데이트 일정에 추가 합니다.

6. 모든 Visual Studio 창을 닫습니다. **VSIX 설치 관리자** 대화 상자가 나타납니다.

7. 라이선스 **를 선택 하** 여 사용 조건을 읽고, **닫기** 를 선택 하 여 **VSIX 설치 관리자** 대화 상자로 돌아갑니다.

8. **수정**을 선택합니다. 확장 업데이트 설치가 시작 됩니다. 잠시 후에 대화 상자를 변경 하 여 수정 완료를 표시 합니다. **닫기**를 선택 하 고 Visual Studio를 다시 시작 하 여 설치를 완료 합니다.

> [!NOTE]  
> Data Lake Tools 버전 2.3.0.0 이상만 사용하여 대화형 쿼리 클러스터에 연결하고 대화형 Hive 쿼리를 실행할 수 있습니다.

## <a name="connect-to-azure-subscriptions"></a>Azure 구독에 연결

Visual Studio 용 Data Lake 도구를 사용 하 여 HDInsight 클러스터에 연결 하 고, 몇 가지 기본 관리 작업을 수행 하 고, Hive 쿼리를 실행할 수 있습니다.

> [!NOTE]  
> 일반 Hadoop 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [Visual Studio를 사용 하 여 Hive 쿼리를 작성 및 제출 하는 방법](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)을 참조 하세요.

### <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

Azure 구독에 연결하려면,

1. Visual Studio를 엽니다.

2. **시작** 창에서 **코드를 제외**하 고 계속을 선택 합니다.

3. IDE 메뉴 모음에서 **뷰** > **서버 탐색기**를 선택 합니다.

4. **서버 탐색기**에서 **Azure**를 마우스 오른쪽 단추로 클릭 하 고 **Microsoft Azure 구독에 연결**을 선택 하 여 인증 프로세스를 완료 합니다. **서버 탐색기**에서 **Azure** > **hdinsight** 를 확장 하 여 기존 hdinsight 클러스터 목록을 확인 합니다.

5. 클러스터가 없는 경우 Azure Portal, Azure PowerShell 또는 HDInsight SDK를 사용 하 여 만듭니다. 자세한 내용은 [HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md)을 참조 하세요.

   ![HDInsight 클러스터 목록, 서버 탐색기, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. HDInsight 클러스터를 확장합니다. 클러스터에는 **Hive 데이터베이스**, 기본 저장소 계정, 추가 연결 된 저장소 계정 및 **Hadoop 서비스 로그**에 대 한 노드가 포함 됩니다. 엔터티를 더 확장할 수 있습니다.

Azure 구독에 연결되면 다음 작업을 수행할 수 있습니다.

### <a name="connect-to-azure-from-visual-studio"></a>Visual Studio에서 Azure에 연결

Visual Studio에서 Azure Portal에 연결하려면,

1. **서버 탐색기**에서 **Azure** > **HDInsight** 를 확장 하 고 클러스터를 선택 합니다.

2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **Azure Portal에서 클러스터 관리**를 선택 합니다.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Visual Studio의 질문과 의견 제공

질문을 하 고 Visual Studio에서 피드백을 제공 하려면 다음을 수행 하세요.

1. 서버 탐색기에서 **Azure** > **HDInsight**를 선택 합니다.

2. **HDInsight** 를 마우스 오른쪽 단추로 클릭 하 고 **MSDN 포럼** 을 선택 하 여 **질문 하거나 피드백을 제공 해** 주세요.

## <a name="link-to-or-edit-a-cluster"></a>클러스터에 연결 하거나 클러스터를 편집 합니다.

> [!NOTE]
> 현재 연결할 수 있는 HDInsight 클러스터의 유일한 유형은 Hive 유형입니다.

HDInsight 클러스터를 연결 하려면:

1. **Hdinsight**를 마우스 오른쪽 단추로 클릭 한 다음 Hdinsight **클러스터 연결** 을 선택 하 여 **hdinsight 클러스터 연결** 대화 상자를 표시 합니다.

2. Https\:형식으로 **연결 Url** 을 입력 *//\<클러스터&nbsp;이름 >. azurehdinsight*. **클러스터 이름은** 다른 필드로 이동할 때 URL의 클러스터 이름 부분으로 자동으로 채워집니다. 그런 다음 **사용자 이름** 및 **암호**를 입력 하 고 **다음**을 선택 합니다.

    ![클러스터, HDInsight, Visual Studio 연결](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. **마침**을 선택합니다. 클러스터가 성공적으로 연결 되 면 클러스터는 **HDInsight** 노드 아래에 나열 됩니다.

연결 된 클러스터를 업데이트 하려면 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **편집**을 선택 합니다. 그런 다음 클러스터 정보를 업데이트할 수 있습니다.

![연결 된 클러스터, HDInsight, Visual Studio 편집](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>연결된 리소스 탐색
서버 탐색기에서 기본 스토리지 계정 및 연결된 스토리지 계정을 확인할 수 있습니다. 기본 스토리지 계정을 확장한 경우 스토리지 계정의 컨테이너를 확인할 수 있습니다. 기본 스토리지 계정과 기본 컨테이너가 표시되어 있습니다.

![서버 탐색기에서 Visual Studio 연결 된 리소스에 대 한 Data Lake 도구](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

컨테이너의 내용을 보려면 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **컨테이너 보기** 를 선택 합니다. 컨테이너를 연 후 도구 모음 단추를 사용 하 여 콘텐츠 목록을 **새로 고치고** , **blob을 업로드**하 고, 선택한 blob을 **삭제**하 고, blob을 **열고**, 선택한 blob을 다운로드 (다른**이름으로 저장**) 할 수 있습니다.

![컨테이너 목록 및 blob 작업, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>대화형 Apache Hive 쿼리 실행
[Apache Hive](https://hive.apache.org)는 Hadoop을 기반으로 하는 데이터 웨어하우스 인프라입니다. Hive는 데이터 요약, 쿼리 및 분석에 사용됩니다. Data Lake Tools for Visual Studio를 사용하여 Visual Studio에서 Hive 쿼리를 실행할 수 있습니다. Hive에 대 한 자세한 내용은 [Azure HDInsight의 Apache Hive 및 HiveQL?](hdinsight-use-hive.md)을 참조 하세요.

[Azure HDInsight의 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 는 Apache Hive 2.1의 [Llap에서 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) 를 사용 합니다. 대화형 쿼리는 크고 저장 된 데이터 집합에 대 한 복잡 한 데이터 웨어하우스 스타일 쿼리에 대해 대화형 작업을 가져옵니다. 대화형 쿼리에서 Hive 쿼리를 실행 하는 것은 기존의 Hive 일괄 작업 보다 훨씬 빠릅니다. 

> [!NOTE]  
> [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터에 연결하는 경우에만 대화형 Hive 쿼리를 실행할 수 있습니다.

또한 Data Lake Tools for Visual Studio를 사용하여 Hive 작업에 대해 자세히 확인할 수도 있습니다. Data Lake Tools for Visual Studio는 특정 Hive 작업에 대한 YARN 로그를 수집하고 표시합니다.

**서버 탐색기**에서 **Azure** > **HDInsight** 를 선택 하 고 클러스터를 선택 합니다.  이 노드는 수행할 섹션의 **서버 탐색기** 시작 지점입니다.

### <a name="view-hivesampletable"></a>Hivesampletable 보기

모든 HDInsight 클러스터에는 `hivesampletable`이라는 기본 샘플 Hive 테이블이 있습니다.  

클러스터에서 **Hive 데이터베이스** > **기본** > **hivesampletable**를 선택 합니다.

- `hivesampletable` 스키마를 보려면 다음을 수행 합니다.

    **Hivesampletable**를 확장 합니다. `hivesampletable` 열의 이름 및 데이터 형식이 표시 됩니다.

- `hivesampletable` 데이터를 보려면 다음을 수행 합니다.

    **Hivesampletable**를 마우스 오른쪽 단추로 클릭 하 고 **상위 100 행 보기**를 선택 합니다. 100 결과 목록이 **Hive 테이블: hivesampletable** 창에 표시 됩니다. 이 작업은 Hive ODBC 드라이버를 사용 하 여 다음 Hive 쿼리를 실행 하는 것과 같습니다.

    `SELECT * FROM hivesampletable LIMIT 100`

    행 **수**를 변경 하 여 행 수를 사용자 지정할 수 있습니다. 드롭다운 목록에서 50, 100, 200 또는 1000 행을 선택할 수 있습니다.

### <a name="create-hive-tables"></a>Hive 테이블 만들기
Hive 테이블을 만들려면 GUI를 사용하거나 Hive 쿼리를 사용할 수 있습니다. Hive 쿼리를 사용 하는 방법에 대 한 자세한 내용은 [hive 쿼리 만들기 및 실행](#create-and-run-hive-queries)을 참조 하세요.

1. 클러스터에서 **Hive 데이터베이스** > **기본값**을 선택 합니다.

2. **기본값**을 마우스 오른쪽 단추로 클릭 하 고 **테이블 만들기**를 선택 합니다.

3. 테이블을 구성합니다.

4. **테이블 만들기** 단추를 선택 하 여 작업을 제출 하면 새 Hive 테이블이 생성 됩니다.

    ![테이블 창, Hive, HDInsight 클러스터, Visual Studio 만들기](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Hive 쿼리 만들기 및 실행
Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리 만들기
* Hive 애플리케이션 만들기

#### <a name="create-an-ad-hoc-query"></a>임시 쿼리 만들기

임시 쿼리를 만들고 실행 하려면 다음을 수행 합니다.

1. 쿼리를 실행 하려는 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **Hive 쿼리 작성**을 선택 합니다.  

2. Hive 쿼리를 입력 합니다.

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 `SELECT * FROM`를 입력 하면 IntelliSense에서 제안 된 테이블 이름을 모두 나열 합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다.

    ![IntelliSense 예제 1, Hive 임시 쿼리, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 예제 2, Hive 임시 쿼리, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.

    다음은 사용할 수 있는 샘플 쿼리입니다.

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 실행 모드를 선택 합니다.

    * **대화형**  

        첫 번째 드롭다운 목록에서 **Interactive**를 선택 하 고 **실행**을 선택 합니다.

        ![대화형 모드, Hive 임시 쿼리, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        첫 번째 드롭다운 목록에서 **Batch**를 선택 하 고 **제출** 을 선택 합니다. 또는 **제출** 옆에 있는 드롭다운 아이콘을 선택 하 고 **고급**을 선택 합니다.

        ![일괄 처리 모드, Hive 임시 쿼리, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        고급 제출 옵션을 선택 하면 **스크립트 제출** 대화 상자가 나타납니다. 스크립트에 대 한 **작업 이름**, **인수**, **추가 구성**및 **상태 디렉터리** 를 구성 합니다.

        ![스크립트 제출 대화 상자, Hive 임시 쿼리, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > 일괄 처리를 대화형 쿼리 클러스터로 전송할 수 없습니다.  대화형 모드를 사용 해야 합니다.

#### <a name="create-a-hive-application"></a>Hive 애플리케이션 만들기

Hive 솔루션을 만들고 실행하려면,

1. 메뉴 모음에서 **파일** > **새** > **프로젝트**를 선택 합니다.

2. **새 프로젝트 만들기** 창에서 검색 상자를 선택 하 고 **Hive**를 입력 합니다. 그런 다음 **Hive 응용 프로그램** 을 선택 하 고 **다음**을 선택 합니다.

3. **새 프로젝트 구성** 창에서 **프로젝트 이름을**입력 하 고 프로젝트 **위치**를 선택한 다음 **만들기**를 선택 합니다.

    ![새 Hive 응용 프로그램, 새 프로젝트 창 구성, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. **솔루션 탐색기**에서 **Script.hql**을 두 번 클릭하여 이 스크립트를 엽니다.

### <a name="view-job-summary-and-output"></a>작업 요약 및 출력 보기

작업 요약은 **일괄 처리** 모드와 **대화형** 모드에서 약간 다릅니다.

![Hive 작업 요약 창, batch 및 대화형 모드, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

작업 상태가 **완료**로 변경 될 때까지 **새로 고침** 아이콘을 사용 하 여 상태를 업데이트 합니다.  

* **일괄 처리** 모드의 작업 세부 정보를 보려면 아래쪽의 링크를 선택 하 여 **작업 쿼리**, **작업 출력**또는 **작업 로그**를 확인 하거나 **Yarn 로그를 확인**합니다.

* **대화형** 모드의 작업 세부 정보는 **Output** and **HiveServer2 output** pane을 참조 하세요.

    ![Hive 대화형 작업 출력, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>작업 그래프 보기

현재 작업 그래프는 Tez를 실행 엔진으로 사용 하는 Hive 작업에 대해서만 표시 됩니다.  Tez를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure HDInsight의 Apache Hive 및 HiveQL?](hdinsight-use-hive.md)을 참조 하세요.  또한 [Map 줄임 대신 Apache Tez 사용](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)을 참조 하세요.  

꼭 짓 점 내의 모든 연산자를 보려면 작업 그래프의 꼭 짓 점을 두 번 클릭 합니다. 또한 특정 연산자를 가리켜서 연산자에 대한 자세한 정보를 볼 수도 있습니다.

Tez가 실행 엔진으로 지정 된 경우에도 Tez 응용 프로그램이 시작 되지 않으면 작업 그래프가 표시 되지 않을 수 있습니다.  이 상황은 작업에 DML 문이 포함 되어 있지 않거나 DML 문이 Tez 응용 프로그램을 시작 하지 않고 반환 될 수 있기 때문에 발생할 수 있습니다. 예를 들어 `SELECT * FROM table1`은 Tez 응용 프로그램을 시작 하지 않습니다.

![Apache Hive 작업 그래프, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>작업 실행 세부 정보 보기

작업 그래프에서 **작업 실행 세부** 정보를 선택 하 여 Hive 작업에 대 한 구조화 되 고 시각화 된 정보를 가져올 수 있습니다. 더 많은 작업 정보를 얻을 수도 있습니다. 성능 문제가 발생하면 보기를 사용하여 문제에 대한 자세한 정보를 얻을 수 있습니다. 예를 들어 각 작업의 작동 방식 및 각 작업에 대 한 자세한 정보 (데이터 읽기/쓰기, 일정/시작/종료 시간 등)에 대 한 정보를 검색할 수 있습니다. 이 정보를 사용하여 시각화된 정보에 따라 작업 구성 또는 시스템 아키텍처를 튜닝합니다.

![작업 실행 보기 창, Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive 작업 보기

Hive 작업에 대한 작업 쿼리, 작업 출력, 작업 로그 및 Yarn 로그를 볼 수 있습니다.

최신 릴리스의 도구에서는 YARN 로그를 수집하고 표시하여 Hive 작업에 대한 자세한 정보를 확인할 수 있습니다. Yarn 로그는 성능 문제를 조사하는 데 도움이 됩니다. HDInsight에서 Yarn 로그를 수집 하는 방법에 대 한 자세한 내용은 [액세스 APACHE HADOOP Yarn 응용 프로그램 로그](../hdinsight-hadoop-access-yarn-app-logs-linux.md)를 참조 하세요.

Hive 작업을 보려면,

1. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **작업 보기**를 선택 합니다.

    ![작업 보기, Apache Hive, HDInsight 클러스터, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    클러스터에서 실행한 Hive 작업 목록이 표시됩니다.  

2. 작업을 선택합니다. **Hive 작업 요약** 창에서 다음 링크 중 하나를 선택 합니다.
    - **작업 쿼리**
    - **작업 출력**
    - **작업 로그**  
    - **Yarn 로그**

## <a name="run-apache-pig-scripts"></a>Apache Pig 스크립트 실행

1. 메뉴 모음에서 **파일** > **새** > **프로젝트**를 선택 합니다.

2. **시작** 창에서 검색 상자를 선택 하 고 **Pig**을 입력 합니다. 그런 다음 **Pig 응용 프로그램** 을 선택 하 고 **다음**을 선택 합니다.

3. **새 프로젝트 구성** 창에서 **프로젝트 이름을**입력 하 고 프로젝트의 **위치** 를 선택 하거나 만듭니다. 그런 다음 **만들기**를 선택합니다.

4. IDE **솔루션 탐색기** 창에서 **pig** 를 두 번 클릭 하 여 스크립트를 엽니다.

## <a name="feedback-and-known-issues"></a>사용자 의견 및 알려진 문제

* null 값으로 시작되는 결과가 표시되지 않는 문제는 해결되었습니다. 이 문제로 인해 차단되는 경우 지원 팀에 문의하세요.

* Visual Studio에서 만드는 HQL 스크립트는 사용자의 로컬 지역 설정에 따라 인코딩됩니다. 클러스터에 스크립트를 이진 파일로 업로드하면 스크립트가 제대로 실행되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Data Lake Tools for Visual Studio 패키지를 사용하여 Visual Studio에서 HDInsight 클러스터에 연결하는 방법을 알아보았습니다. 또한 Hive 쿼리를 실행하는 방법도 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Data Lake tools for Visual Studio를 사용하여 Apache Hive 쿼리 실행](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight의 Apache Hive 및 HiveQL 무엇 인가요?](hdinsight-use-hive.md)
* [Apache Hadoop 클러스터 만들기-템플릿](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에서 Apache Hadoop 작업 제출](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight의 Apache Hive 및 Apache Hadoop를 사용 하 여 Twitter 데이터 분석](../hdinsight-analyze-twitter-data-linux.md)
