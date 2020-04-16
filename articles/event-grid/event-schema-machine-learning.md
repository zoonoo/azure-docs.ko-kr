---
title: 이벤트 그리드 소스로 Azure 기계 학습
description: Azure 이벤트 그리드를 통해 기계 학습 작업 영역 이벤트에 대해 제공되는 속성에 대해 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7d9af420c7e2b47d2aeb4a8bf42ee138a605b305
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393269"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>이벤트 그리드 소스로서의 Azure 기계 학습

이 문서에서는 기계 학습 작업 영역 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure 기계 학습은 다음 이벤트 유형을 내보며 다음을 처리합니다.

| 이벤트 유형 | Description |
| ---------- | ----------- |
| 마이크로소프트.머신러닝서비스.모델등록 | 새 모델 또는 모델 버전이 성공적으로 등록되었을 때 발생합니다. |
| 마이크로소프트.머신러닝서비스.모델 배포 | 모델이 엔드포인트에 성공적으로 배포되었을 때 발생합니다. |
| 마이크로소프트.머신러닝서비스.실행완료 | 실행이 성공적으로 완료되면 발생합니다. |
| 마이크로소프트.머신러닝서비스.데이터셋드리프트감지 | 데이터 집합 드리프트 모니터가 드리프트를 감지할 때 발생합니다. |
| 마이크로소프트.머신러닝서비스.실행상태 변경 | 실행 상태가 '실패'로 변경될 때 발생합니다. |

### <a name="the-contents-of-an-event-response"></a>이벤트 응답의 내용

이벤트가 트리거되면 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 끝점으로 보냅니다.

이 섹션에는 각 이벤트에 대해 해당 데이터가 어떻게 보이는지에 대한 예제가 포함되어 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>마이크로소프트.머신러닝서비스.모델등록 이벤트

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>마이크로소프트.머신러닝서비스.모델 배포 이벤트

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>마이크로소프트.머신러닝서비스.실행완료 이벤트

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>마이크로소프트.머신러닝서비스.데이터셋드리프트감지이벤트

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>마이크로소프트.머신러닝서비스.실행상태 변경 이벤트

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
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체에는 각 이벤트 형식에 대한 다음 속성이 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered"></a>마이크로소프트.머신러닝서비스.모델등록

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| ModelName | 문자열 | 등록된 모델의 이름입니다. |
| 모델 버전 | 문자열 | 등록된 모델의 버전입니다. |
| 모델 태그 | object | 등록된 모델의 태그입니다. |
| 모델 속성 | object | 등록된 모델의 속성입니다. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>마이크로소프트.머신러닝서비스.모델 배포

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| ServiceName | 문자열 | 배포된 서비스의 이름입니다. |
| 서비스컴퓨팅 유형 | 문자열 | 배포된 서비스의 계산 유형(예: ACI, AKS)입니다. |
  | 모델 ID | 문자열 | 모델 아이디의 쉼표 분리 목록입니다. 서비스에 배포된 모델의 아이디입니다. |
| 서비스 태그 | object | 배포된 서비스의 태그입니다. |
| 서비스 속성 | object | 배포된 서비스의 속성입니다. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>마이크로소프트.머신러닝서비스.실행완료

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 실험 ID | 문자열 | 실행이 속한 실험의 ID입니다. |
| 실험 이름 | 문자열 | 실행이 속한 실험의 이름입니다. |
| 런Id | 문자열 | 완료된 실행의 ID입니다. |
| 실행 유형 | 문자열 | 완료된 실행의 실행 유형입니다. |
| 런태그 | object | 완료된 실행의 태그입니다. |
| 런프로퍼티 | object | 완료된 실행의 속성입니다. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>마이크로소프트.머신러닝서비스.데이터셋드리프트감지

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 데이터 드리프트 Id | 문자열 | 이벤트를 트리거한 데이터 드리프트 모니터의 ID입니다. |
| 데이터드리프트네임 | 문자열 | 이벤트를 트리거한 데이터 드리프트 모니터의 이름입니다. |
| 런Id | 문자열 | 데이터 드리프트를 감지한 실행의 ID입니다. |
| 베이스데이터세트Id | 문자열 | 드리프트를 감지하는 데 사용되는 기본 데이터 집합의 ID입니다. |
| 대상데이터셋Id | 문자열 | 드리프트를 감지하는 데 사용되는 대상 데이터 집합의 ID입니다. |
| 드리프트코효율 | double | 이벤트를 트리거한 계수 결과입니다. |
| StartTime | Datetime | 드리프트 감지를 초래한 대상 데이터 집합 시간계의 시작 시간입니다.  |
| EndTime | Datetime | 드리프트 감지를 초래한 대상 데이터 집합 시간계의 종료 시간입니다. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>마이크로소프트.머신러닝서비스.실행상태 변경

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 실험 ID | 문자열 | 실행이 속한 실험의 ID입니다. |
| 실험 이름 | 문자열 | 실행이 속한 실험의 이름입니다. |
| 런Id | 문자열 | 완료된 실행의 ID입니다. |
| 실행 유형 | 문자열 | 완료된 실행의 실행 유형입니다. |
| 런태그 | object | 완료된 실행의 태그입니다. |
| 런프로퍼티 | object | 완료된 실행의 속성입니다. |
| 런 상태 | 문자열 | 실행의 상태입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
| 제목 | Description |
| ----- | ----- |
| [Azure 기계 학습 이벤트 사용](../machine-learning/concept-event-grid-integration.md) | Azure 기계 학습을 이벤트 그리드와 통합하는 개요입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure 이벤트 그리드 구독을 만드는 데 대한 자세한 내용은 [이벤트 그리드 구독 스키마를](subscription-creation-schema.md) 참조하십시오.
* Azure 기계 학습을 사용하여 Azure 이벤트 그리드를 사용하는 방법을 보려면 [Azure 기계 학습 사용 이벤트를](/azure/machine-learning/service/concept-event-grid-integration) 참조하십시오.
* Azure 기계 학습과 함께 Azure 이벤트 그리드를 사용하는 예는 [이벤트 기반 기계 학습 워크플로 만들기를 참조하세요.](/azure/machine-learning/service/how-to-use-event-grid)
