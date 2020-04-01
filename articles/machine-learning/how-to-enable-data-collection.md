---
title: 프로덕션 모델에서 데이터 수집
titleSuffix: Azure Machine Learning
description: Azure Blob 저장소에서 Azure 기계 학습 입력 모델 데이터를 수집하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475435"
---
# <a name="collect-data-for-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Azure 기계 학습 모니터링 SDK는 곧 사용 중지됩니다. SDK는 현재 SDK를 사용하여 모델의 데이터 드리프트를 모니터링하는 개발자에게 여전히 적합합니다. 그러나 신규 고객의 경우 응용 [프로그램 인사이트를](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)사용하여 간소화된 데이터 모니터링을 사용하는 것이 좋습니다.

이 문서에서는 Azure 기계 학습에서 입력 모델 데이터를 수집하는 방법을 보여 주며 있습니다. 또한 입력 데이터를 AKS(Azure Kubernetes Service) 클러스터에 배포하고 출력 데이터를 Azure Blob 저장소에 저장하는 방법을 보여 주기도 합니다.

수집이 활성화되면 수집한 데이터가 다음과 같은 데 도움이 됩니다.

* 생산 데이터가 모델에 들어감에 따라 [데이터 드리프트를 모니터링합니다.](how-to-monitor-data-drift.md)

* 모델을 재교육하거나 최적화할 시기에 대해 더 나은 결정을 내릴 수 있습니다.

* 수집된 데이터로 모델을 다시 학습합니다.

## <a name="what-is-collected-and-where-it-goes"></a>수집되는 내용 및 어디로 가는지

다음 데이터를 수집할 수 있습니다.

* AKS 클러스터에 배포된 웹 서비스의 입력 데이터를 모델링합니다. 음성 오디오, 이미지 및 비디오는 *수집되지 않습니다.*
  
* 프로덕션 입력 데이터를 사용하는 모델 예측

>[!NOTE]
> 이 데이터에 대한 사전 집계 및 사전 계산은 현재 컬렉션 서비스의 일부가 아닙니다.

