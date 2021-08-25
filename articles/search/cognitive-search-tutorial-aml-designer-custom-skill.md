---
title: '예제: Azure Machine Learning 디자이너를 사용하여 사용자 지정 기술 만들기 및 배포'
titleSuffix: Azure Cognitive Search
description: 이 예제에서는 Azure Machine Learning 디자이너를 사용하여 Azure Cognitive Search의 AI 보강 파이프라인에 대한 사용자 지정 AML기술을 빌드하고 배포하는 방법을 보여줍니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 5af4110d1092c2e0e916d1404e9177b64e650e0b
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728113"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning-designer"></a>예제: Azure Machine Learning 디자이너를 사용하여 사용자 지정 기술 빌드 및 배포

[Azure Machine Learning 디자이너](../machine-learning/concept-designer.md)는 회귀 및 분류와 같은 작업을 위한 기계 학습 모델을 만드는 사용하기 쉬운 대화형 캔버스입니다. Cognitive Search 보강 파이프라인에서 디자이너가 만든 모델을 호출하려면 몇 가지 추가 단계가 필요합니다. 이 예에서는 자동차 가격을 예측하고 AML 기술로 유추 엔드포인트를 호출하는 간단한 회귀 모델을 만듭니다. 

[예제 파이프라인 및 데이터 세트](../machine-learning/concept-designer.md) 설명서 페이지의 [회귀 - 자동차 가격 예측(고급)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) 자습서에 따라 다양한 기능을 제공하는 자동차의 가격을 예측하는 모델을 만듭니다.

> [!IMPORTANT] 
> 실시간 유추 프로세스에 따라 모델을 배포하면 유효한 엔드포인트가 생성되지만 Cognitive Search에서 AML 기술에 사용할 수 있는 엔드포인트는 아닙니다. 

## <a name="register-model-and-download-assets"></a>모델 등록 및 자산 다운로드

학습된 모델이 있으면 [학습된 모델을 등록](../machine-learning/how-to-deploy-model-designer.md)하고 단계에 따라 `trained_model_outputs` 폴더에 있는 모든 파일을 다운로드하거나 모델 아티팩트 페이지에서 `score.py` 및 `conda_env.yml` 파일만 다운로드합니다. 모델이 실시간 유추 엔드포인트로 배포되기 전에 채점 스크립트를 편집합니다.


## <a name="edit-the-scoring-script-for-use-with-cognitive-search"></a>Cognitive Search에서 사용할 채점 스크립트 편집 

Cognitive Search 보강 파이프라인은 단일 문서에 대해 작업을 수행하고 단일 예측에 대한 입력이 포함된 요청을 생성합니다. 다운로드한 `score.py`는 레코드 목록을 수락하고 예측 목록을 직렬화된 JSON 문자열로 반환합니다. `score.py`에 두 가지 변경 사항을 적용합니다.

* 목록이 아닌 단일 입력 레코드로 작업하도록 스크립트를 편집합니다.
* 단일 속성인 예상 가격을 포함하는 JSON 개체를 반환하도록 스크립트를 편집합니다.

다운로드한 `score.py`를 열고 `run(data)` 함수를 편집합니다. 함수는 모델의 `_samples.json` 파일에 설명된 대로 다음 입력을 예상하도록 현재 설정되어 있습니다.

```json
[
  {
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
  },
  {
    "symboling": 0,
    "make": "toyota",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "four",
    "body-style": "wagon",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 95.7,
    "length": 169.7,
    "width": 63.6,
    "height": 59.1,
    "curb-weight": 2280,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 3.05,
    "stroke": 3.03,
    "compression-ratio": 9.0,
    "horsepower": 62.0,
    "peak-rpm": 4800.0,
    "city-mpg": 31,
    "highway-mpg": 37,
    "price": 6918.0
  },
  {
    "symboling": 1,
    "make": "honda",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "sedan",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 96.5,
    "length": 169.1,
    "width": 66.0,
    "height": 51.0,
    "curb-weight": 2293,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 110,
    "fuel-system": "2bbl",
    "bore": 3.15,
    "stroke": 3.58,
    "compression-ratio": 9.1,
    "horsepower": 100.0,
    "peak-rpm": 5500.0,
    "city-mpg": 25,
    "highway-mpg": 31,
    "price": 10345.0
  }
]
```

