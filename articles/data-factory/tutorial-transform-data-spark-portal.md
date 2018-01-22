---
title: "Azure Data Factory에서 Spark를 사용하여 데이터 변환 | Microsoft Docs"
description: "이 자습서에서는 Azure Data Factory에서 Spark 작업을 사용하여 데이터를 변환하는 단계별 지침을 제공합니다."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: 8bd9382ed5a855368533c6bf2305682861c109c0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory에서 Spark 작업을 사용하여 클라우드의 데이터 변환
이 자습서에서는 Azure Portal을 사용하여 Spark 활동 및 주문형 HDInsight 연결된 서비스를 사용하여 데이터를 변환하는 Data Factory 파이프라인을 만듭니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * Spark 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure Storage 계정**. Python 스크립트와 입력 파일을 만들고 Azure 저장소에 업로드합니다. Spark 프로그램의 출력은 이 저장소 계정에 저장됩니다. 주문형 Spark 클러스터는 기본 저장소와 동일한 저장소 계정을 사용합니다.  
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침을 따르세요.


### <a name="upload-python-script-to-your-blob-storage-account"></a>Blob Storage 계정에 Python 스크립트 업로드
1. 다음 내용이 포함된 **WordCount_Spark.py**라는 Python 파일을 만듭니다. 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt;storageAccountName&gt;**을 Azure Storage 계정 이름으로 바꿉니다. 그런 다음 파일을 저장합니다. 
3. Azure Blob Storage에 아직 없는 경우 **adftutorial**이라는 컨테이너를 만듭니다. 
4. **spark**라는 폴더를 만듭니다.
5. **spark** 폴더 아래에 **script**라는 하위 폴더를 만듭니다. 
6. **script** 하위 폴더에 **WordCount_Spark.py** 파일을 업로드합니다. 


