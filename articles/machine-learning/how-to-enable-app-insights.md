---
title: Machine Learning 웹 서비스 끝점에서 데이터 모니터링 및 수집
titleSuffix: Azure Machine Learning
description: Azure 애플리케이션 Insights를 사용 하 여 Azure Machine Learning 배포 된 웹 서비스 모니터링
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: e12c22d56399ce1690bee678623c58288cf0163b
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552206"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>ML 웹 서비스 엔드포인트에서 데이터 모니터링 및 수집
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 로그를 쿼리하고 Azure 애플리케이션 Insights를 통해 Azure Kubernetes Service Azure Container Instances (ACI)에서 웹 서비스 끝점에 배포 된 모델에서 데이터를 수집 하 고 모니터링 하는 방법에 대해 알아봅니다. 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning studio](#studio)https://ml.azure.com

끝점의 출력 데이터와 응답을 수집 하는 것 외에도 다음을 모니터링할 수 있습니다.

* 요청 속도, 응답 시간 및 실패율
* 종속성 비율, 응답 시간 및 실패율
* 예외

[Azure 애플리케이션 Insights에 대해 자세히 알아보세요](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험

* Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 이러한 필수 구성 요소를 가져오는 방법을 알아보려면 [개발 환경을 구성](how-to-configure-environment.md) 하는 방법을 참조 하세요.

* AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에 배포할 학습된 Machine Learning 모델. 없는 경우 [학습 이미지 분류 모델](tutorial-train-models-with-aml.md) 자습서를 참조 하세요.

## <a name="query-logs-for-deployed-models"></a>배포 된 모델에 대 한 로그 쿼리

이전에 배포된 웹 서비스에서 로그를 검색하려면 서비스를 로드하고 `get_logs()` 함수를 사용합니다. 로그에는 배포 중에 발생한 오류에 대한 자세한 정보가 포함되어 있을 수 있습니다.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

## <a name="web-service-metadata-and-response-data"></a>웹 서비스 메타 데이터 및 응답 데이터

> [!IMPORTANT]
> Azure 애플리케이션 Insights는 최대 64kb의 페이로드를 기록 합니다. 이 제한에 도달 하면 메모리 부족 등의 오류가 표시 되거나 정보가 기록 되지 않을 수 있습니다.

웹 서비스 요청에 대 한 정보를 기록 하려면 `print` score.py 파일에 문을 추가 합니다. 각 `print` 문은 Application Insights의 추적 테이블에 있는 하나의 항목을 메시지 아래에 생성 `STDOUT` 합니다. `print`문의 내용은 `customDimensions` `Contents` 추적 테이블의 아래에 포함 됩니다. JSON 문자열을 인쇄할 경우에서 추적 출력에 계층적 데이터 구조를 생성 `Contents` 합니다.

이 데이터에 액세스 하거나 더 긴 보존 또는 추가 처리를 위해 저장소 계정에 대 한 [연속 내보내기를](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) 설정 하기 위해 Azure 애플리케이션 Insights를 직접 쿼리할 수 있습니다. 모델 데이터는 Azure Machine Learning에서 레이블 지정, 재 학습, explainability, 데이터 분석 또는 기타 사용을 설정 하는 데 사용할 수 있습니다. 


<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Python SDK를 사용 하 여 구성 

### <a name="update-a-deployed-service"></a>배포된 서비스 업데이트

1. 작업 영역에서 서비스를 식별합니다. 값은 `ws` 작업 영역의 이름입니다.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 서비스 업데이트 및 Azure 애플리케이션 Insights 사용

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>서비스에서 사용자 지정 추적 로그

사용자 지정 추적을 기록하려는 경우 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서에서 AKS 또는 ACI에 대한 표준 배포 프로세스를 수행합니다. 그런 후 다음 단계를 사용하세요.

1. 유추 하는 동안 Application Insights로 데이터를 보내려면 인쇄 문을 추가 하 여 점수 매기기 파일을 업데이트 합니다. 요청 데이터 및 응답과 같은 보다 복잡 한 정보를 기록 하려면 JSON 구조를 추가 합니다. 다음 예에서는 모델을 초기화 한 시간, 유추 하는 동안 입력 및 출력을 score.py, 오류가 발생 하는 시간을 기록 합니다.

    > [!IMPORTANT]
    > Azure 애플리케이션 Insights는 최대 64kb의 페이로드를 기록 합니다. 이 제한에 도달 하면 메모리 부족 등의 오류가 표시 되거나 정보가 기록 되지 않을 수 있습니다. 로그 하려는 데이터가 64kb 보다 큰 경우 [프로덕션 환경에서 모델에 대 한 데이터 수집](how-to-enable-data-collection.md)의 정보를 사용 하 여 blob 저장소에 저장 해야 합니다.
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. 서비스 구성 업데이트
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 이미지를 빌드하고 [AKS 또는 ACI](how-to-deploy-and-where.md)에 배포합니다.

로깅 및 데이터 수집에 대 한 자세한 내용은 [Azure Machine Learning 로깅 사용](how-to-enable-logging.md) 및 [프로덕션 환경에서 모델에서 데이터 수집](how-to-enable-data-collection.md)을 참조 하세요.

### <a name="disable-tracking-in-python"></a>Python에서 추적을 사용하지 않도록 설정

Azure 애플리케이션 Insights를 사용 하지 않도록 설정 하려면 다음 코드를 사용 합니다.

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Azure Machine Learning studio를 사용 하 여 구성

이러한 단계를 사용 하 여 모델을 배포할 준비가 되 면 Azure Machine Learning studio에서 Azure 애플리케이션 Insights를 사용 하도록 설정할 수도 있습니다.

1. 에서 작업 영역에 로그인 합니다.https://ml.azure.com/
1. **모델** 로 이동 하 여 배포할 모델을 선택 합니다.
1. **+ 배포** 선택
1. **모델 배포** 양식 채우기
1. **고급** 메뉴 확장

    ![배포 양식](./media/how-to-enable-app-insights/deploy-form.png)
1. **Application Insights 진단 및 데이터 수집 사용을** 선택 합니다.

    ![앱 Insights 사용](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>메트릭 및 로그 보기

서비스의 데이터는 Azure Machine Learning와 동일한 리소스 그룹 내에 Azure 애플리케이션 Insights 계정에 저장 됩니다.
이 데이터를 보려면:

1. [스튜디오](https://ml.azure.com/)에서 Azure Machine Learning 작업 영역으로 이동 합니다.
1. **엔드포인트**를 선택합니다.
1. 배포 된 서비스를 선택 합니다.
1. 아래로 스크롤하여 **Application Insights url** 을 찾고 링크를 선택 합니다.

    [![Application Insights url 찾기](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Application Insights의 **개요** 탭 또는 왼쪽 목록의 __모니터링__ 섹션에서 __로그__를 선택 합니다.

    [![모니터링의 개요 탭](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Score.py 파일에서 기록 된 정보를 보려면 __추적__ 테이블을 확인 합니다. 다음 쿼리는 __입력__ 값이 기록 된 로그를 검색 합니다.

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![추적 데이터](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Azure 애플리케이션 Insights를 사용 하는 방법에 대 한 자세한 내용은 [Application Insights 무엇입니까?](../azure-monitor/app/app-insights-overview.md)를 참조 하세요.

## <a name="export-data-for-further-processing-and-longer-retention"></a>추가 처리 및 더 긴 보존을 위해 데이터 내보내기

>[!Important]
> Azure 애플리케이션 Insights는 blob 저장소에 대 한 내보내기만 지원 합니다. 이 내보내기 기능의 추가 제한은 [App Insights에서 원격 분석 내보내기](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)에 나열 되어 있습니다.

Azure 애플리케이션 Insights의 [연속 내보내기를](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) 사용 하 여 지원 되는 저장소 계정으로 메시지를 보낼 수 있으며,이 경우 더 긴 보존을 설정할 수 있습니다. 데이터는 JSON 형식으로 저장 되며 모델 데이터를 추출 하기 위해 쉽게 구문 분석할 수 있습니다. 

Azure Data Factory, Azure ML 파이프라인 또는 다른 데이터 처리 도구를 사용 하 여 필요에 따라 데이터를 변환할 수 있습니다. 데이터를 변환한 후에는 데이터 집합으로 Azure Machine Learning 작업 영역에 데이터를 등록할 수 있습니다. 이렇게 하려면 [데이터 집합을 만들고 등록 하는 방법](how-to-create-register-datasets.md)을 참조 하세요.

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="연속 내보내기":::


## <a name="example-notebook"></a>예제 Notebook

[앱 기반-bbb노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) 은이 문서의 개념을 보여 줍니다. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Kubernetes Service 클러스터에 모델](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) 을 배포 하는 방법 또는 [Azure Container Instances에 모델](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) 을 배포 하 여 웹 서비스 끝점에 모델을 배포 하는 방법 및 Azure 애플리케이션 Insights를 사용 하 여 데이터 수집 및 끝점 모니터링 활용
* 프로덕션 환경에서 수집한 데이터를 활용 하는 방법에 대 한 자세한 내용은 [Mlops: Azure Machine Learning를 사용 하 여 모델 관리, 배포 및 모니터링](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) 을 참조 하세요. 이러한 데이터는 기계 학습 프로세스를 지속적으로 개선 하는 데 도움이 될 수 있습니다.
