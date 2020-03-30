---
title: 기계 학습 웹 서비스 엔드포인트에서 데이터 모니터링 및 수집
titleSuffix: Azure Machine Learning
description: Azure 응용 프로그램 통찰력을 사용하여 Azure 기계 학습으로 배포된 웹 서비스 모니터링
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136196"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>ML 웹 서비스 엔드포인트에서 데이터 모니터링 및 수집
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 응용 프로그램 인사이트를 통해 Azure 응용 프로그램 인사이트를 사용하도록 설정하여 AKS(Azure Kubernetes Service) 또는 ACI(Azure 컨테이너 인스턴스)의 웹 서비스 끝점에 배포된 모델에서 데이터를 수집하고 모니터링하는 방법을 배웁니다. 
* [Azure 기계 학습 파이썬 SDK](#python)
* [Azure 기계 학습 스튜디오에서](#studio)https://ml.azure.com

엔드포인트의 출력 데이터 및 응답을 수집하는 것 외에도 다음을 모니터링할 수 있습니다.

* 요청 속도, 응답 시간 및 실패율
* 종속성 비율, 응답 시간 및 실패율
* 예외

[Azure 응용 프로그램 인사이트에 대해 자세히 알아봅니다.](../azure-monitor/app/app-insights-overview.md) 


## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure 기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree) 사용해 보십시오.

* Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 이러한 필수 구성 조건을 얻는 방법에 대해 알아보려면 [개발 환경을 구성하는 방법을](how-to-configure-environment.md) 참조하세요.

* AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에 배포할 학습된 Machine Learning 모델. 없는 경우 Train 이미지 분류 [모델](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

## <a name="web-service-metadata-and-response-data"></a>웹 서비스 메타데이터 및 응답 데이터

>[!Important]
> Azure 응용 프로그램 인사이트는 최대 64kb의 페이로드만 기록합니다. 이 제한에 도달하면 모델의 최신 출력만 기록됩니다. 

웹 서비스 메타데이터 및 모델의 예측에 해당하는 서비스에 대한 메타데이터 및 응답은 메시지 `"model_data_collection"`아래의 Azure Application Insights 추적에 기록됩니다. Azure Application Insights를 직접 쿼리하여 이 데이터에 액세스하거나 저장소 계정으로 [의 지속적인 내보내기를](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) 설정하여 보존 기간이 길거나 추가 처리를 할 수 있습니다. 그런 다음 Azure 기계 학습에서 모델 데이터를 사용하여 레이블 지정, 재교육, 설명 가능성, 데이터 분석 또는 기타 용도를 설정할 수 있습니다. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>파이썬 SDK를 사용하여 구성 

### <a name="update-a-deployed-service"></a>배포된 서비스 업데이트

1. 작업 영역에서 서비스를 식별합니다. `ws` 값은 작업 영역의 이름입니다.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 서비스를 업데이트하고 Azure 응용 프로그램 인사이트를 사용하도록 설정합니다.

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>서비스에서 사용자 지정 추적 로그

사용자 지정 추적을 기록하려는 경우 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서에서 AKS 또는 ACI에 대한 표준 배포 프로세스를 수행합니다. 그런 후 다음 단계를 사용하세요.

1. 인쇄 문을 추가하여 점수 매기기 파일 업데이트
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 서비스 구성 업데이트
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 이미지를 빌드하고 [AKS 또는 ACI](how-to-deploy-and-where.md)에 배포합니다.

### <a name="disable-tracking-in-python"></a>Python에서 추적을 사용하지 않도록 설정

Azure 응용 프로그램 정보를 사용하지 않도록 설정하려면 다음 코드를 사용합니다.

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Azure 기계 학습 스튜디오를 사용하여 구성

이러한 단계를 통해 모델을 배포할 준비가 되면 Azure 기계 학습 스튜디오에서 Azure 응용 프로그램 인사이트를 활성화할 수도 있습니다.

1. 에서 작업 영역에 로그인https://ml.azure.com/
1. **모델로** 이동하여 배포할 모델을 선택합니다.
1. **+배포** 선택
1. **배포 모델** 양식 채우기
1. **고급** 메뉴 확장

    ![양식 배포](./media/how-to-enable-app-insights/deploy-form.png)
1. **애플리케이션 인사이트 진단 및 데이터 수집 사용 선택**

    ![앱 인사이트 사용](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>데이터 평가
서비스의 데이터는 Azure Machine Learning과 동일한 리소스 그룹 내에서 Azure Application Insights 계정에 저장됩니다.
이 데이터를 보려면:

1. [Azure 포털의](https://ms.portal.azure.com/) Azure 기계 학습 작업 영역으로 이동하여 응용 프로그램 인사이트 링크를 클릭합니다.

    [![앱인사이트로크](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. **개요** 탭을 선택하여 서비스에 대한 기본 메트릭 집합을 확인합니다.

   [![개요](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. 웹 서비스 요청 메타데이터 및 응답을 조사하려면 **로그(분석)** 섹션에서 **요청** 테이블을 선택하고 **실행을** 선택하여 요청을 봅니다.

   [![모델 데이터](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 사용자 지정 추적을 조사하려면 **애널리틱스를** 선택합니다.
4. 스키마 섹션에서 **추적**을 선택합니다. 그런 후 **실행**을 선택하여 쿼리를 실행합니다. 데이터는 테이블 형식으로 표시되어야 하며 채점 파일의 사용자 지정 호출에 매핑해야 합니다.

   [![사용자 지정 추적](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Azure 응용 프로그램 인사이트를 사용하는 방법에 대한 자세한 내용은 [응용 프로그램 인사이트란 무엇입니까?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>추가 처리 및 보존 기간을 위해 데이터 내보내기

>[!Important]
> Azure 응용 프로그램 인사이트는 Blob 저장소로의 내보내기만 지원합니다. 이 내보내기 기능의 추가 제한은 [App Insights의 내보내기 원격 분석에](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)나열됩니다.

Azure Application Insights의 [지속적인 내보내기를](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) 사용하여 더 긴 보존을 설정할 수 있는 지원되는 저장소 계정으로 메시지를 보낼 수 있습니다. `"model_data_collection"` 메시지는 JSON 형식으로 저장되며 모델 데이터를 추출하기 위해 쉽게 구문 분석할 수 있습니다. 

Azure 데이터 팩터리, Azure ML 파이프라인 또는 기타 데이터 처리 도구를 사용하여 필요에 따라 데이터를 변환할 수 있습니다. 데이터를 변환한 경우 Azure 기계 학습 작업 영역에 데이터 집합으로 등록할 수 있습니다. 이렇게 하려면 [데이터 집합을 만들고 등록하는 방법을](how-to-create-register-datasets.md)참조하세요.

   [![연속 내보내기](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>예제 Notebook

[응용 프로그램 인사이트 인사이트-인-프로덕션 서비스.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) 노트북은 이 문서의 개념을 보여 줍니다. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Kubernetes 서비스 클러스터에 모델을 배포하는 방법](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) 또는 Azure 컨테이너 [인스턴스에 모델을 배포하여](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) 모델을 웹 서비스 엔드포인트에 배포하고 Azure Application Insights에서 데이터 수집 및 엔드포인트 모니터링을 활용할 수 있도록 하는 방법을 알아보십시오.
* [MlOps: Azure Machine Learning을 사용하여 모델을 관리, 배포 및 모니터링하여](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) 프로덕션 모델에서 수집된 데이터를 활용하는 방법에 대해 자세히 알아봅니다. 이러한 데이터는 기계 학습 프로세스를 지속적으로 개선하는 데 도움이 될 수 있습니다.
