---
title: 아파치 하두프 & 비주얼 스튜디오 데이터 레이크 도구 - Azure HDInsight
description: Visual Studio용 데이터 레이크 도구를 설치하고 사용하는 방법을 알아봅니다. 도구를 사용하여 Azure HDInsight에서 아파치 하두롭 클러스터에 연결한 다음 Hive 쿼리를 실행합니다.
keywords: Hadoop 도구, Hive 쿼리, Visual Studio, Visual Studio Hadoop Hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383517"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행

비주얼 스튜디오(데이터 레이크 도구)에 Microsoft Azure 데이터 레이크 및 스트림 분석 도구를 사용하는 방법에 대해 알아봅니다. 이 도구를 사용하여 [Azure HDInsight의 아파치 하두롭 클러스터에](apache-hadoop-introduction.md) 연결하고 Hive 쿼리를 제출합니다.  

HDInsight 사용에 대한 자세한 내용은 [HDInsight 시작 을](apache-hadoop-linux-tutorial-get-started.md)참조하십시오.  

아파치 스톰에 연결하는 방법에 대한 자세한 내용은 [데이터 레이크 도구를 사용하여 아파치 스톰에 대한 C# 토폴로지 개발을](../storm/apache-storm-develop-csharp-visual-studio-topology.md)참조하십시오.

Data Lake Tools for Visual Studio를 사용하여 Azure Data Lake Analytics 및 HDInsight에 액세스할 수 있습니다. Data Lake Tools에 대한 자세한 내용은 [Data Lake Tools for Visual Studio를 사용하여 U-SQL 스크립트 개발](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하고 Visual Studio용 데이터 레이크 도구를 사용하려면 다음 항목이 필요합니다.

* Azure HDInsight 클러스터를 만듭니다. HDInsight 클러스터를 만들려면 [Azure HDInsight에서 Apache Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요. 대화형 Apache Hive 쿼리를 실행하려면 [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터가 필요합니다.  

* [비주얼 스튜디오](https://visualstudio.microsoft.com/downloads/). [비주얼 스튜디오 커뮤니티 에디션은](https://visualstudio.microsoft.com/vs/community/) 무료입니다. 여기에 표시된 지침은 [Visual Studio 2019에](https://visualstudio.microsoft.com/downloads/)대한 것입니다.

## <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구 설치  

적절한 지침에 따라 Visual Studio 버전에 데이터 레이크 도구를 설치합니다.

* 비주얼 스튜디오 2017 또는 비주얼 스튜디오 2019의 경우:

    Visual Studio를 설치하는 동안 **Azure 개발** 워크로드 또는 데이터 저장소 **및 처리** 워크로드를 포함해야 합니다.  

    기존 Visual Studio 설치의 경우 IDE 메뉴 모음으로 이동하여 **도구** > **받기 도구 및 기능을** 선택하여 Visual Studio 설치 프로그램을 엽니다. 워크로드 탭에서 최소한 **Azure 개발** 워크로드(웹 **& 클라우드)를** **선택합니다.** 또는 **데이터 저장소 및 처리** 워크로드(기타 도구 **집합**아래)를 선택합니다.

  ![워크로드 선택, 비주얼 스튜디오 설치 프로그램](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* 비주얼 스튜디오 2015의 경우:

    [데이터 호수 도구를 다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=49504) Visual Studio 버전과 일치하는 Data Lake Tools 버전을 선택하세요.

## <a name="update-data-lake-tools-for-visual-studio"></a>비주얼 스튜디오용 데이터 레이크 도구 업데이트  

그런 다음 데이터 레이크 도구를 최신 버전으로 업데이트해야 합니다.

1. Visual Studio를 엽니다.

2. **시작** 창에서 **코드 없이 계속을**선택합니다.

3. 시각적 스튜디오 IDE 메뉴 모음에서 **확장** > **관리 확장을 선택합니다.**

4. 확장 관리 대화 상자에서 **업데이트** 노드를 **확장합니다.**

5. 사용 가능한 업데이트 목록에 **Azure 데이터 레이크 및 스트림 분석 도구가**포함된 경우 이를 선택합니다. 그런 다음 **업데이트** 단추를 선택합니다. 다운로드 **및 설치** 대화 상자가 나타나고 사라지면 Visual Studio는 **Azure 데이터 레이크 및 스트림 분석 도구** 확장을 업데이트 일정에 추가합니다.

6. 모든 Visual Studio 창을 닫습니다. **VSIX 설치 프로그램** 대화 상자가 나타납니다.

7. **라이센스를** 선택하여 라이센스 조건을 읽은 다음 **닫기를** 선택하여 **VSIX 설치 관리자** 대화 상자로 돌아갑니다.

8. **수정**을 선택합니다. 확장 업데이트 설치가 시작됩니다. 잠시 후 대화 상자가 변경되어 수정이 완료되었지만 완료된 것으로 표시됩니다. **닫기를**선택한 다음 Visual Studio를 다시 시작하여 설치를 완료합니다.

> [!NOTE]  
> Data Lake Tools 버전 2.3.0.0 이상만 사용하여 대화형 쿼리 클러스터에 연결하고 대화형 Hive 쿼리를 실행할 수 있습니다.

## <a name="connect-to-azure-subscriptions"></a>Azure 구독에 연결

Visual Studio용 데이터 레이크 도구를 사용하여 HDInsight 클러스터에 연결하고, 몇 가지 기본 관리 작업을 수행하며, Hive 쿼리를 실행할 수 있습니다.

> [!NOTE]  
> 일반 Hadoop 클러스터에 연결하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 Hive 쿼리를 작성하고 제출하는 방법을](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)참조하십시오.

### <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

Azure 구독에 연결하려면,

1. Visual Studio를 엽니다.

2. **시작** 창에서 **코드 없이 계속을**선택합니다.

3. IDE 메뉴 모음에서 **View** > **서버 탐색기**보기를 선택합니다.

4. **서버 탐색기에서** **Azure를**마우스 오른쪽 단추로 클릭하고 **Microsoft Azure 구독에 연결/를**선택하고 인증 프로세스를 완료합니다. **서버 탐색기에서** **Azure** > **HDInsight를** 확장하여 기존 HDInsight 클러스터 목록을 봅니다.

5. 클러스터가 없는 경우 Azure 포털, Azure PowerShell 또는 HDInsight SDK를 사용하여 클러스터를 만듭니다. 자세한 내용은 [HDInsight의 클러스터 설정을](../hdinsight-hadoop-provision-linux-clusters.md)참조하십시오.

   ![HDInsight 클러스터 목록, 서버 탐색기, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. HDInsight 클러스터를 확장합니다. 클러스터에는 Hive 데이터베이스에 대한 **노드가 포함되어 있습니다.** 또한 기본 저장소 계정, 연결된 추가 저장소 계정 및 **Hadoop 서비스 로그**. 엔터티를 더 확장할 수 있습니다.

Azure 구독에 연결되면 다음 작업을 수행할 수 있습니다.

### <a name="connect-to-azure-from-visual-studio"></a>비주얼 스튜디오에서 Azure에 연결

Visual Studio에서 Azure Portal에 연결하려면,

1. **서버 탐색기에서** **Azure** > **HDInsight를** 확장하고 클러스터를 선택합니다.

2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭하고 **Azure 포털에서 클러스터 관리를**선택합니다.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>비주얼 스튜디오의 질문과 피드백 제공

질문을 하거나 Visual Studio의 피드백을 제공하려면 다음 단계를 수행하십시오.

1. 서버 탐색기에서 **Azure** > **HDInsight를**선택합니다.

2. **HDInsight를** 마우스 오른쪽 버튼으로 클릭하고 **MSDN 포럼을** 선택하여 질문을 하거나 **피드백을 제공하십시오.**

## <a name="link-to-or-edit-a-cluster"></a>클러스터에 대한 링크 또는 편집

> [!NOTE]
> 현재 연결할 수 있는 HDInsight 클러스터의 유일한 유형은 Hive 유형입니다.

HDInsight 클러스터를 연결하려면 다음을 수행하십시오.

1. **HDInsight를**마우스 오른쪽 단추로 클릭한 다음 **HDInsight 클러스터 링크를** 선택하여 **HDInsight 클러스터 연결** 대화 상자를 표시합니다.

2. 양식에 `https://CLUSTERNAME.azurehdinsight.net` **연결 URL을** 입력합니다. **클러스터 이름은** 다른 필드로 이동하면 URL의 클러스터 이름 부분으로 자동으로 채워지습니다. 그런 다음 **사용자 이름과** **암호를**입력하고 **다음**을 선택합니다.

    ![클러스터 링크, HDInsight, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. **마침**을 선택합니다. 클러스터 연결이 성공하면 클러스터가 **HDInsight** 노드 아래에 나열됩니다.

연결된 클러스터를 업데이트하려면 클러스터를 마우스 오른쪽 단추로 클릭하고 **편집을**선택합니다. 그런 다음 클러스터 정보를 업데이트할 수 있습니다.

![링크된 클러스터 편집, HDInsight, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>연결된 리소스 탐색

서버 탐색기에서 기본 스토리지 계정 및 연결된 스토리지 계정을 확인할 수 있습니다. 기본 스토리지 계정을 확장한 경우 스토리지 계정의 컨테이너를 확인할 수 있습니다. 기본 스토리지 계정과 기본 컨테이너가 표시되어 있습니다.

![서버 탐색기에서 Visual Studio 연결 리소스를 위한 데이터 레이크 도구](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

컨테이너를 마우스 오른쪽 단추로 클릭하고 **컨테이너 보기를** 선택하여 컨테이너의 내용을 봅니다. 컨테이너를 연 후 도구 모음 단추를 사용하여 콘텐츠 목록을 **새로 고치고,** **Blob 업로드,** **선택한 Blob 삭제,** **Blob 열기**및 선택한 Blob을 다운로드할 수 있습니다.**Save As**

![컨테이너 목록 및 Blob 작업, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>대화형 Apache Hive 쿼리 실행

[Apache Hive](https://hive.apache.org)는 Hadoop을 기반으로 하는 데이터 웨어하우스 인프라입니다. Hive는 데이터 요약, 쿼리 및 분석에 사용됩니다. Data Lake Tools for Visual Studio를 사용하여 Visual Studio에서 Hive 쿼리를 실행할 수 있습니다. 하이브에 대한 자세한 내용은 [Azure HDInsight에서 아파치 하이브 및 HiveQL이란 무엇입니까?](hdinsight-use-hive.md)

[Azure HDInsight의 대화형 쿼리는](../interactive-query/apache-interactive-query-get-started.md) 아파치 하이브 2.1의 [LLAP에서 하이브를](https://cwiki.apache.org/confluence/display/Hive/LLAP) 사용합니다. 대화형 쿼리는 대규모의 저장된 데이터 집합에서 복잡한 데이터 웨어하우스 스타일 쿼리에 상호 작용을 제공합니다. 대화형 쿼리에서 Hive 쿼리를 실행하는 것은 기존 Hive 일괄 처리 작업보다 훨씬 빠릅니다. 

> [!NOTE]  
> [HDInsight 대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터에 연결하는 경우에만 대화형 Hive 쿼리를 실행할 수 있습니다.

또한 Visual Studio용 데이터 레이크 도구를 사용하여 Hive 작업 의 내부 내용을 확인할 수도 있습니다. Data Lake Tools for Visual Studio는 특정 Hive 작업에 대한 YARN 로그를 수집하고 표시합니다.

**서버 탐색기에서** **Azure** > **HDInsight를** 선택하고 클러스터를 선택합니다.  이 노드는 섹션을 따를 **서버 탐색기의** 시작점입니다.

### <a name="view-hivesampletable"></a>hivesampletable 보기

모든 HDInsight 클러스터에는 기본 샘플 Hive 테이블이 있습니다. `hivesampletable`  

클러스터에서 **Hive 데이터베이스** > **기본** > **하이브 샘플 테이블을**선택합니다.

* 스키마를 `hivesampletable` 보려면 다음을 수행하십시오.

    **하이브 샘플 테이블을 확장합니다.** 열의 이름과 데이터 `hivesampletable` 형식이 표시됩니다.

* 데이터를 보려면 `hivesampletable` 다음을 수행하십시오.

    **하이브 샘플 테이블을**마우스 오른쪽 단추로 클릭하고 상위 **100개 행 보기를**선택합니다. 100개의 결과 목록이 **하이브 테이블: 하이브 샘플 테이블** 창에 나타납니다. 이 작업은 Hive ODBC 드라이버를 사용하여 다음 Hive 쿼리를 실행하는 것과 같습니다.

    `SELECT * FROM hivesampletable LIMIT 100`

    행 수를 변경하여 행 **수를**사용자 지정할 수 있습니다. 드롭다운 목록에서 50개, 100개, 200개 또는 1000개의 행을 선택할 수 있습니다.

### <a name="create-hive-tables"></a>Hive 테이블 만들기

Hive 테이블을 만들려면 GUI를 사용하거나 Hive 쿼리를 사용할 수 있습니다. Hive 쿼리 사용에 대한 자세한 내용은 [Hive 쿼리 만들기 및 실행](#create-and-run-hive-queries)을 참조하십시오.

1. 클러스터에서 Hive 데이터베이스**기본값.을** **선택합니다.** > 

2. **기본값을**마우스 오른쪽 단추로 클릭하고 **테이블 만들기를**선택합니다.

3. 테이블을 구성합니다.

4. 테이블 **만들기** 단추를 선택하여 새 Hive 테이블을 만드는 작업을 제출합니다.

    ![테이블 창 만들기, 하이브, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Hive 쿼리 만들기 및 실행

Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리 만들기
* Hive 애플리케이션 만들기

#### <a name="create-an-ad-hoc-query"></a>임시 쿼리 만들기

임시 쿼리를 만들고 실행하려면 다음을 수행합니다.

1. 쿼리를 실행할 클러스터를 마우스 오른쪽 단추로 클릭하고 **Hive 쿼리 작성을**선택합니다.  

2. Hive 쿼리를 입력합니다.

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 `SELECT * FROM`입력하는 경우 IntelliSense는 제안된 모든 테이블 이름을 나열합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다.

    ![IntelliSense 예제 1, 하이브 임시 쿼리, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 예제 2, 하이브 임시 쿼리, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.

    다음은 사용할 수 있는 샘플 쿼리입니다.

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 실행 모드를 선택합니다.

    * **대화형**  

        첫 번째 드롭다운 목록에서 **대화형**을 선택한 다음 **실행을**선택합니다.

        ![인터랙티브 모드, 하이브 애드혹 쿼리, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        첫 번째 드롭다운 목록에서 **일괄 처리를**선택한 다음 **제출을**선택합니다. 또는 **제출** 옆에 있는 드롭다운 아이콘을 선택하고 고급 을 **선택합니다.**

        ![배치 모드, 하이브 임시 쿼리, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        고급 제출 옵션을 선택하면 **스크립트 제출** 대화 상자가 나타납니다. 스크립트에 대한 **작업 이름,** 인수, 추가 구성 및 **상태 디렉터리를** **구성합니다.** **Additional Configurations**

        ![스크립트 대화 상자 제출, 하이브 임시 쿼리, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > 대화형 쿼리 클러스터에 일괄 처리를 제출할 수 없습니다.  대화형 모드를 사용해야 합니다.

#### <a name="create-a-hive-application"></a>Hive 애플리케이션 만들기

Hive 솔루션을 만들고 실행하려면,

1. 메뉴 모음에서**새** > **프로젝트** **파일** > 선택.

2. 새 **프로젝트 만들기** 창에서 검색 상자를 선택하고 **하이브를**입력합니다. 그런 다음 **하이브 응용 프로그램을** 선택하고 **다음을**선택합니다.

3. 새 **프로젝트 구성** 창에서 **프로젝트 이름을**입력하고 프로젝트 **위치를**선택또는 작성한 다음 **을**선택합니다.

    ![새로운 하이브 응용 프로그램, 새 프로젝트 창 구성, HDInsight 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. **솔루션 탐색기**에서 **Script.hql**을 두 번 클릭하여 이 스크립트를 엽니다.

### <a name="view-job-summary-and-output"></a>작업 요약 및 출력 보기

작업 요약은 일괄 **처리** 모드와 **대화형** 모드 간에 약간 다릅니다.

![하이브 작업 요약 창, 배치 및 대화 형 모드, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

새로 **고침** 아이콘을 사용하여 작업 상태가 **완료됨으로**변경될 때까지 상태를 업데이트합니다.  

* **일괄 처리** 모드에서 작업 세부 정보의 경우 하단의 링크를 선택하여 **작업 쿼리,** **작업 출력**또는 **작업 로그를**보거나 **원사 로그를 볼**수 있습니다.

* **대화형** 모드에서 작업 세부 정보는 **출력** 및 **HiveServer2 출력** 창을 참조하십시오.

    ![하이브 인터랙티브 작업 출력, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>작업 그래프 보기

현재 작업 그래프는 Tez를 실행 엔진으로 사용하는 Hive 작업에대해서만 표시됩니다.  Tez 를 사용하도록 설정하는 방법에 대한 자세한 [내용은 Azure HDInsight에서 아파치 하이브 및 HiveQL이란 무엇입니까?](hdinsight-use-hive.md)  또한 [지도 감소 대신 아파치 테즈를 사용하십시오.](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)  

정점 내부의 모든 연산을 보려면 작업 그래프의 정점을 두 번 클릭합니다. 또한 특정 연산자를 가리켜서 연산자에 대한 자세한 정보를 볼 수도 있습니다.

Tez가 실행 엔진으로 지정되더라도 Tez 응용 프로그램이 실행되지 않으면 작업 그래프가 나타나지 않을 수 있습니다.  작업에 DML 문이 포함되어 있지 않기 때문에 이 상황이 발생할 수 있습니다. 또는 DML 문이 Tez 응용 프로그램을 시작하지 않고 반환할 수 있기 때문입니다. 예를 들어 `SELECT * FROM table1` Tez 응용 프로그램을 실행하지 않습니다.

![아파치 하이브 작업 그래프, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>작업 실행 세부 정보 보기

작업 그래프에서 **작업 실행 세부 정보를** 선택하여 Hive 작업에 대한 구조화 및 시각화된 정보를 얻을 수 있습니다. 또한 더 많은 작업 세부 정보를 얻을 수 있습니다. 성능 문제가 발생하면 보기를 사용하여 문제에 대한 자세한 정보를 얻을 수 있습니다. 예를 들어 각 작업의 작동 방식에 대한 정보와 각 작업에 대한 자세한 정보(데이터 읽기/쓰기, 일정/시작/종료 시간 등)를 검색할 수 있습니다. 이 정보를 사용하여 시각화된 정보에 따라 작업 구성 또는 시스템 아키텍처를 튜닝합니다.

![작업 실행 보기 창, 데이터 레이크 비주얼 스튜디오 도구](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive 작업 보기

Hive 작업에 대한 작업 쿼리, 작업 출력, 작업 로그 및 Yarn 로그를 볼 수 있습니다.

도구의 최신 릴리스에서는 원사 로그를 수집하고 표면화하여 Hive 작업 내부의 내용을 확인할 수 있습니다. Yarn 로그는 성능 문제를 조사하는 데 도움이 됩니다. HDInsight가 원사 로그를 수집하는 방법에 대한 자세한 내용은 [액세스 아파치 하두롭 YARN 응용 프로그램 로그를](../hdinsight-hadoop-access-yarn-app-logs-linux.md)참조하십시오.

Hive 작업을 보려면,

1. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭하고 **작업 보기를**선택합니다.

    ![보기 작업, 아파치 하이브, HDInsight 클러스터, 비주얼 스튜디오](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    클러스터에서 실행한 Hive 작업 목록이 표시됩니다.  

2. 작업을 선택합니다. **Hive 작업 요약** 창에서 다음 링크 중 하나를 선택합니다.
    - **작업 쿼리**
    - **작업 출력**
    - **작업 로그**  
    - **원사 로그**

## <a name="run-apache-pig-scripts"></a>Apache Pig 스크립트 실행

1. 메뉴 모음에서**새** > **프로젝트** **파일** > 선택.

2. **시작** 창에서 검색 상자를 선택하고 **돼지를**입력합니다. 그런 다음 **돼지 응용 프로그램을** 선택하고 **다음을**선택합니다.

3. 새 **프로젝트 구성** 창에서 **프로젝트 이름을**입력하고 프로젝트의 **위치를** 선택하거나 만듭니다. 그런 다음 **만들기**를 선택합니다.

4. IDE **솔루션 탐색기** 창에서 **Script.pig를** 두 번 클릭하여 스크립트를 엽니다.

## <a name="feedback-and-known-issues"></a>사용자 의견 및 알려진 문제

* null 값으로 시작되는 결과가 표시되지 않는 문제는 해결되었습니다. 이 문제로 인해 차단되는 경우 지원 팀에 문의하세요.

* Visual Studio에서 만드는 HQL 스크립트는 사용자의 로컬 지역 설정에 따라 인코딩됩니다. 클러스터에 스크립트를 이진 파일로 업로드하면 스크립트가 제대로 실행되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Data Lake Tools for Visual Studio 패키지를 사용하여 Visual Studio에서 HDInsight 클러스터에 연결하는 방법을 알아보았습니다. 또한 Hive 쿼리를 실행하는 방법도 알아보았습니다. 

* [Data Lake tools for Visual Studio를 사용하여 Apache Hive 쿼리 실행](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight에서 아파치 하이브와 하이브QL이란?](hdinsight-use-hive.md)
* [Apache Hadoop 클러스터 만들기 - 템플릿](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에서 아파치 하두프 작업 제출](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight에서 Apache Hive 및 Apache Hadoop을 사용하여 Twitter 데이터 분석](../hdinsight-analyze-twitter-data-linux.md)
