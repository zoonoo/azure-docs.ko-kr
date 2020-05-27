---
title: '자습서 - Azure Synapse Analytics용 Apache Spark: Synapse에 대한 Apache Spark 작업 정의'
description: 자습서 - Azure Synapse Analytics를 사용하여 Spark 작업 정의를 만들어서 Azure Synapse Analytics용 Apache Spark 풀에 제출합니다.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587938"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>자습서: Azure Synapse Analytics를 사용하여 Synapse Spark 풀에 대한 Apache Spark 작업 정의 만들기

이 자습서에서는 Azure Synapse Analytics를 사용하여 Spark 작업 정의를 만든 다음, Synapse Spark 풀에 제출하는 방법을 보여줍니다. 다음과 같은 몇 가지 방식으로 플러그 인을 사용할 수 있습니다.

* Synapse Spark 풀에서 Spark 작업 정의를 개발하고 제출합니다.
* 제출한 후 작업 세부 정보를 봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Synapse Spark 풀에서 Spark 작업 정의를 개발하고 제출합니다.
> * 제출한 후 작업 세부 정보를 봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Synapse Analytics 작업 영역. 지침은 [Azure Synapse Analytics 작업 영역 만들기](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)를 참조하세요.

## <a name="get-started"></a>시작하기

Spark 작업 정의를 제출하려면, 작업하려는 ADLS Gen2 파일 시스템의 Storage Blob 데이터 소유자여야 합니다. 그렇지 않으면, 권한을 수동으로 추가해야 합니다.

### <a name="scenario-1-add-permission"></a>시나리오 1: 권한 추가

