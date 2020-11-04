---
title: 프로덕션 모델에서 데이터 수집
titleSuffix: Azure Machine Learning
description: 배포 된 Azure Machine Learning 모델에서 데이터를 수집 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: be2afad7e7cb08d9c677e589846f3d67cf43e708
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314231"
---
# <a name="collect-data-from-models-in-production"></a>프로덕션 환경에서 모델의 데이터 수집



이 문서에서는 AKS (Azure Kubernetes Service) 클러스터에 배포 된 Azure Machine Learning 모델에서 데이터를 수집 하는 방법을 보여 줍니다. 수집 된 데이터는 Azure Blob 저장소에 저장 됩니다.

수집을 사용 하도록 설정 하면 수집 하는 데이터를 통해 다음 작업을 수행할 수 있습니다.

* 수집한 프로덕션 데이터의 [데이터 상태가를 모니터링](how-to-monitor-datasets.md) 합니다.

* [Power BI](#powerbi) 또는 [Azure Databricks](#databricks) 를 사용 하 여 수집 된 데이터 분석

* 모델을 다시 학습 또는 최적화 하는 시기에 대 한 더 나은 결정을 내립니다.

* 수집 된 데이터를 사용 하 여 모델을 다시 학습 합니다.

## <a name="what-is-collected-and-where-it-goes"></a>수집 되는 내용 및 진행 위치

다음 데이터를 수집할 수 있습니다.

* AKS 클러스터에 배포 된 웹 서비스의 입력 데이터를 모델링 합니다. 음성 오디오, 이미지 및 비디오는 수집 *되지 않습니다* .
  
* 프로덕션 입력 데이터를 사용하는 모델 예측

>[!NOTE]
> 이 데이터에 대 한 Preaggregation 및 precalculations는 현재 수집 서비스의 일부가 아닙니다.

출력은 Blob 저장소에 저장 됩니다. 데이터가 Blob 저장소에 추가 되기 때문에 원하는 도구를 선택 하 여 분석을 실행할 수 있습니다.

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 0.1.0 a16 이전 버전의 Python 용 Azure Machine Learning SDK 버전에서는 `designation` 인수의 이름이로 지정 됩니다 `identifier` . 이전 버전을 사용 하 여 코드를 개발한 경우 적절 하 게 업데이트 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.

- Azure Machine Learning 작업 영역, 스크립트를 포함 하는 로컬 디렉터리 및 Python 용 Azure Machine Learning SDK가 설치 되어 있어야 합니다. 이를 설치 하는 방법에 대 한 자세한 내용은 [개발 환경을 구성](how-to-configure-environment.md)하는 방법을 참조 하세요.

- AKS에 배포 하려면 학습 된 기계 학습 모델을 만들어야 합니다. 모델이 없는 경우 [이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조 하세요.

- AKS 클러스터가 필요 합니다. 하나를 만들어 배포 하는 방법에 대 한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- [환경을 설정](how-to-configure-environment.md) 하 고 [Azure Machine Learning 모니터링 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)를 설치 합니다.

## <a name="enable-data-collection"></a>데이터 수집 사용

Azure Machine Learning 또는 다른 도구를 통해 배포 하는 모델에 관계 없이 [데이터 수집](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector?preserve-view=true&view=azure-ml-py) 을 사용 하도록 설정할 수 있습니다.

데이터 수집을 사용 하도록 설정 하려면 다음을 수행 해야 합니다.

1. 점수 매기기 파일을 엽니다.

1. 파일 맨 위에 다음 코드를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. `init` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* 는 선택적 매개 변수입니다. 모델에 필요 하지 않은 경우에는 사용할 필요가 없습니다. *CorrelationId* 를 사용 하면 *LoanNumber* 나 *CustomerId* 와 같은 다른 데이터를 보다 쉽게 매핑할 수 있습니다.
    
    *식별자* 매개 변수는 나중에 blob에서 폴더 구조를 작성 하는 데 사용 됩니다. 이를 사용 하 여 원시 데이터를 처리 된 데이터와 구분할 수 있습니다.

1. `run(input_df)` 함수에 다음 코드 줄을 추가합니다.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. AKS에 서비스를 배포 하는 경우 데이터 컬렉션은 자동으로 **true** 로 설정 *되지 않습니다* . 다음 예제와 같이 구성 파일을 업데이트 합니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    이 구성을 변경 하 여 서비스 모니터링에 대 한 Application Insights를 사용 하도록 설정할 수도 있습니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 새 이미지를 만들고 machine learning 모델을 배포 하려면 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.


## <a name="disable-data-collection"></a>데이터 수집 비활성화

언제 든 지 데이터 수집을 중지할 수 있습니다. Python 코드를 사용 하 여 데이터 수집을 사용 하지 않도록 설정 합니다.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>데이터 유효성 검사 및 분석

기본 설정 도구를 선택 하 여 Blob 저장소에 수집 된 데이터를 분석할 수 있습니다.

### <a name="quickly-access-your-blob-data"></a>Blob 데이터에 빠르게 액세스

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 작업 영역을 엽니다.

1. **스토리지** 를 선택합니다.

    [![저장소 옵션을 선택 합니다.](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 다음 구문을 사용 하 여 blob의 출력 데이터에 대 한 경로를 따릅니다.

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Power BI를 사용 하 여 모델 데이터 분석

1. [Power BI Desktop](https://www.powerbi.com)를 다운로드 하 여 엽니다.

1. **데이터 가져오기** 를 선택 하 고 [**Azure Blob Storage**](/power-bi/desktop-data-sources)를 선택 합니다.

    [![Power BI blob 설정](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. Blob에서 **설정**  >  **액세스 키** 를 선택 하 여이 정보를 찾을 수 있습니다.

1. **모델 데이터** 컨테이너를 선택 하 고 **편집** 을 선택 합니다.

    [![Power BI 탐색기](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 **이름** 열 아래를 클릭 하 고 저장소 계정을 추가 합니다.

1. 필터에 모델 경로를 입력 합니다. 특정 연도나 월의 파일만 확인 하려면 필터 경로를 확장 합니다. 예를 들어 3 월 데이터만 보려면 다음 필터 경로를 사용 합니다.

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. **이름** 값에 따라 사용자와 관련 된 데이터를 필터링 합니다. 예측 및 입력을 저장 한 경우 각각에 대 한 쿼리를 만들어야 합니다.

1. **콘텐츠** 열 제목 옆에 있는 하향 이중 화살표를 선택 하 여 파일을 결합 합니다.

    [![Power BI 콘텐츠](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. **확인** 을 선택합니다. 데이터 로드입니다.

    [![파일 결합 Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **닫기 및 적용** 을 선택 합니다.

1. 입력 및 예측을 추가한 경우 테이블은 **RequestId** 값을 기준으로 자동으로 정렬 됩니다.

1. 모델 데이터에 대한 사용자 지정 보고서 빌드를 시작합니다.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Azure Databricks를 사용 하 여 모델 데이터 분석

1. [Azure Databricks 작업 영역](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)을 만듭니다.

1. Databricks 작업 영역으로 이동합니다.

1. Databricks 작업 영역에서 **데이터 업로드** 를 선택 합니다.

    [![Databricks 데이터 업로드 옵션 선택](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. **새 테이블 만들기** 를 선택 하 고 **다른 데이터 원본** 을 선택 하  >  **Azure Blob Storage**  >  **노트북에서 테이블 만들기** 를 선택 합니다.

    [![Databricks 테이블 만들기](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터의 위치를 업데이트 합니다. 다음은 예제입니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks 설정](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 템플릿의 단계를 따라 데이터를 보고 분석 합니다.

## <a name="next-steps"></a>다음 단계

수집한 데이터에 대 한 [데이터 드리프트를 검색](how-to-monitor-datasets.md) 합니다.