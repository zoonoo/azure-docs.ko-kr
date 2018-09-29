---
title: 프로덕션 환경에서 모델에 대해 데이터 컬렉션 사용 - Azure Machine Learning
description: Azure Blob Storage에서 Azure Machine Learning 입력 모델 데이터를 수집하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 412871c0c692f60e690f61fa4e6f67f836cd3ef7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158210"
---
# <a name="collect-data-for-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집

이 문서에서는 AKS(Azure Kubernetes 클러스터)에 배포한 Azure Machine Learning Services에서 Azure Blob Storage로 입력 모델 데이터를 수집하는 방법을 확인할 수 있습니다. 

데이터를 수집하도록 설정하면 이렇게 수집한 데이터는 다음과 같은 용도로 활용할 수 있습니다.
* 프로덕션 데이터가 모델에 들어올 때 데이터 드리프트 모니터링

* 모델을 다시 학습시키거나 최적화할 시기를 더 효율적으로 결정

* 수집한 데이터로 모델 다시 학습시키기

## <a name="what-is-collected-and-where-does-it-go"></a>수집되는 데이터 및 데이터가 이동하는 위치

다음 데이터를 수집할 수 있습니다.
* AKS(Azure Kubernetes 클러스터)에 배포된 웹 서비스의 모델 **입력** 데이터(음성, 이미지 및 비디오는 수집되지 **않음**) 
  
* 프로덕션 입력 데이터를 사용하는 모델 예측

> [!Note]
> 이 데이터의 사전 집계 또는 사전 계산 기능은 현재 서비스에서 제공되지 않습니다.   

출력은 Azure Blob에 저장됩니다. 데이터가 Azure Blob에 추가되므로 원하는 도구를 선택하여 분석을 수행할 수 있습니다. 

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.

- AKS(Azure Kubernetes Service)에 배포할 학습된 Machine Learning 모델. 이러한 모델이 없으면 [이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

- [AKS 클러스터](how-to-deploy-to-aks.md)

- [사용 중인 환경](how-to-configure-environment.md)에 설치된 다음 종속성과 모듈
  + Linux에서:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Windows에서:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화
Azure Machine Learning 서비스 또는 기타 도구를 통해 모듈을 배포하는지에 관계없이 데이터 수집을 사용하도록 설정할 수 있습니다. 

데이터 수집을 사용하도록 설정하려면 다음 단계를 수행해야 합니다.

1. 점수 매기기 파일을 엽니다. 

1. 파일 맨 위에 다음 코드를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId*는 선택적 매개 변수이므로 모델에 필요하지 않으면 설정하지 않아도 됩니다. correlationId가 있으면 다른 데이터와의 매핑을 더 쉽게 수행할 수 있습니다. LoanNumber, CustomerId 등을 예로 들 수 있습니다.
    
    *Identifier*는 나중에 Blob에서 폴더 구조를 작성하는 데 사용되며 “raw” 데이터와 “processed” 데이터를 분리하는 데 사용할 수 있습니다.

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

5. [새 이미지를 만들고 서비스를 배포합니다.](how-to-deploy-to-aks.md) 


**환경 파일** 및 **점수 매기기 파일**의 종속성이 설치된 서비스가 이미 있으면 다음 단계를 수행하여 데이터 수집을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 작업 영역을 엽니다.

1. **배포** -> **서비스 선택** -> **편집**으로 이동합니다.

   ![서비스 편집](media/how-to-enable-data-collection/EditService.png)

1. **고급 설정**에서 **모델 데이터 컬렉션 사용** 선택을 취소합니다. 

   ![데이터 수집 선택 취소](media/how-to-enable-data-collection/CheckDataCollection.png)

   이 창에서 “Appinsights 진단 사용”을 선택하여 서비스의 상태를 추적할 수도 있습니다.  

1. **업데이트**를 선택하여 변경 내용을 적용합니다.


## <a name="disable-data-collection"></a>데이터 수집 비활성화
데이터 수집은 언제든지 중지할 수 있습니다. 데이터 수집을 사용하지 않도록 설정하려면 Python 코드 또는 Azure Portal을 사용합니다.

+ 옵션 1 - Azure Portal에서 데이터 수집을 사용하지 않도록 설정 
  1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

  1. 작업 영역을 엽니다.

  1. **배포** -> **서비스 선택** -> **편집**으로 이동합니다.

     ![서비스 편집](media/how-to-enable-data-collection/EditService.png)

  1. **고급 설정**에서 **모델 데이터 컬렉션 사용** 선택을 취소합니다. 

     ![데이터 수집 선택 취소](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. **업데이트**를 선택하여 변경 내용을 적용합니다.

* 옵션 2 - Python을 사용하여 데이터 수집을 사용하지 않도록 설정

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>예제 Notebook

이 문서에 나와 있는 개념은 `00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` Notebook에서 확인할 수 있습니다.  

이 Notebook을 다운로드하려면 다음 단계를 수행합니다.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]