변경 내용은 단일 레코드인 인덱싱 중에 Cognitive Search에서 생성된 입력을 모델이 수락할 수 있도록 합니다.

```json
{
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
}
```

27~30행을 다음으로 바꿉니다.
```python

    for key, val in data.items():
        input_entry[key].append(decode_nan(val))
```
또한 스크립트가 문자열에서 JSON 개체로 생성하는 출력을 편집해야 합니다. 원본 파일의 return 문(37행)을 다음과 같이 편집합니다.
```python
    output = result.data_frame.values.tolist()
    return {
        "predicted_price": output[0][-1]
    }
```

다음은 입력 형식 및 예측 출력이 변경되어 단일 레코드를 입력으로 받아들이고 예상 가격이 포함된 JSON 개체를 반환하는 업데이트된 `run` 함수입니다.

```python
def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    # data is now a JSON object not a list of JSON objects
    for key, val in data.items():
        input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)
    score_module = ScoreModelModule()
    result, = score_module.run(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        append_or_result_only=True)
    #return json.dumps({"result": result.data_frame.values.tolist()})
    output = result.data_frame.values.tolist()
    # return the last column of the the first row of the dataframe
    return  {
        "predicted_price": output[0][-1]
    }
```
## <a name="register-and-deploy-the-model"></a>모델 등록 및 배포

변경 내용이 저장되면 이제 포털에 모델을 등록할 수 있습니다. 모델 등록을 선택하고 유효한 이름을 제공합니다. 모델 프레임워크에는 `Other`, 프레임워크 이름에는 `Custom`, 프레임워크 버전에는 `1.0`을 선택합니다. `Upload folder` 옵션을 선택하고 업데이트된 `score.py` 및 `conda_env.yaml`이 있는 폴더를 선택합니다.

모델을 선택하고 `Deploy` 동작을 선택합니다. 배포 단계에서는 AKS 유추 클러스터가 프로비전되어 있다고 가정합니다. 컨테이너 인스턴스는 현재 Cognitive Search에서 지원되지 않습니다.
 1. 유효한 엔드포인트 이름을 제공합니다.
2. `Azure Kubernetes Service`의 컴퓨팅 형식을 선택합니다.
3. 유추 클러스터의 컴퓨팅 이름 선택합니다.
4. `enable authentication`을 켜기로 전환합니다.
5. 형식에 `Key-based authentication`을 선택합니다.
6. `entry script file`에 업데이트된 `score.py`를 선택합니다.
7. `conda dependencies file`에 `conda_env.yaml`을 선택합니다.
8. 배포 단추를 선택하여 새 엔드포인트를 배포합니다.

## <a name="integrate-with-cognitive-search"></a>Cognitive Search와 통합

새로 만든 엔드포인트를 Cognitive Search와 통합하려면
1. 단일 자동차 레코드가 포함된 JSON 파일을 Blob 컨테이너에 추가합니다.
2. [데이터 가져오기 워크플로](cognitive-search-quickstart-blob.md)를 사용하여 AI 보강 파이프라인을 구성합니다. `JSON`을 `parsing mode`로 선택해야 합니다.
3. `Add Enrichments` 탭에서 `Extract people names` 단일 기술을 자리 표시자로 선택합니다.
4. 새 필드를 `Edm.Double` 유형의 `predicted_price`라는 인덱스에 추가하고 Retrievable 속성을 true로 설정합니다.
5. 데이터 가져오기 프로세스를 완료합니다.

### <a name="add-the-aml-skill-to-the-skillset"></a>기술 세트에 AML 기술 추가

