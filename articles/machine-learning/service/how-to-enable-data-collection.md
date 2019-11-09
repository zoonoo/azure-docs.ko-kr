---
title: 프로덕션 모델에서 데이터 수집
titleSuffix: Azure Machine Learning
description: Azure Blob Storage에서 Azure Machine Learning 입력 모델 데이터를 수집하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 20bc148e392900aecb63ad393ec6e90cda65585a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839093"
---
# <a name="collect-data-for-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> 이 SDK는 곧 사용이 중지 됩니다. 이 SDK는 모델에서 데이터 드리프트를 모니터링 하는 개발자에 게 적합 하지만 대부분의 개발자는 [Application Insights로 간소화 된 데이터 모니터링](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)을 사용 해야 합니다. 

이 문서에서는 azure Kubernetes Cluster (AKS)에 배포한 Azure Machine Learning에서 Azure Blob 저장소로 입력 모델 데이터를 수집 하는 방법을 배울 수 있습니다. 

데이터를 수집하도록 설정하면 이렇게 수집한 데이터는 다음과 같은 용도로 활용할 수 있습니다.
* 프로덕션 데이터가 모델에 입력 되는 [데이터 상태가 모니터링](how-to-monitor-data-drift.md)

* 모델을 다시 학습시키거나 최적화할 시기를 더 효율적으로 결정

* 수집한 데이터로 모델 다시 학습시키기

## <a name="what-is-collected-and-where-does-it-go"></a>수집되는 데이터 및 데이터가 이동하는 위치

다음 데이터를 수집할 수 있습니다.
* AKS(Azure Kubernetes 클러스터)에 배포된 웹 서비스의 모델 **입력** 데이터(음성, 이미지 및 비디오는 수집되지 **않음**) 
  
* 프로덕션 입력 데이터를 사용 하 여 모델 예측

> [!Note]
> 이 데이터의 사전 집계 또는 사전 계산 기능은 현재 서비스에서 제공되지 않습니다.   

출력은 Azure Blob에 저장됩니다. 데이터가 Azure Blob에 추가되므로 원하는 도구를 선택하여 분석을 수행할 수 있습니다. 

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> `0.1.0a16` 하기 전의 SDK 버전에서 `designation` 인수의 이름은 `identifier`입니다. 코드가 이전 버전으로 개발 된 경우 적절 하 게 업데이트 해야 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험

- Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경을 구성 하는 방법](how-to-configure-environment.md) 문서를 사용 하 여 이러한 필수 구성 요소를 가져오는 방법을 알아봅니다.

- AKS(Azure Kubernetes Service)에 배포할 학습된 Machine Learning 모델. 없는 경우 [학습 이미지 분류 모델](tutorial-train-models-with-aml.md) 자습서를 참조 하세요.

- Azure Kubernetes Service 클러스터입니다. 를 만들고 배포 하는 방법에 대 한 자세한 내용은 [how to deploy and where](how-to-deploy-and-where.md) document 항목을 참조 하세요.

