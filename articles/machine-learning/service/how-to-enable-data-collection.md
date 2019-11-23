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
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406464"
---
# <a name="collect-data-for-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> This SDK is retiring soon. This SDK is still appropriate for developers monitoring data drift in models but most developers should use the simplified [data monitoring with Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In this article, you can learn how to collect input model data from Azure Machine Learning you've deployed into Azure Kubernetes Cluster (AKS) into an Azure Blob storage. 

데이터를 수집하도록 설정하면 이렇게 수집한 데이터는 다음과 같은 용도로 활용할 수 있습니다.
* [Monitor data drifts](how-to-monitor-data-drift.md) as production data enters your model

* 모델을 다시 학습시키거나 최적화할 시기를 더 효율적으로 결정

* 수집한 데이터로 모델 다시 학습시키기

## <a name="what-is-collected-and-where-does-it-go"></a>수집되는 데이터 및 데이터가 이동하는 위치

다음 데이터를 수집할 수 있습니다.
* AKS(Azure Kubernetes 클러스터)에 배포된 웹 서비스의 모델 **입력** 데이터(음성, 이미지 및 비디오는 수집되지 **않음**) 
  
* Model predictions using production input data

> [!Note]
> 이 데이터의 사전 집계 또는 사전 계산 기능은 현재 서비스에서 제공되지 않습니다.   

출력은 Azure Blob에 저장됩니다. 데이터가 Azure Blob에 추가되므로 원하는 도구를 선택하여 분석을 수행할 수 있습니다. 

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versions of the SDK prior to `0.1.0a16` the `designation` argument was named `identifier`. If your code was developed with an earlier version, you will need to update accordingly.

## <a name="prerequisites"></a>전제 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. Learn how to get these prerequisites using the [How to configure a development environment](how-to-configure-environment.md) document

- AKS(Azure Kubernetes Service)에 배포할 학습된 Machine Learning 모델. If you don't have one, see the [train image classification model](tutorial-train-models-with-aml.md) tutorial

- Azure Kubernetes Service 클러스터입니다. For information on how to create and deploy to one, see the [How to deploy and where](how-to-deploy-and-where.md) document

- [Set up your environment](how-to-configure-environment.md) and install the [Monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화
Data collection can be enabled regardless of the model being deployed through Azure Machine Learning or other tools. 

데이터 수집을 사용하도록 설정하려면 다음 단계를 수행해야 합니다.

1. Open the scoring file

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
    
    *Identifier* is later used for building the folder structure in your Blob, it can be used to divide “raw” data versus “processed”

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

5. To create a new image and deploy the service, see the [How to deploy and where](how-to-deploy-and-where.md) document


**환경 파일** 및 **점수 매기기 파일**의 종속성이 설치된 서비스가 이미 있으면 다음 단계를 수행하여 데이터 수집을 사용하도록 설정합니다.

1. Go to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace

1. Go to **Deployments** -> **Select service** -> **Edit**

   ![서비스 편집](media/how-to-enable-data-collection/EditService.PNG)

1. In **Advanced Settings**, select **Enable Model data collection**

    [![데이터 수집 선택](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In this window, you can also choose to "Enable Appinsights diagnostics" to track the health of your service

1. Select **Update** to apply the change


## <a name="disable-data-collection"></a>데이터 수집 비활성화
데이터 수집은 언제든지 중지할 수 있습니다. Use Python code or Azure Machine Learning studio to disable data collection.

+ Option 1 - Disable in Azure Machine Learning studio: 
  1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

  1. Open your workspace

  1. Go to **Deployments** -> **Select service** -> **Edit**

     [![편집 옵션](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Advanced Settings**, deselect **Enable Model data collection**

     [![데이터 수집 선택 취소](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Select **Update** to apply the change

  You can also access these settings in your workspace in [Azure Machine Learning studio](https://ml.azure.com).

+ 옵션 2 - Python을 사용하여 데이터 수집을 사용하지 않도록 설정

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>데이터 유효성 검사 및 분석
선호하는 도구를 선택하여 Azure Blob에 수집된 데이터를 분석할 수 있습니다.

Blob의 데이터에 빠르게 액세스하려면 다음을 수행합니다.

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace
1. Click on **Storage**

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob의 출력 데이터 경로는 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Power BI를 통해 모델 데이터 분석

1. Download and Open [Power BI Desktop](https://www.powerbi.com)

1. Select **Get Data** and click on [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![PBI Blob 설정](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. You can find this information in your blob's **Settings** >> Access keys

1. Select the container **modeldata** and click on **Edit**

    [![PBI 탐색기](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 “이름” 열 아래를 클릭하고 스토리지 계정 1을 추가합니다. 필터에 대한 경로를 모델링합니다. 참고: 특정 연도 또는 특정 월의 파일만 보려면 필터 경로를 확장하기만 하면 됩니다. For example, just look into March data: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/designation>/year>/3

1. **이름**을 기준으로 관련된 데이터를 필터링합니다. If you stored **predictions** and **inputs**, you'll need to create a query for each

1. Click on the double arrow aside the **Content** column to combine the files

    [![PBI 콘텐츠](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Click OK and the data will preload

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. You can now click **Close and Apply**

1.  If you added inputs and predictions, your tables will automatically correlate by **RequestId**

1. Start building your custom reports on your model data


### <a name="analyzing-model-data-using-databricks"></a>Databricks를 사용하여 모델 데이터 분석

1. Create a [Databricks workspace](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Go to your Databricks workspace

1. In your databricks workspace, select **Upload Data**

    [![DB 업로드](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Create New Table and select **Other Data Sources** -> Azure Blob Storage -> Create Table in Notebook

    [![DB 테이블](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터 위치를 업데이트합니다. 다음은 예제입니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Follow the steps on the template in order to view and analyze your data

## <a name="example-notebook"></a>예제 Notebook

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Notebook은 이 문서의 개념을 보여 줍니다.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