기술 세트 목록에서 직접 만든 기술 세트를 선택합니다. 이제 사람 식별 기술을 가격을 예측하는 AML 기술로 대체하도록 기술 세트를 편집합니다. 기술 세트 정의(JSON) 탭의 기술 드롭다운에서 `Azure Machine Learning (AML)`을 선택합니다. 작업 영역을 선택합니다. AML 기술이 엔드포인트를 검색하려면 작업 영역과 검색 서비스가 동일한 Azure 구독에 있어야 합니다.
자습서의 앞부분에서 만든 엔드포인트를 선택합니다.
엔드포인트를 배포할 때 구성된 인증 정보 및 URI로 기술이 채워져 있는지 확인합니다. 기술 템플릿을 복사하고 기술 세트의 기술을 바꿉니다.
다음을 수행하도록 기술을 편집합니다.
1. 이름을 유효한 이름으로 설정
2. 설명 추가
3. degreesOfParallelism을 1로 설정
4. 컨텍스트를 `/document`로 설정
5. 필요한 모든 입력에 대한 입력을 설정합니다. (아래 샘플 기술 정의를 참조하세요.)
6. 반환된 예상 가격을 캡처하도록 출력을 설정합니다.

```json
{
      "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
      "name": "AMLdemo",
      "description": "AML Designer demo",
      "context": "/document",
      "uri": "Your AML endpoint",
      "key": "Your AML endpoint key",
      "resourceId": null,
      "region": null,
      "timeout": "PT30S",
      "degreeOfParallelism": 1,
      "inputs": [
        {
          "name": "symboling",
          "source": "/document/symboling"
        },
        {
          "name": "make",
          "source": "/document/make"
        },
        {
          "name": "fuel-type",
          "source": "/document/fuel-type"
        },
        {
          "name": "aspiration",
          "source": "/document/aspiration"
        },
        {
          "name": "num-of-doors",
          "source": "/document/num-of-doors"
        },
        {
          "name": "body-style",
          "source": "/document/body-style"
        },
        {
          "name": "drive-wheels",
          "source": "/document/drive-wheels"
        },
        {
          "name": "engine-location",
          "source": "/document/engine-location"
        },
        {
          "name": "wheel-base",
          "source": "/document/wheel-base"
        },
        {
          "name": "length",
          "source": "/document/length"
        },
        {
          "name": "width",
          "source": "/document/width"
        },
        {
          "name": "height",
          "source": "/document/height"
        },
        {
          "name": "curb-weight",
          "source": "/document/curb-weight"
        },
        {
          "name": "engine-type",
          "source": "/document/engine-type"
        },
        {
          "name": "num-of-cylinders",
          "source": "/document/num-of-cylinders"
        },
        {
          "name": "engine-size",
          "source": "/document/engine-size"
        },
        {
          "name": "fuel-system",
          "source": "/document/fuel-system"
        },
        {
          "name": "bore",
          "source": "/document/bore"
        },
        {
          "name": "stroke",
          "source": "/document/stroke"
        },
        {
          "name": "compression-ratio",
          "source": "/document/compression-ratio"
        },
        {
          "name": "horsepower",
          "source": "/document/horsepower"
        },
        {
          "name": "peak-rpm",
          "source": "/document/peak-rpm"
        },
        {
          "name": "city-mpg",
          "source": "/document/city-mpg"
        },
        {
          "name": "highway-mpg",
          "source": "/document/highway-mpg"
        },
        {
          "name": "price",
          "source": "/document/price"
        }
      ],
      "outputs": [
        {
          "name": "predicted_price",
          "targetName": "predicted_price"
        }
      ]
    }
```
### <a name="update-the-indexer-output-field-mappings"></a>인덱서 출력 필드 매핑 업데이트

인덱서 출력 필드 매핑에 따라 인덱스에 저장되는 보강이 결정됩니다. 인덱서의 출력 필드 매핑 섹션을 아래 코드 조각으로 바꿉니다.

```json
"outputFieldMappings": [
    {
      "sourceFieldName": "/document/predicted_price",
      "targetFieldName": "predicted_price"
    }
  ]
```

이제 인덱서를 실행하고 `predicted_price` 속성이 AML 기술 출력의 결과로 인덱스에 채워졌는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 기술 웹 API 검토](./cognitive-search-custom-skill-web-api.md)

> [사용자 지정 기술을 보강 파이프라인에 추가하는 방법 자세히 알아보기](./cognitive-search-custom-skill-interface.md)

> [AML 기술에 대해 자세히 알아보기](./cognitive-search-tutorial-aml-custom-skill.md)