### <a name="upload-the-input-file"></a>입력 파일 업로드
1. 일부 텍스트가 포함된 **minecraftstory.txt**라는 파일을 만듭니다. Spark 프로그램은 이 텍스트의 단어 수를 계산합니다. 
2. `spark` 폴더에 `inputfiles`이라는 하위 폴더를 만듭니다. 
3. `inputfiles` 하위 폴더에 `minecraftstory.txt`를 업로드합니다. 

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![사용할 수 없는 이름 - 오류](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      이 빠른 시작의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup**이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Data Factory UI 응용 프로그램을 시작합니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
이 섹션에서는 두 개의 연결된 서비스를 작성합니다. 
    
- Azure Storage 계정을 데이터 팩터리에 연결하는 **Azure Storage 연결된 서비스**. 이 저장소는 주문형 HDInsight 클러스터에서 사용됩니다. 실행될 Spark 스크립트도 포함되어 있습니다. 
- **주문형 HDInsight 연결된 서비스**. Azure Data Factory는 HDInsight 클러스터를 자동으로 만들고, Spark 프로그램을 실행한 다음, 미리 구성된 시간 동안 유휴 상태가 되면 이 HDInsight 클러스터를 삭제합니다. 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

1. **시작** 페이지에서 다음 이미지와 같이 왼쪽 패널의 **편집** 탭으로 전환합니다. 

    ![파이프라인 만들기 타일](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. 창의 아래쪽에서 **연결**, **+ 새로 만들기**를 차례로 클릭합니다. 

    ![새 연결 단추](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. **새 연결된 서비스** 창에서 **Azure Blob Storage**를 선택하고 **계속**을 클릭합니다. 

    ![Azure Blob Storage 선택](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. **Azure Storage 계정 이름**을 선택하고 **저장**을 클릭합니다. 

    ![Blob 저장소 계정 지정](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>주문형 HDInsight 연결된 서비스 만들기

1. **+ 새로 만들기** 단추를 다시 클릭하여 또 하나의 연결된 서비스를 만듭니다. 
2. **새 연결된 서비스** 창에서 **Azure HDInsight**를 선택하고 **계속**을 클릭합니다. 

    ![Azure HDInsight 선택](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureHDInsightLinkedService**를 입력합니다.
    2. **유형**에 대해 **주문형 HDInsight**가 선택되었는지 확인합니다.
    3. **Azure Storage 연결된 서비스**에 대해 **AzureStorage1**을 선택합니다. 이 연결된 서비스는 앞에서 먼저 만들었습니다. 다른 이름을 사용한 경우 이 필드에 대한 올바른 이름을 지정합니다. 
    4. **클러스터 유형**에 대해 **spark**를 선택합니다.
    5. HDInsight 클러스터를 만들 수 있는 권한이 있는 **서비스 주체의 ID**를 입력합니다. 이 서비스 주체는 클러스터를 만든 구독 또는 리소스 그룹의 참가자 역할의 구성원이어야 합니다. 자세한 내용은 [Azure Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 참조하세요.
    6. **서비스 주체 키**를 입력합니다. 
    7. **리소스 그룹**에 대한 데이터 팩터리를 만들 때 사용한 것과 동일한 리소스 그룹을 선택합니다. Spark 클러스터가 이 리소스 그룹에 만들어집니다. 
    8. **OS 유형**을 펼칩니다.
    9. 클러스터 **사용자**에 대한 **이름**을 입력합니다. 
    10. 사용자에 대한 **암호**를 입력합니다. 
    11. **저장**을 클릭합니다. 

        ![HDInsight 연결된 서비스 설정](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight에는 지원하는 각 Azure 지역에서 사용할 수 있는 총 코어 수에 대한 제한이 있습니다. 주문형 HDInsight 연결된 서비스의 경우 HDInsight 클러스터는 기본 저장소로 사용되는 Azure Storage와 동일한 위치에 만들어집니다. 클러스터를 성공적으로 만드는 데 충분한 코어 할당량이 있는지 확인합니다. 자세한 내용은 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요. 

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

2. +(더하기) 단추를 클릭하고, 메뉴에서 **파이프라인**을 클릭합니다.

    ![새 파이프라인 메뉴](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. **활동** 도구 상자에서 **HDInsight**를 펼치고, **Spark** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

    ![Spark 활동 끌어서 놓기](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. 아래쪽의 **Spark** 활동 창에 대한 속성에서 다음 단계를 수행합니다. 

    1. **HDI 클러스터** 탭으로 전환합니다.
    2. 이전 단계에서 만든 **AzureHDInsightLinkedService**를 선택합니다. 
        
    ![HDInsight 연결된 서비스 지정](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. **스크립트/Jar** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **작업 연결된 서비스**에 대해 **AzureStorage1**을 선택합니다.
    2. **저장소 찾아보기**를 클릭합니다. 
    3. **adftutorial/spark/script 폴더**로 이동하여 **WordCount_Spark.py**를 선택하고 **마침**을 클릭합니다.      

    ![Spark 스크립트 지정](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. 파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사** 단추를 클릭합니다. **오른쪽 화살표**(>>) 단추를 클릭하여 유효성 검사 창을 닫습니다. 
    
    ![유효성 검사 단추](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. **게시**를 클릭합니다. Data Factory UI는 엔터티(연결된 서비스 및 파이프라인)를 Azure Data Factory 서비스에 게시합니다. 

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거
도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

![지금 트리거](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. **모니터** 탭으로 전환합니다. 파이프라인 실행이 표시되는지 확인합니다. Spark 클러스터를 만드는 데 약 20분이 걸립니다. 

    ![파이프라인 실행 모니터링](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. 주기적으로 **새로 고침**을 클릭하여 파이프라인 실행 상태를 확인합니다. 

    ![파이프라인 실행 상태](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. 파이프라인 실행과 연결된 활동 실행을 보려면 [작업]열에서 **활동 실행 보기** 작업을 클릭합니다. 위쪽의 **파이프라인** 링크를 클릭하여 파이프라인 실행 보기로 다시 전환할 수 있습니다.

    ![활동 실행 보기](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>출력 확인
출력 파일이 adftutorial 컨테이너의 spark/otuputfiles/wordcount 폴더에 만들어졌는지 확인합니다. 

![출력 확인](./media/tutorial-transform-data-spark-portal/verity-output.png)

파일에는 입력 텍스트 파일의 각 단어 및 해당 단어가 파일에 나타난 횟수가 있어야 합니다. 예:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인에서 Spark 활동 및 주문형 HDInsight 연결된 서비스를 사용하여 데이터를 변환합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * Spark 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다.

가상 네트워크에 있는 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
> [자습서: Azure Virtual Network에서 Hive를 사용하여 데이터 변환](tutorial-transform-data-hive-virtual-network-portal.md).





