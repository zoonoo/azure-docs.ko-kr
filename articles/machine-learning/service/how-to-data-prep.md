---
title: Python용 Machine Learning Data Prep SDK를 사용하여 데이터 준비 - Azure
description: Python용 Azure Machine Learning Data Prep SDK를 사용하여 다양한 형식의 데이터를 로드하고, 데이터를 더 유용하게 변환하고, 해당 데이터를 모델이 액세스할 위치에 쓰는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446435"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델링을 위한 데이터 준비

이 문서에서는 Azure Machine Learning Data Prep SDK의 고유 기능과 사용 사례에 대해 알아봅니다. 데이터 준비는 기계 학습 워크플로에서 가장 중요한 부분입니다. 실제 데이터는 중요한 정리 및 변환이 없으면 자주 손상되고 일관성이 없거나 학습 데이터로 사용될 수 없습니다. 원시 데이터에서 오류 및 잘못된 부분을 수정하고, 해결하고자 하는 문제와 관련된 새 기능을 빌드하면 모델의 정확도가 증가됩니다. SDK는 주요 시나리오에 대한 이점을 제공하고 기타 해당 라이브러리와 상호 운용성을 유지하면서 기타 일반 데이터 준비 라이브러리의 사용자에게 친숙하도록 설계되었습니다.

[Azure Machine Learning 데이터 준비 SDK](https://aka.ms/data-prep-sdk)를 사용하여 Python에서 데이터를 준비할 수 있습니다.

## <a name="azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK

[Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)는 Python 라이브러리로서 다음을 제공합니다.

* 유사 항목 그룹화, 예제별 파생 열, 자동 분할, 스마트 파일 읽기 및 오른쪽 스키마 처리와 같은 인텔리전트 시간 절약 변환.
* **코드 변경 사항이 거의 없이** 로컬로 작은 데이터에서 또는 클라우드에서 대규모 데이터에서 작동하는 단일 API.
* 메모리에 로드하는 것이 아니라 데이터를 처리하는 것에 대해 스트리밍 접근 방법을 사용하여 단일 머신에서 보다 효율적으로 크기 조정하는 기능.

### <a name="install-the-sdk"></a>SDK 설치

다음 명령을 사용하여 Python 환경에서 SDK를 설치합니다.

```shell
pip install azureml-dataprep
```

다음 코드 예제를 사용하여 패키지를 가져옵니다.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>예제 및 참조

이 SDK의 기능 및 모듈에 대한 내용은 [Data Prep SDK 참조 문서](https://aka.ms/data-prep-sdk)를 참조하세요.

다음 예제에서는 다음을 포함한 SDK의 고유 기능 중 일부를 강조 표시합니다.

* 자동 파일 유형 탐지
* 인텔리전트 변환
* 요약 통계
* 환경 간 기능


#### <a name="automatic-file-type-detection"></a>자동 파일 유형 탐지

`smart_read_file()` 기능을 사용하여 파일 형식을 지정하지 않고 데이터를 로드합니다. 이 기능은 파일 형식을 자동으로 인식하고 구문 분석합니다.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>인텔리전트 변환

SDK를 사용하여 기능 엔지니어링을 자동화하는 예제 및 참조별 열을 분할 및 파생합니다. `2018-09-15 14:30:00` 값을 갖는 `datetime`이라는 데이터 흐름 개체에 필드가 있다고 가정합니다.

`datetime` 필드를 자동으로 분할하려면 다음 기능을 호출합니다.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

예제 매개 변수를 정의하지 않으면 이 기능은 `datetime` 필드를 `datetime_1` 및 `datetime_2`의 두 개의 새 필드로 자동으로 분할합니다. 결과 값은 각각 `2018-09-15` 및 `14:30:00`입니다. 예제 패턴을 제공할 수도 있으며, SDK는 사용자가 의도한 변환을 예측하고 실행합니다. 동일한 `datetime` 개체를 사용하면 다음 코드는 제공된 예제에 따라 요일에 대한 새 열 `datetime_weekday`를 만듭니다.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>요약 통계

하나의 코드 줄을 사용하여 데이터 흐름에 대한 빠른 요약 통계를 생성할 수 있습니다. 이 메서드는 데이터를 이해하는 간편한 방법 및 데이터를 분산하는 방법을 제공합니다.

```python
dataflow.get_profile()
```

데이터 흐름 개체에서 이 기능을 호출하면 결국 다음 테이블과 같은 출력이 발생합니다.

![요약 통계 출력](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>여러 환경 호환성

SDK는 데이터 흐름 개체를 *모든* Python 환경에서 직렬화하고 열 수 있습니다. 열린 환경은 저장된 환경과 다를 수 있습니다. 이 기능은 Python 환경 간 전송을 쉽게 하고 Azure Machine Learning 모델과 빠른 통합을 가능하게 합니다.

다음 코드를 사용하여 데이터 흐름 개체를 저장합니다.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

다음 코드를 사용하여 모든 환경에서 패키지를 다시 열고 데이터 흐름 개체의 목록을 검색합니다.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>데이터 준비 파이프라인

각 준비 단계에 대한 자세한 예제와 코드를 보려면 다음 방법 가이드를 사용하세요.

1. 다양한 형식의 [데이터 로드](how-to-load-data.md)
2. 더 유용한 구조로 데이터 [변환](how-to-transform-data.md)
3. 모델에 액세스할 수 있는 위치에 해당 데이터 [쓰기](how-to-write-data.md)

![데이터 준비 프로세스](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>다음 단계

Azure Machine Learning Data Prep SDK를 사용하여 데이터 준비의 [예제 노트북](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb)을 검토합니다.

Azure Machine Learning Data Prep SDK [참조 문서](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
