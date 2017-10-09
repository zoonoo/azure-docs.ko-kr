---
title: "Azure Machine Learning 서비스(미리 보기) 모델 배포 | Microsoft Docs"
description: "이 자습서 전체에서 Azure Machine Learning 서비스(미리 보기)를 사용하는 방법을 보여 줍니다. 이는 제3부 모델 배포입니다."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0dfcc965d96949527b3e80285061bff320872621
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-3-deploy-a-model"></a>Iris 분류 3부: 모델 배포
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서는 3부로 구성된 시리즈 중 제3부입니다. 이 2부 자습서에서는 Azure Machine Learning 서비스(미리 보기)를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 모델 파일 찾기
> * 점수 매기기 스크립트 및 스키마 파일 생성
> * 환경 준비
> * 실시간 웹 서비스 만들기
> * 실시간 웹 서비스 실행
> * 출력 Blob 데이터 검사 

 이 자습서에서는 작업을 단순하게 유지하기 위해 변함 없는 [Iris 꽃 데이터 집합](https://en.wikipedia.org/wiki/iris_flower_data_set)을 사용합니다. 스크린샷은 Windows 전용이지만 macOS 환경에서도 거의 동일합니다.

## <a name="prerequisites"></a>필수 조건
다음과 같이 이 자습서 시리즈의 제1부 및 제2부를 완료해야 합니다.
- [데이터 준비 자습서](tutorial-classifying-iris-part-1.md)에 따라 Azure Machine Learning 리소스를 만들고 Azure Machine Learning Workbench 응용 프로그램을 설치합니다.
- 다음으로 [모델 작성 자습서](tutorial-classifying-iris-part-2.md)에 따라 Azure Machine Learning에서 로지스틱 회귀 모델을 만듭니다.

## <a name="download-the-model-pickle-file"></a>모델 pickle 파일 다운로드
이 자습서 시리즈의 제2부에서는 `iris_sklearn.py` 스크립트가 Azure Machine Learning Workbench에서 로컬로 실행되었습니다. 이 작업은 인기 있는 **[pickle](https://docs.python.org/2/library/pickle.html)** Python 개체 직렬화 패키지를 사용하여 로지스틱 회귀 모델을 직렬화했습니다. 

1. **Azure Machine Learning Workbench** 응용 프로그램을 시작하고, 자습서 시리즈의 1부에서 만든 **myIris** 프로젝트를 엽니다.

2. 프로젝트가 열리면 Azure Machine Learning Workbench의 왼쪽 도구 모음에 있는 **파일** 단추(폴더 아이콘)를 클릭하여 프로젝트 폴더의 파일 목록을 엽니다.

3. **iris_sklearn.py** 파일을 선택하면 Python 코드가 Workbench의 새 텍스트 편집기 탭에서 열립니다.

4. **iris_sklearn.py** 파일을 검토하여 pickle 파일이 생성된 위치를 확인합니다. Ctrl+F를 사용하여 찾기 대화 상자를 연 다음 Python 코드에서 **pickle**이라는 단어를 찾습니다.

   이 코드 조각에서는 pickle 처리된 출력 파일이 생성된 방법을 보여 줍니다. 디스크에서 출력 pickle 파일의 이름은 `model.pkl`입니다. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 이전 실행의 출력 파일에서 모델 pickle 파일을 찾습니다.
   
   **iris_sklearn.py** 스크립트를 실행한 경우 모델 파일은 `outputs` 폴더에 `model.pkl` 이름으로 기록되어 있습니다. 이 폴더는 로컬 프로젝트 폴더가 아니라 스크립트를 실행하도록 선택한 실행 환경에 있습니다. 
   
   - 파일을 찾으려면 Azure Machine Learning Workbench 응용 프로그램을 사용하고, 왼쪽 도구 모음에서 **실행** 단추(시계 아이콘)를 클릭하여 **모든 실행**의 목록을 엽니다.  
   - **모든 실행** 탭이 열립니다. 실행 테이블에서 대상이 **로컬**이고 스크립트 이름이 **iris_sklearn.py**인 최근 실행 중 하나를 선택합니다. 
   - **실행 속성** 페이지가 열립니다. 페이지의 오른쪽 위에서 **출력** 섹션을 확인합니다. 
   - **model.pkl** 파일 옆에 있는 확인란을 선택하고 **다운로드** 단추를 클릭하여 pickle 파일을 다운로드합니다. 프로젝트 폴더의 루트에 저장합니다. 이는 이후 단계에서 필요합니다.

   ![pickle 파일 다운로드](media/tutorial-classifying-iris/download_model.png)

   [큰 데이터 파일을 읽고 쓰는 방법](how-to-read-write-files.md) 문서에서 `outputs` 폴더에 대해 자세히 알아보세요.

## <a name="get-scoring-and-schema-files"></a>점수 매기기 및 스키마 파일 가져오기
모델 파일과 함께 웹 서비스를 배포하려면 점수 매기기 스크립트와 필요에 따라 웹 서비스 입력 데이터에 대한 스키마가 필요합니다. 점수 매기기 스크립트는 현재 폴더에서 `model.pkl` 파일을 로드하고, 이를 사용하여 새로 예측되는 Iris 클래스를 생성합니다.  

1. **Azure Machine Learning Workbench** 응용 프로그램을 시작하고, 자습서 시리즈의 1부에서 만든 **myIris** 프로젝트를 엽니다.

2. 프로젝트가 열리면 Azure Machine Learning Workbench의 왼쪽 도구 모음에 있는 **파일** 단추(폴더 아이콘)를 클릭하여 프로젝트 폴더의 파일 목록을 엽니다.

3. **iris_score.py** 파일을 선택합니다. Python 스크립트가 열립니다. 이 파일은 점수 매기기 파일로 사용됩니다.

4. 스키마 파일을 가져오려면 스크립트를 실행합니다. 명령 모음에서 **로컬** 환경과 **iris_score.py** 스크립트를 선택한 다음 **실행** 단추를 클릭합니다. 

5. 이 스크립트는 모델에 필요한 입력 데이터 스키마를 캡처하는 **outputs** 폴더에 JSON 파일을 만듭니다.

   ![점수 매기기 파일](media/tutorial-classifying-iris/model_data_collection.png)

6. Machine Learning Workbench 창의 오른쪽에 있는 [작업] 창에서 최신 **iris_score.py** 작업에 녹색의 **완료됨** 상태가 표시될 때까지 기다립니다. 그런 다음 최신 작업 실행에 대한 **iris_score.py[1]** 하이퍼링크를 클릭하여 **iris_score.py**의 실행 세부 정보를 봅니다. 

7. [실행 속성] 페이지의 **출력** 섹션에서 새로 만든 **service_schema.json** 파일을 선택합니다. 파일을 프로젝트 루트 폴더에 저장합니다.

8. **iris_score.py** 스크립트를 연 탭으로 돌아갑니다. 

   웹 서비스에서 모델 입력 및 예측을 캡처할 수 있는 데이터 수집을 사용하는 방법에 유의하세요. 다음 사항은 데이터 수집에 특히 중요합니다.

9. 모델 데이터 수집 기능이 포함된 ModelDataCollector 파일 가져오기 클래스의 맨 위에 있는 코드를 검토합니다.

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. `init()` 함수에서 ModelDataCollector를 인스턴스화하는 다음 코드 줄을 검토합니다.

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. `run(input_df)` 함수에서 입력 및 예측 데이터를 수집하는 다음 코드 줄을 검토합니다.

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

이제 모델을 조작할 환경이 준비되었습니다.

## <a name="prepare-to-operationalize-locally"></a>로컬에서 조작 준비
_로컬 모드_ 배포를 사용하여 로컬 컴퓨터의 Docker 컨테이너에서 실행합니다.

개발 및 테스트에는 _로컬 모드_를 사용할 수 있습니다. 다음 단계를 수행하여 모델을 조작하려면 Docker 엔진이 로컬로 실행되어야 합니다. 명령 끝에 `-h` 플래그를 사용하여 명령 도움말을 얻을 수 있습니다.

>[!NOTE]
>Docker 엔진을 로컬에 설치하지 않았다면 Azure에서 배포용 클러스터를 만들어 계속 진행할 수 있습니다. 요금이 지속적으로 발생하지 않도록 자습서 완료 후에 해당 클러스터를 반드시 삭제해야 합니다.

1. Azure Machine Learning Workbench에서 명령줄 인터페이스를 열고, [파일] 메뉴에서 **명령 프롬프트 열기**를 클릭합니다.

   명령줄 프롬프트가 현재 프로젝트 폴더 위치인 `c:\temp\myIris>`에서 열립니다.

2. `Microsoft.ContainerRegistry` Azure 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 3단계에서 환경을 만들려면 먼저 이 리소스 공급자를 등록해야 합니다. 다음 명령을 사용하여 이미 등록되어 있는지 확인할 수 있습니다.
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   다음과 유사한 결과가 표시됩니다. 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   `Microsoft.ContainerRegistry`가 등록되어 있지 않으면 다음 명령을 사용하여 등록할 수 있습니다.
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   등록하는 데 몇 분이 걸릴 수 있으며 위의 `az provider list` 명령 또는 다음 명령을 사용하여 상태를 확인할 수 있습니다.
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

3. 환경을 만듭니다. 이 단계는 환경별(예: dev 또는 prod)로 한 번만 실행해야 합니다. 첫 번째 환경에는 _로컬 모드_를 사용합니다. (나중에 다음 명령에서 `-c` 또는 `--cluster` 스위치를 사용하여 환경을 _클러스터 모드_로 설정할 수 있습니다.)

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   화면의 지침에 따라 Docker 이미지 저장을 위한 저장소 계정, Docker 이미지 나열을 위한 ACR(Azure Container Registry), 원격 분석 수집을 위한 AppInsight 계정을 프로비전합니다. `-c` 스위치를 사용한 경우 ACS(Azure Container Service) 클러스터도 만들어집니다.
   
   클러스터 이름은 환경을 식별할 수 있는 방법이며, 위치는 Azure Portal에서 만든 모델 관리 계정의 위치와 동일해야 합니다.

4. 모델 관리 계정을 만듭니다(이 경우 한 번만 설정).  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. 모델 관리 계정을 설정합니다.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. 환경을 설정합니다.
설정이 완료되면 다음 명령을 사용하여 조작에 필요한 환경 변수를 설정합니다. 환경 이름은 위의 1단계에서 사용된 이름입니다. 리소스 그룹 이름은 동일한 프로세스의 출력이며, 설정 프로세스가 완료되면 명령 창에 표시됩니다.
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

   로컬 웹 서비스 배포를 위한 작업 환경을 제대로 구성했는지 확인하려면 다음 명령을 입력합니다.

   ```azurecli
   az ml env show
   ```

이제 실시간 웹 서비스를 만들 준비가 되었습니다.

## <a name="create-a-real-time-web-service"></a>실시간 웹 서비스 만들기
다음 명령을 사용하여 실시간 웹 서비스를 만듭니다.

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   그러면 나중에 사용할 수 있는 웹 서비스 ID가 생성됩니다.

   다음은 `az ml service create realtime` 명령에 대한 스위치입니다.
   * -n: 응용 프로그램 이름이며, 모두 소문자여야 합니다.
   * -f: 점수 매기기 스크립트 파일 이름입니다.
   * --model-file: 모델 파일이며, 이 경우 pickle 처리된 model.pkl 파일입니다.
   * -r: 모델 유형이며, 이 경우 Python 모델입니다.
   * --collect-model-data true: 데이터 수집을 사용하도록 설정합니다.

   >[!IMPORTANT]
   >서비스 이름(새 Docker 이미지 이름이기도 함)은 모두 소문자여야 합니다. 그렇지 않으면 오류가 발생합니다. 

   명령을 실행하면 위의 환경 설정의 일부로 만든 저장소 계정에 모델과 점수 매기기 파일이 업로드됩니다. 배포 프로세스에서는 모델, 스키마, 점수 매기기 파일이 포함된 Docker 이미지를 빌드하고, 이를 `<ACR_name>.azureacr.io/<imagename>:<version>` ACR 레지스트리에 푸시합니다. 그런 다음 해당 이미지를 로컬로 컴퓨터에 가져와 해당 이미지를 기반으로 하여 Docker 컨테이너를 시작합니다. (환경이 클러스터 모드로 구성된 경우 Docker 컨테이너는 ACS Kubernetes 클러스터에 대신 배포됩니다.)

   배포의 일부로 로컬 컴퓨터에 웹 서비스에 대한 HTTP REST 끝점이 만들어집니다. 몇 분 후에 명령이 성공 메시지와 함께 완료되어야 하고, 이 경우 웹 서비스가 작동할 준비가 됩니다!

   `docker ps` 명령을 사용하여 실행 중인 Docker 컨테이너를 볼 수 있습니다.
   ```azurecli
   docker ps
   ```
### <a name="alternative-route"></a>대체 경로
위에서 보여 주는 `az ml service create realtime` 명령에 대한 대체 방안으로, 모델 등록, 매니페스트 생성, Docker 이미지 빌드 및 웹 서비스 만들기 단계를 개별적으로 수행할 수도 있습니다. 이렇게 하면 각 단계마다 더 많은 유연성이 제공되어 이전 단계에서 생성한 엔터티를 다시 사용하고 필요한 경우에만 엔터티를 다시 작성할 수 있습니다. 다음 지침에 따라 이를 수행할 수 있는 방법을 알아보세요.

1. pickle 파일 이름을 제공하여 모델을 등록합니다.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   이 경우 모델 ID가 생성됩니다.

2. 매니페스트 만들기

   매니페스트를 만들려면 다음 명령을 사용하고, 이전 단계의 모델 ID 출력을 제공합니다.

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   이 경우 매니페스트 ID가 생성됩니다.

3. Docker 이미지 만들기

   Docker 이미지를 만들려면 다음 명령을 사용하고 이전 단계에서 출력한 매니페스트 ID 값을 제공합니다.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   이 경우 Docker 이미지 ID가 생성됩니다.
   
4. 서비스 만들기

   서비스를 만들려면 나열된 명령을 사용하고, 이전 단계에 출력한 이미지 ID를 제공합니다.

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   이 경우 웹 서비스 ID가 생성됩니다.

이제 웹 서비스를 실행할 준비가 되었습니다.

## <a name="run-the-real-time-web-service"></a>실시간 웹 서비스 실행

4개의 난수가 있는 배열을 포함한 JSON 인코딩 레코드를 제공하여 실행 중인 `irisapp` 웹 서비스를 테스트합니다.

1. 웹 서비스를 만들 때 샘플 데이터가 포함되었습니다. 로컬 모드에서 실행할 때 `az ml service show realtime` 명령을 호출하여 서비스를 테스트하는 데 사용할 수 있는 샘플 실행 명령을 검색할 수 있습니다. 또한 고유한 사용자 지정 응용 프로그램에 서비스를 통합하는 데 사용할 수 있는 점수 매기기 URL도 제공합니다.

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. 서비스를 테스트하려면 반환된 서비스 실행 명령을 실행합니다.

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}
   ```
   출력은 예상된 클래스인 `"2"`입니다. (결과가 다를 수도 있습니다.) 

3. CLI 외부에서 서비스를 실행하려면 인증용 키를 얻어야 합니다.

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Azure Blob 저장소에서 수집된 데이터 보기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정을 찾습니다. 이렇게 하려면 **더 많은 서비스**를 클릭합니다.

3. 검색 상자에서 **저장소 계정**을 입력하고 **Enter** 키를 누릅니다.

4. **저장소 계정** 검색 페이지에서 환경에 맞는 **저장소 계정** 리소스를 선택합니다. 

   > [!TIP]
   > 사용할 저장소 계정을 확인하려면 Azure Machine Learning Workbench를 열고, 작업 중인 프로젝트를 선택하고, **파일** 메뉴에서 명령줄 프롬프트를 엽니다. 이때 명령줄 프롬프트에서 `az ml env show -v`를 입력하고, *storage_account* 값을 확인합니다. 이는 저장소 계정의 이름입니다.

5. **저장소 계정** 페이지가 열리면 왼쪽 목록에서 **컨테이너** 항목을 클릭합니다. **modeldata**라는 컨테이너를 찾습니다. 
 
   데이터가 표시되지 않을 경우 첫 번째 웹 서비스 요청 후 최대 10분 동안 기다려야 데이터가 저장 계정으로 전파되기 시작한 것을 확인할 수 있습니다.

   데이터는 다음 컨테이너 경로를 사용하여 Blob으로 흐릅니다.

   `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

6. Microsoft 소프트웨어 및 오픈 소스 도구를 사용하여 다양한 방법으로 Azure Blob에서 이 데이터를 사용할 수 있습니다. 

   예제에서 출력 Blob을 사용하는 방법:
   - Azure ML Workbench: Azure ML Workbench에서 csv 파일을 데이터 원본으로 추가하여 csv 파일을 엽니다. 
   - Excel: 일별 csv 파일을 스프레드시트로 엽니다.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): Blob의 csv 데이터에서 가져온 데이터로 차트를 만듭니다.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): csv 데이터의 큰 부분으로 데이터 프레임을 만듭니다.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): csv 데이터를 Hive 테이블에 로드하고, Blob에 대해 SQL 쿼리를 직접 수행합니다.

## <a name="next-steps"></a>다음 단계
3부 자습서 시리즈의 제3부에서는 Azure Machine Learning 서비스를 사용하여 다음을 수행하는 방법을 알아보았습니다.
> [!div class="checklist"]
> * 모델 파일 찾기
> * 점수 매기기 스크립트 및 스키마 파일 생성
> * 환경 준비
> * 실시간 웹 서비스 만들기
> * 실시간 웹 서비스 실행
> * 출력 Blob 데이터 검사 

Docker 기반 웹 서비스를 통해 다양한 계산 환경에서 성공적으로 학습 스크립트를 실행하고, 모델을 만들고 직렬화하고 조작했습니다. 

고급 데이터 준비를 수행할 준비가 되었습니다.
> [!div class="nextstepaction"]
> [고급 데이터 준비](tutorial-bikeshare-dataprep.md)