출력은 Blob 저장소에 저장됩니다. 데이터가 Blob 저장소에 추가되므로 좋아하는 도구를 선택하여 분석을 실행할 수 있습니다.

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 버전 0.1.0a16보다 이전의 파이썬용 Azure 기계 학습 SDK 버전에서는 `designation` 인수의 이름이 지정됩니다. `identifier` 이전 버전으로 코드를 개발한 경우 그에 따라 코드를 업데이트해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 없는 경우 시작하기 전에 [무료 계정을](https://aka.ms/AMLFree) 만드세요.

- AzureMachine 학습 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 파이썬용 Azure 기계 학습 SDK를 설치해야 합니다. 설치하는 방법에 대해 알아보려면 [개발 환경을 구성하는 방법을](how-to-configure-environment.md)참조하세요.

- AKS에 배포하려면 숙련된 기계 학습 모델이 필요합니다. 모델이 없는 경우 [Train 이미지 분류 모델](tutorial-train-models-with-aml.md) 자습서를 참조하십시오.

- AKS 클러스터가 필요합니다. 하나를 만들고 배포하는 방법에 대한 자세한 내용은 [배포 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

- 환경을 설정하고 [Azure 기계 학습 모니터링 SDK를](https://aka.ms/aml-monitoring-sdk) [설치합니다.](how-to-configure-environment.md)

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화

Azure 기계 학습 또는 기타 도구를 통해 배포하는 모델에 관계없이 데이터 수집을 활성화할 수 있습니다.

데이터 수집을 사용하려면 다음을 수행해야 합니다.

1. 점수 매기기 파일을 엽니다.

1. 파일 맨 위에 [다음 코드](https://aka.ms/aml-monitoring-sdk)를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. `init` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *상관 Id는* 선택적 매개 변수입니다. 모델에 필요하지 않은 경우 사용할 필요가 없습니다. *CorrelationId를* 사용하면 *LoanNumber* 또는 CustomerId 와 같은 다른 데이터와 보다 쉽게 매핑할 수 *있습니다.*
    
    *식별자* 매개 변수는 나중에 Blob에서 폴더 구조를 빌드하는 데 사용됩니다. 원시 데이터를 처리된 데이터와 구분하는 데 사용할 수 있습니다.

1. `run(input_df)` 함수에 다음 코드 줄을 추가합니다.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. AKS에서 서비스를 배포할 때 데이터 수집이 **true로** 자동으로 *설정되지 않습니다.* 다음 예제와 같이 구성 파일을 업데이트합니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    이 구성을 변경하여 서비스 모니터링을 위한 응용 프로그램 통찰력을 활성화할 수도 있습니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 새 이미지를 만들고 기계 학습 모델을 배포하려면 [배포 방법 및 위치를 참조하세요.](how-to-deploy-and-where.md)

환경 파일에 종속성이 설치되어 있고 점수 매기기 파일이 이미 있는 서비스가 있는 경우 다음 단계를 수행하여 데이터 수집을 사용하도록 설정합니다.

1. Azure [기계 학습으로](https://ml.azure.com)이동합니다.

1. 작업 영역을 엽니다.

1. 배포 > 선택 서비스**편집**을 **선택합니다.** > **Select service**

   ![서비스 편집](././media/how-to-enable-data-collection/EditService.PNG)

1. **고급 설정에서** **응용 프로그램 인사이트 진단 및 데이터 수집 활성화를 선택합니다.**

1. 변경 내용을 적용하려면 **업데이트를** 선택합니다.

## <a name="disable-data-collection"></a>데이터 수집 비활성화

언제든지 데이터 수집을 중지할 수 있습니다. 파이썬 코드 또는 Azure 기계 학습을 사용하여 데이터 수집을 사용하지 않도록 설정합니다.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>옵션 1 - Azure 기계 학습에서 데이터 수집 을 사용하지 않도록 설정

1. [Azure 기계 학습에](https://ml.azure.com)로그인합니다.

1. 작업 영역을 엽니다.

1. 배포 > 선택 서비스**편집**을 **선택합니다.** > **Select service**

   [![편집 옵션 선택](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. **고급 설정에서** **응용 프로그램 인사이트 진단 및 데이터 수집 을 사용하도록 설정합니다.**

1. **업데이트**를 선택하여 변경 내용을 적용합니다.

[Azure 기계 학습의](https://ml.azure.com)작업 영역에서 이러한 설정에 액세스할 수도 있습니다.

### <a name="option-2---use-python-to-disable-data-collection"></a>옵션 2 - 파이썬을 사용하여 데이터 수집을 사용하지 않도록 설정

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>데이터 검증 및 분석

Blob 저장소에서 수집된 데이터를 분석하기 위해 기본 설정 도구를 선택할 수 있습니다.

### <a name="quickly-access-your-blob-data"></a>Blob 데이터에 빠르게 액세스

1. [Azure 기계 학습에](https://ml.azure.com)로그인합니다.

1. 작업 영역을 엽니다.

1. **저장소**를 선택합니다.

    [![저장소 옵션 선택](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 이 구문을 사용하여 Blob의 출력 데이터에 대한 경로를 따릅니다.

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Power BI를 사용하여 모델 데이터 분석

1. 다운로드 및 [전원 BI 데스크톱을](https://www.powerbi.com)엽니 다.

1. **데이터 받기를** 선택하고 [**Azure Blob 저장소를 선택합니다.**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![전원 BI Blob 설정](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. Blob에서 **액세스 설정** > **키를** 선택하여 이 정보를 찾을 수 있습니다.

1. 모델 **데이터** 컨테이너를 선택하고 **편집을**선택합니다.

    [![파워 BI 네비게이터](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 **이름** 열 아래를 클릭하고 저장소 계정을 추가합니다.

1. 모델 경로를 필터에 입력합니다. 특정 연도 또는 월의 파일만 살펴보려면 필터 경로를 확장하기만 하면 됩니다. 예를 들어 3월 데이터만 보려면 다음 필터 경로를 사용합니다.

   \</modeldata/subscriptionid\<>/ 리소스그룹명\<>/ 작업\<공간이름>/\<웹서비스네임>/ 모델명\<>/모델버전>/지정\<>/>/3\<

1. **이름** 값을 기반으로 관련 데이터를 필터링합니다. 예측 및 입력을 저장한 경우 각각에 대한 쿼리를 만들어야 합니다.

1. 파일을 결합하려면 **콘텐츠** 열 제목 옆에 있는 아래쪽 이중 화살표를 선택합니다.

    [![전원 BI 콘텐츠](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. **확인**을 선택합니다. 데이터 프리로드.

    [![전원 BI 파일 결합](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **닫기 및 적용을 선택합니다.**

1. 입력 및 예측을 추가하면 **RequestId** 값에 따라 테이블이 자동으로 정렬됩니다.

1. 모델 데이터에 대한 사용자 지정 보고서 빌드를 시작합니다.

### <a name="analyze-model-data-using-azure-databricks"></a>Azure 데이터브릭을 사용하여 모델 데이터 분석

1. Azure [데이터 브릭 작업 영역을](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)만듭니다.

1. Databricks 작업 영역으로 이동합니다.

1. Databricks 작업 영역에서 **데이터 업로드를**선택합니다.

    [![데이터 브릭 업로드 데이터 옵션 선택](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. **새 테이블 만들기를** 선택하고**전자 필기장에서** **다른 데이터 원본** > **Azure Blob 저장소** > 만들기 테이블을 선택합니다.

    [![데이터 브릭 테이블 생성](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터의 위치를 업데이트합니다. 다음은 예제입니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![데이터 브릭 설정](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 템플릿의 단계를 수행하여 데이터를 보고 분석합니다.