- [환경 설정](how-to-configure-environment.md) 및 [모니터링 SDK](https://aka.ms/aml-monitoring-sdk) 설치

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화
Azure Machine Learning 또는 다른 도구를 통해 배포 되는 모델에 관계 없이 데이터 수집을 사용 하도록 설정할 수 있습니다. 

데이터 수집을 사용하도록 설정하려면 다음 단계를 수행해야 합니다.

1. 점수 매기기 파일 열기

1. 파일 맨 위에 [다음 코드](https://aka.ms/aml-monitoring-sdk)를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId*는 선택적 매개 변수이므로 모델에 필요하지 않으면 설정하지 않아도 됩니다. correlationId가 있으면 다른 데이터와의 매핑을 더 쉽게 수행할 수 있습니다. LoanNumber, CustomerId 등을 예로 들 수 있습니다.
    
    *식별자* 는 나중에 Blob에서 폴더 구조를 작성 하는 데 사용 되며 "원시" 데이터와 "처리 된" 데이터를 구분 하는 데 사용할 수 있습니다.

3.  `run(input_df)` 함수에 다음 코드 줄을 추가합니다.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. 데이터 수집은 AKS에서 서비스를 배포할 때 **true**로 자동 설정되지 **않습니다** 따라서 다음과 같이 구성 파일을 업데이트해야 합니다. 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    이 구성을 변경하여 서비스 모니터링용 AppInsights도 설정할 수 있습니다.
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. 새 이미지를 만들고 서비스를 배포 하려면 [How to deploy and where](how-to-deploy-and-where.md) document 항목을 참조 하세요.


**환경 파일** 및 **점수 매기기 파일**의 종속성이 설치된 서비스가 이미 있으면 다음 단계를 수행하여 데이터 수집을 사용하도록 설정합니다.

1. [Azure Machine Learning studio](https://ml.azure.com) 로 이동

1. 작업 영역을 엽니다.

1. **배포** 로 이동 -> **서비스** -> **편집** 을 선택 합니다.

   ![서비스 편집](media/how-to-enable-data-collection/EditService.PNG)

1. **고급 설정**에서 **모델 데이터 수집 사용** 을 선택 합니다.

    [![데이터 수집 선택](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   이 창에서 "Appinsights 진단 사용"을 선택 하 여 서비스의 상태를 추적할 수도 있습니다.

1. **업데이트** 를 선택 하 여 변경 내용을 적용 합니다.


## <a name="disable-data-collection"></a>데이터 수집 비활성화
데이터 수집은 언제든지 중지할 수 있습니다. Python 코드 또는 Azure Machine Learning studio를 사용 하 여 데이터 수집을 사용 하지 않도록 설정 합니다.

+ 옵션 1-Azure Machine Learning studio에서 사용 안 함: 
  1. [Azure Machine Learning studio](https://ml.azure.com) 에 로그인

  1. 작업 영역을 엽니다.

  1. **배포** 로 이동 -> **서비스** -> **편집** 을 선택 합니다.

     [![편집 옵션](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. **고급 설정**에서 **모델 데이터 수집 사용** 을 선택 취소 합니다.

     [![데이터 수집 선택 취소](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. **업데이트** 를 선택 하 여 변경 내용을 적용 합니다.

  [Azure Machine Learning studio](https://ml.azure.com)의 작업 영역에서 이러한 설정에 액세스할 수도 있습니다.

+ 옵션 2 - Python을 사용하여 데이터 수집을 사용하지 않도록 설정

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>데이터 유효성 검사 및 분석
선호하는 도구를 선택하여 Azure Blob에 수집된 데이터를 분석할 수 있습니다.

Blob의 데이터에 빠르게 액세스하려면 다음을 수행합니다.

1. [Azure Machine Learning studio](https://ml.azure.com) 에 로그인

1. 작업 영역을 엽니다.
1. **저장소** 를 클릭 합니다.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob의 출력 데이터 경로는 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Power BI를 통해 모델 데이터 분석

1. [Power BI Desktop](https://www.powerbi.com) 다운로드 및 열기

1. **데이터 가져오기** 를 선택 하 고 [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources) 을 클릭 합니다.

    [![PBI Blob 설정](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. Blob의 **설정** 에서 > 액세스 키 >이 정보를 찾을 수 있습니다.

1. 컨테이너 **modeldata** 를 선택 하 고 **편집** 을 클릭 합니다.

    [![PBI 탐색기](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 “이름” 열 아래를 클릭하고 스토리지 계정 1을 추가합니다. 필터에 대한 경로를 모델링합니다. 참고: 특정 연도 또는 특정 월의 파일만 보려면 필터 경로를 확장하기만 하면 됩니다. 예를 들어 다음과 같이 3 월 데이터를 확인 합니다./modeldata/svv>/resourcegroupname >/workspacename >/webservicename >/modelname >/modelversion >/hv>/3

1. **이름**을 기준으로 관련된 데이터를 필터링합니다. **예측** 및 **입력**을 저장 한 경우 각각에 대해 쿼리를 만들어야 합니다.

1. **콘텐츠** 열을 제외 하 고 두 개의 이중 화살표를 클릭 하 여 파일을 결합 합니다.

    [![PBI 콘텐츠](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 확인을 클릭 하면 데이터가 미리 로드 됩니다.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 이제 **닫기 및 적용** 을 클릭할 수 있습니다.

1.  입력 및 예측을 추가한 경우 테이블은 **RequestId** 에 의해 자동으로 상호 연결 됩니다.

1. 모델 데이터에 대 한 사용자 지정 보고서 작성 시작


### <a name="analyzing-model-data-using-databricks"></a>Databricks를 사용하여 모델 데이터 분석

1. [Databricks 작업 영역](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 만들기

1. Databricks 작업 영역으로 이동

1. Databricks 작업 영역에서 **데이터 업로드** 를 선택 합니다.

    [![DB 업로드](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 새 테이블을 만들고 **다른 데이터 원본을** 선택 합니다.-> Azure Blob Storage-> 노트에서 테이블 만들기

    [![DB 테이블](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터 위치를 업데이트합니다. 다음은 예제입니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 데이터를 보고 분석 하기 위해 템플릿의 단계를 따르세요.

## <a name="example-notebook"></a>예제 Notebook

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Notebook은 이 문서의 개념을 보여 줍니다.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
