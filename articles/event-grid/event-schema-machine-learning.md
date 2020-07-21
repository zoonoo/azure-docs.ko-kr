---
title: Event Grid 원본으로 Azure Machine Learning
description: 를 사용 하 여 Machine Learning 작업 영역 이벤트에 제공 되는 속성을 설명 Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e72123a4f609b93e191c82f11443cbb1de7d012d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86522077"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Event Grid 원본으로 Azure Machine Learning

이 문서에서는 machine learning 작업 영역 이벤트의 속성과 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Machine Learning는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| MachineLearningServices가 등록 되었습니다. | 새 모델 또는 모델 버전이 성공적으로 등록 되 면 발생 합니다. |
| MachineLearningServices 배포 | 모델이 끝점에 성공적으로 배포 되 면 발생 합니다. |
| MachineLearningServices | 실행이 성공적으로 완료 되 면 발생 합니다. |
| MachineLearningServices. DatasetDriftDetected | 데이터 집합 드리프트 모니터가 드리프트를 검색 하는 경우 발생 합니다. |
| MachineLearningServices. RunStatusChanged | 실행 상태가 변경 될 때 발생 합니다. |

### <a name="the-contents-of-an-event-response"></a>이벤트 응답 내용

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>MachineLearningServices에 등록 된 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>MachineLearningServices 배포 된 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>MachineLearningServices 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>MachineLearningServices. DatasetDriftDetected 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>MachineLearningServices 이벤트입니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunStatusChanged",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체에는 각 이벤트 유형에 대 한 다음 속성이 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered"></a>MachineLearningServices가 등록 되었습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ModelName | 문자열 | 등록 된 모델의 이름입니다. |
| ModelVersion | 문자열 | 등록 된 모델의 버전입니다. |
| ModelTags | object | 등록 된 모델의 태그입니다. |
| ModelProperties | object | 등록 된 모델의 속성입니다. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>MachineLearningServices 배포

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ServiceName | 문자열 | 배포 된 서비스의 이름입니다. |
| ServiceComputeType | 문자열 | 배포 된 서비스의 계산 형식 (예: ACI, AKS)입니다. |
  | ModelIds | 문자열 | 쉼표로 구분 된 모델 Id 목록입니다. 서비스에 배포 된 모델의 Id입니다. |
| ServiceTags | object | 배포 된 서비스의 태그입니다. |
| ServiceProperties | object | 배포 된 서비스의 속성입니다. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>MachineLearningServices

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ExperimentId | 문자열 | 실행이 속한 실험의 ID입니다. |
| ExperimentName | 문자열 | 실행이 속한 실험의 이름입니다. |
| RunId | 문자열 | 완료 된 실행의 ID입니다. |
| RunType | 문자열 | 완료 된 실행의 실행 형식입니다. |
| RunTags | object | 완료 된 실행의 태그입니다. |
| RunProperties | object | 완료 된 실행의 속성입니다. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>MachineLearningServices. DatasetDriftDetected

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| DataDriftId | 문자열 | 이벤트를 트리거한 데이터 드리프트 모니터의 ID입니다. |
| DataDriftName | 문자열 | 이벤트를 트리거한 데이터 드리프트 모니터의 이름입니다. |
| RunId | 문자열 | 데이터 드리프트를 검색 한 실행의 ID입니다. |
| BaseDatasetId | 문자열 | 드리프트를 검색 하는 데 사용 되는 기본 데이터 집합의 ID입니다. |
| TargetDatasetId | 문자열 | 드리프트를 검색 하는 데 사용 되는 대상 데이터 집합의 ID입니다. |
| DriftCoefficient | double | 이벤트를 트리거한 계수 결과입니다. |
| StartTime | Datetime | 드리프트 검색을 일으킨 대상 데이터 집합 시계열의 시작 시간입니다.  |
| EndTime | Datetime | 드리프트 검색을 일으킨 대상 데이터 집합 시계열의 종료 시간입니다. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>MachineLearningServices. RunStatusChanged

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ExperimentId | 문자열 | 실행이 속한 실험의 ID입니다. |
| ExperimentName | 문자열 | 실행이 속한 실험의 이름입니다. |
| RunId | 문자열 | 완료 된 실행의 ID입니다. |
| RunType | 문자열 | 완료 된 실행의 실행 형식입니다. |
| RunTags | object | 완료 된 실행의 태그입니다. |
| RunProperties | object | 완료 된 실행의 속성입니다. |
| RunStatus | 문자열 | 실행의 상태입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
| 제목 | Description |
| ----- | ----- |
| [Azure Machine Learning 이벤트 사용](../machine-learning/concept-event-grid-integration.md) | Event Grid와 Azure Machine Learning 통합에 대 한 개요입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대 한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md) 를 참조 하세요.
* Azure Machine Learning와 함께 Azure Event Grid를 사용 하는 방법에 대 한 소개는 [Azure Machine Learning 이벤트](/azure/machine-learning/concept-event-grid-integration) 사용을 참조 하세요.
* Azure Machine Learning와 함께 Azure Event Grid를 사용 하는 예제는 [이벤트 기반 기계 학습 워크플로 만들기](/azure/machine-learning/how-to-use-event-grid) 를 참조 하세요.
