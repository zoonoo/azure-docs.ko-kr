---
title: Monitor and collect data from Machine Learning web service endpoints
titleSuffix: Azure Machine Learning
description: Monitor web services deployed with Azure Machine Learning using Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 19dba88bf04ee84459ebd9ef0279f125724d7522
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406446"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitor and collect data from ML web service endpoints
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to collect data from and monitor models deployed to web service endpoints in Azure Kubernetes Service (AKS) or Azure Container Instances (ACI) by enabling Azure Application Insights. In addition to collecting an endpoint's input data and response, you can monitor:

* 요청 속도, 응답 시간 및 실패율
* 종속성 비율, 응답 시간 및 실패율
* 예외

[Learn more about Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>전제 조건

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. To learn how to get these prerequisites, see [How to configure a development environment](how-to-configure-environment.md)
* AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에 배포할 학습된 Machine Learning 모델. If you don't have one, see the [Train image classification model](tutorial-train-models-with-aml.md) tutorial

## <a name="web-service-input-and-response-data"></a>Web service input and response data

The input and response to the service - corresponding to the inputs to the ML model and its prediction - are logged to the Azure Application Insights traces under the message `"model_data_collection"`. You can query Azure Application Insights directly to access this data, or set up a [continuous export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) to a storage account for longer retention or further processing. Model data can then be used in the Azure ML service to setup labeling, retraining, explainability, data analysis, or other use. 

## <a name="use-the-azure-portal-to-configure"></a>Use the Azure portal to configure

You can enable and disable Azure Application Insights in the Azure portal. 

1. In the [Azure portal](https://portal.azure.com), open your workspace

1. On the **Deployments** tab, select the service where you want to enable Azure Application Insights

   [![배포 탭의 서비스 목록](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Select **Edit**

   [![편집 단추](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Advanced Settings**, select the **Enable AppInsights diagnostics** check box

   [![진단을 사용하도록 설정하기 위한 확인란을 선택함](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Select **Update** at the bottom of the screen to apply the changes

### <a name="disable"></a>사용 안 함

1. In the [Azure portal](https://portal.azure.com), open your workspace
1. Select **Deployments**, select the service, and then select **Edit**

   [![편집 단추 사용](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Advanced Settings**, clear the **Enable AppInsights diagnostics** check box

   [![진단을 사용하도록 설정하기 위한 확인란을 선택 취소함](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Select **Update** at the bottom of the screen to apply the changes
 
## <a name="use-python-sdk-to-configure"></a>Use Python SDK to configure 

### <a name="update-a-deployed-service"></a>배포된 서비스 업데이트

1. 작업 영역에서 서비스를 식별합니다. The value for `ws` is the name of your workspace

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Update your service and enable Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>서비스에서 사용자 지정 추적 로그

사용자 지정 추적을 기록하려는 경우 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서에서 AKS 또는 ACI에 대한 표준 배포 프로세스를 수행합니다. 그런 후 다음 단계를 사용하세요.

1. Update the scoring file by adding print statements
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Update the service configuration
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Build an image and deploy it on [AKS](how-to-deploy-to-aks.md) or [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>Python에서 추적을 사용하지 않도록 설정

To disable Azure Application Insights, use the following code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>데이터 평가
Your service's data is stored in your Azure Application Insights account, within the same resource group as Azure Machine Learning.
이 데이터를 보려면:

1. Go to your Machine Learning service workspace in [Azure Machine Learning studio](https://ml.azure.com) and click on Application Insights link

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Select the **Overview** tab to see a basic set of metrics for your service

   [![개요](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. To look into your web service input and response payloads, select **Analytics**
1. In the schema section, select **Traces** and filter down traces with the message `"model_data_collection"`. In the custom dimensions, you can see the inputs, predictions, and other relevant details

   [![Model data](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. To look into your custom traces, select **Analytics**
4. 스키마 섹션에서 **추적**을 선택합니다. 그런 후 **실행**을 선택하여 쿼리를 실행합니다. Data should appear in a table format and should map to your custom calls in your scoring file

   [![사용자 지정 추적](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

To learn more about how to use Azure Application Insights, see [What is Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Export data for further processing and longer retention

You can use Azure Application Insights' [continuous export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) to send messages to a supported storage account, where a longer retention can be set. The `"model_data_collection"` messages are stored in JSON format and can be easily parsed to extract model data. Azure Data Factory, Azure ML Pipelines, or other data processing tools can be used to transform the data as needed. When you have transformed the data, you can then register it with the Azure Machine Learning workspace as a dataset. To do so, see [How to create and register datasets](how-to-create-register-datasets.md).

   [![Continuous Export](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>예제 Notebook

The [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstrates concepts in this article. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* See [how to deploy a model to an Azure Kubernetes Service cluster](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) or [how to deploy a model to Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) to deploy your models to web service endpoints, and enable Azure Application Insights to leverage data collection and endpoint monitoring
* See [MLOps: Manage, deploy, and monitor models with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) to learn more about leveraging data collected from models in production. Such data can help to continually improve your machine learning process