1. [Microsoft Azure](https://ms.portal.azure.com)를 연 다음, 스토리지 계정을 엽니다.

2. **컨테이너**를 클릭한 다음, **파일 시스템**을 만듭니다. 이 자습서에서는 `sparkjob`를 사용합니다.

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Spark 제출 대화 상자](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. `sparkjob`을 열고 **Access Control(IAM)** 을 클릭한 다음, **추가**를 클릭하고 **역할 할당 추가**를 선택합니다.

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. **역할 할당**을 클릭하고 사용자 이름을 입력한 다음, 사용자 역할을 확인합니다.

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>시나리오 2: 폴더 구조 준비

Spark 작업 정의를 제출하기 전에 수행해야 하는 한 가지 작업은 ADLS Gen2에 파일을 업로드하고 폴더 구조를 준비하는 것입니다. Synapse Studio에서 스토리지 노드를 사용하여 파일을 저장하겠습니다.

1. [Azure Synapse Analytics](https://web.azuresynapse.net/)를 엽니다.

2. **데이터**를 클릭하고 **스토리지 계정**을 선택하여 ADLS Gen2 파일 시스템에 적절한 파일을 업로드합니다. Scala, Java, .NET 및 Python이 지원됩니다. 이 자습서에서는 그림의 예제를 데모로 사용하므로 원하는 대로 프로젝트 구조를 변경할 수 있습니다.

    ![Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Spark 작업 정의 만들기

1. [Azure Synapse Analytics](https://web.azuresynapse.net/)를 열고 **개발**을 선택합니다.

2. 왼쪽 창에서 **Spark 작업 정의**를 선택합니다.

3. "Spark 작업 정의" 오른쪽에 있는 **작업** 노드를 클릭합니다.

     ![새 Spark 작업 정의 만들기](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. **작업** 드롭다운 목록에서 **새 Spark 작업 정의**를 선택합니다.

     ![새 Spark 작업 정의 만들기](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. 새 Spark 작업 정의 창에서 언어를 선택한 후 다음 정보를 제공합니다.  

   * **언어**에 **Spark(Scala)** 를 선택합니다.

    |  속성   | Description   |  
    | ----- | ----- |  
    |작업 정의 이름| Spark 작업 정의의 이름을 입력합니다.  이 자습서에서는 `job definition sample`를 사용합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다.|  
    |주 정의 파일| 작업에 사용되는 주 파일입니다. 스토리지에서 JAR 파일을 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다. |
    |주 클래스 이름| 주 정의 파일에 있는 주 클래스 또는 정규화된 식별자입니다.|
    |명령줄 인수| 작업에 대한 선택적 인수입니다.|
    |참조 파일| 주 정의 파일에서 참조용으로 사용되는 추가 파일입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
    |Spark 풀| 선택한 Spark 풀에 작업이 제출됩니다.|
    |Spark 버전| Spark 풀에서 실행 중인 Spark 버전입니다.|
    |실행자| 작업에 대해 지정된 Spark 풀에 제공할 실행기의 수입니다.|
    |실행기 크기| 작업에 대해 지정된 Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|  
    |드라이버 크기| 작업에 대해 지정된 Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

    ![Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-scala-definition.png)

   * **언어**에 **PySpark(Python)** 를 선택합니다.

    |  속성   | Description   |  
    | ----- | ----- |  
    |작업 정의 이름| Spark 작업 정의의 이름을 입력합니다.  이 자습서에서는 `job definition sample`를 사용합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다.|  
    |주 정의 파일| 작업에 사용되는 주 파일입니다. 스토리지에서 PY 파일을 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
    |명령줄 인수| 작업에 대한 선택적 인수입니다.|
    |참조 파일| 주 정의 파일에서 참조용으로 사용되는 추가 파일입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
    |Spark 풀| 선택한 Spark 풀에 작업이 제출됩니다.|
    |Spark 버전| Spark 풀에서 실행 중인 Spark 버전입니다.|
    |실행자| 작업에 대해 지정된 Spark 풀에 제공할 실행기의 수입니다.|
    |실행기 크기| 작업에 대해 지정된 Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|  
    |드라이버 크기| 작업에 대해 지정된 Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

    ![Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-py-definition.png)

   * **언어**에 **.NET Spark(C#/F#)** 를 선택합니다.

    |  속성   | Description   |  
    | ----- | ----- |  
    |작업 정의 이름| Spark 작업 정의의 이름을 입력합니다.  이 자습서에서는 `job definition sample`를 사용합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다.|  
    |주 정의 파일| 작업에 사용되는 주 파일입니다. .NET for Spark 애플리케이션(즉, 주 실행 파일, 사용자 정의 함수를 포함하는 DLL 및 기타 필수 파일)이 포함된 ZIP 파일을 스토리지에서 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
    |주 실행 파일| 주 정의 ZIP 파일의 주 실행 파일입니다.|
    |명령줄 인수| 작업에 대한 선택적 인수입니다.|
    |참조 파일| 주 정의 ZIP 파일에 포함되지 않은 Spark for .NET 애플리케이션을 실행하기 위해 작업자 노드에 필요한 추가 파일(즉, 종속 jar, 추가 사용자 정의 함수 DLL 및 기타 구성 파일)입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
    |Spark 풀| 선택한 Spark 풀에 작업이 제출됩니다.|
    |Spark 버전| Spark 풀에서 실행 중인 Spark 버전입니다.|
    |실행자| 작업에 대해 지정된 Spark 풀에 제공할 실행기의 수입니다.|
    |실행기 크기| 작업에 대해 지정된 Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|  
    |드라이버 크기| 작업에 대해 지정된 Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

    ![Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-net-definition.png)

6. **게시**를 선택하여 Spark 작업 정의를 저장합니다.

    ![Spark 작업 정의 게시](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Spark 작업 정의 제출

Spark 작업 정의를 만든 후에 Synapse Spark 풀에 제출할 수 있습니다. 이 부분에서 샘플을 시도하기 전에 **시작**의 단계를 수행했는지 확인하십시오.

### <a name="scenario-1-submit-spark-job-definition"></a>시나리오 1: Spark 작업 정의 제출

1. Spark 작업 정의를 클릭하여 창을 엽니다.

      ![제출할 Spark 작업 정의 열기 ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. **제출** 아이콘을 클릭하여 선택한 Spark 풀에 프로젝트를 제출합니다. **Spark 모니터링 URL** 탭을 클릭하여 Spark 애플리케이션의 LogQuery를 볼 수 있습니다.

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Spark 제출 대화 상자](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>시나리오 2: Spark 작업 실행 진행률 보기

1. **모니터**를 클릭한 다음, **Spark 애플리케이션** 옵션을 선택합니다. 제출된 Spark 애플리케이션을 찾을 수 있습니다.

    ![Spark 애플리케이션 보기](./media/apache-spark-job-definitions/view-spark-application.png)

2. 그런 다음, Spark 애플리케이션을 클릭하면 **LogQuery** 창이 표시됩니다. **LogQuery**에서 작업 실행 진행률을 볼 수 있습니다.

    ![Spark 애플리케이션 LogQuery 보기](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>시나리오 3: 출력 파일 확인

 1. **데이터**를 클릭한 후 **스토리지 계정**을 선택합니다. 실행에 성공한 후, ADLS Gen2 스토리지로 이동하여 출력이 생성되었는지 확인할 수 있습니다.

    ![출력 파일 보기](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Synapse Analytics를 사용하여 Spark 작업 정의를 만든 다음, Synapse Spark 풀에 제출하는 방법을 보여줬습니다. 이제 Azure Synapse Analytics를 사용하여 Power BI 데이터 세트를 만들고 Power BI 데이터를 관리할 수 있습니다. 

- [Power BI Desktop의 데이터에 연결](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Power BI를 사용하여 시각화](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
