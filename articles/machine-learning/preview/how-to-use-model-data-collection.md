---
title: "Azure Machine Learning의 모델 관리 데이터 컬렉션 기능을 사용하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Machine Learning Workbench의 모델 관리 데이터 컬렉션 기능을 사용하는 방법에 대해 설명합니다."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-collect-model-data-using-data-collection"></a>데이터 컬렉션을 사용하여 모델 데이터를 수집하는 방법

모델 데이터 컬렉션 기능을 사용하면 Machine Learning 웹 서비스의 모델 입력 및 예측을 보관할 수 있습니다. 이 문서에서는 모델 데이터 컬렉션의 다음과 같은 측면에 대해 설명합니다.

- 데이터 컬렉션 패키지를 설치하는 방법
- 데이터 컬렉션을 사용하는 방법
- 수집된 데이터를 보고 사용하는 방법

## <a name="how-to-install-the-data-collection-package"></a>데이터 컬렉션 패키지를 설치하는 방법
기본적으로 Linux 및 Windows에 데이터 컬렉션 라이브러리를 설치할 수 있습니다.

### <a name="windows"></a>Windows
Windows에서는 다음 명령을 사용하여 데이터 수집기 모듈을 설치할 수 있습니다.

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linux에서는 libxml++ 라이브러리를 먼저 설치해야 합니다. 다음 명령을 실행합니다. 이 명령은 sudo에서 실행해야 합니다.

    sudo apt-get install libxml++2.6-2v5

그런 후 다음 명령을 실행합니다.

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>데이터 컬렉션을 사용하는 방법

모델 데이터 컬렉션을 사용하려면 점수 매기기 파일을 다음과 같이 변경해야 합니다.

1. 파일 맨 위에 다음 코드를 추가합니다.
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. `init()` 함수에 다음 코드 줄을 추가합니다.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. `run(input_df)` 함수에 다음 코드 줄을 추가합니다.
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    `input_df` 및 `pred` 변수(`model.predict()`의 예측 값)를 초기화한 후 `collect()` 함수를 호출해야 합니다.

4. `--collect-model-data true` 스위치에 `az ml service create realtime` 명령을 사용하여 실시간 웹 서비스를 만듭니다. 이렇게 하면 서비스가 실행되는 동안 모델 데이터가 수집됩니다.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. 데이터 컬렉션을 테스트하려면 `az ml service run realtime` 명령을 실행합니다.

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>수집된 데이터를 보고 사용하는 방법
blob 저장소에 수집된 데이터를 보려면:

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **더 많은 서비스**를 클릭합니다.
3. 검색 상자에 `Storage accounts`를 입력하고 **ENTER** 키를 누릅니다.
4. **저장소 계정** 검색 블레이드에서 **저장소 계정** 리소스를 선택합니다. 저장소 계정을 확인하기 위해 다음 단계를 사용합니다.

    a. Azure ML Workbench로 이동하여 작업 중인 프로젝트를 선택하고, **파일** 메뉴에서 명령줄 프롬프트를 엽니다.
    
    b. `az ml env show -v`를 입력하고 *storage_account* 값을 선택합니다. 이것이 저장소 계정의 이름입니다.

5. 리소스 블레이드 메뉴에서 **컨테이너**를 클릭한 다음 **modeldata**라는 컨테이너를 클릭합니다. 데이터가 저장소 계정에 전파되는 것을 보려면 첫 번째 웹 서비스 요청 후 10분 정도 기다려야 할 수 있습니다. 데이터는 다음 컨테이너 경로를 통해 blob으로 흐릅니다.

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Microsoft 소프트웨어 및 오픈 소스 도구를 사용하여 다양한 방법으로 Azure blob에서 데이터를 사용할 수 있습니다. 다음은 몇 가지 예입니다.
 - Azure ML Workbench - csv 파일을 데이터 원본으로 추가하여 Azure ML Workbench에서 csv 파일 열기
 - Excel - 일별 csv 파일을 스프레드시트로 열기
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) - blob의 csv 데이터에서 가져온 데이터로 차트 만들기
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview) - csv 데이터의 많은 부분을 사용하여 데이터 프레임 만들기
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) - Hive 테이블에 csv 데이터를 로드하고 blob에 대해 직접 SQL 쿼리 수행

