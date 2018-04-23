---
title: Azure Machine Learning 데이터 준비 운영화를 사용하는 방법에 대한 심층 가이드 | Microsoft Docs
description: 이 문서에서는 이전에 디자인한 데이터 원본 및 데이터 준비 패키지의 실행에 대한 자세한 정보를 제공합니다.
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparation-operationalization"></a>데이터 준비 운영화 

## <a name="operationalization-scenario"></a>운영화 시나리오

다음은 사용자가 직면할 수 있는 다양한 데이터 준비 운영화 시나리오입니다.

### <a name="develop-your-model-based-on-training-data"></a>학습 데이터를 기반으로 모델 개발

실시간 점수 매기기 API 서비스를 만들고 배포한다고 가정합니다. 첫 번째 단계는 일부 학습 데이터 집합을 기준으로 점수 매기기에 대한 모델을 개발하는 것입니다. 데이터 준비는 새 데이터 원본으로 학습 데이터의 샘플을 가져옵니다. 데이터가 준비되면 DataPrep 패키지는 준비 단계를 포함합니다. AzureML 실험 계정을 사용하여 사용자는 학습 모델의 각 입력에 대한 레이블을 생성하기 위해 기계 학습 모델을 반복할 수 있습니다.

업데이트되어야 하는 데이터의 기능으로 사용자는 새로운 방식으로 데이터를 준비하고 해당 단계를 저장하도록 DataPrep 패키지로 돌아갑니다. 새로운 기능을 생성하고 해당 기계 학습 모델을 조정하는 이 반복 과정은 모델에서 해당 테스트 데이터의 점수를 정확하게 매길 때까지 계속됩니다. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Azure 모델 관리 서비스에 모델 배포

이제 실시간으로 점수를 매기길 원하는 고객 데이터가 있습니다. Azure 모델 관리 서비스를 사용하여 서비스로 AzureML Python CLI에서 이 모델을 배포할 수 있습니다. 배포된 서비스는 실시간으로 고객 데이터를 수신하기 위한 REST 엔드포인트를 노출하고 학습된 모델에서 해당 출력 레이블을 반환합니다.

사용 편의성을 위해 모델 엔드포인트는 JSON 형식의 데이터를 허용합니다. 입력은 ReadJSONLiteral 변환을 통해 전달되고 DataPrep 데이터 원본으로 변환되는 데이터의 2차원 배열을 나타내는 JSON 문자열이어야 합니다. 실험 단계 중 만들어진 데이터 준비 패키지는 스트리밍된 JSON 데이터에 대해 실행될 수 있습니다. 그런 다음, 결과 데이터 프레임은 점수를 매기기 위해 학습된 모델로 전달될 수 있습니다.

## <a name="read-json-literal-transformation"></a>JSON 리터럴 변환 읽기

### <a name="description"></a>설명

운영화 목적을 위해 데이터 준비는 작업을 실행하고 Pandas 또는 Spark 데이터 프레임을 생성하도록 **ReadJsonLiteral** 변환을 포함합니다. 이 변환에서는 입력으로 기존 데이터 준비 패키지 및 JSON 데이터 원본을 고유하게 사용합니다. 이 변환은 DataPrep Python CLI를 통해 노출됩니다.

### <a name="instructions"></a>지침

#### <a name="pythoncli"></a>PythonCLI

Azure Machine Learning Workbench에서 명령줄 인터페이스를 엽니다(파일 > 명령 프롬프트 열기).

이 예제에서 TrainingPreparationSteps 데이터 준비 패키지는 데이터를 준비하고 각 입력에 볼륨 기능을 추가하는 데 사용됩니다.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()`에는 다음과 같은 선택적 매개 변수가 있습니다.
 - `dataflow_idx`: 패키지에서 실행하도록 원하는 데이터 흐름의 인덱스를 지정합니다.
 - `secrets`: 보안 저장소 사전(키: secretId, 값: 저장된 비밀)
 - `spark`: spark 실행에 대한 spark 세션을 제공합니다.

#### <a name="input"></a>입력

2차원 배열 입력("배열의 배열")입니다. Python에서 입력은 목록의 목록이어야 합니다. JSON은 네이티브 날짜 형식이 없으므로 모든 Python datetime.datetime 개체는 ISO 형식의 날짜 문자열로 변환됩니다. REST 엔드포인트의 경우 입력은 2차원 JSON 배열을 나타내는 JSON 리터럴 문자열이어야 합니다.

#### <a name="output"></a>출력

Pandas 또는 Spark 데이터 프레임입니다. 데이터 프레임의 형식은 실행 구성에서 선택한 프레임워크에 의해 결정됩니다(`.runconfig`). 결과 데이터 프레임은 입력으로 점수를 매기기 위해 학습된 모델로 전달될 수 있습니다.
