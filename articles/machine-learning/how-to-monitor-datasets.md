---
title: 데이터 집합의 데이터 드리프트 분석 및 모니터링 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 데이터 집합을 만들고, 데이터 집합의 데이터 드리프트를 모니터링 하 고, 경고를 설정 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d60a963f8ad4b29d3c282d30e6aca9973208860b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905143"
---
# <a name="detect-data-drift-preview-on-datasets"></a>데이터 집합에서 데이터 드리프트 (미리 보기) 검색


> [!IMPORTANT]
> 데이터 집합에서 데이터 드리프트를 검색 하는 것은 현재 공개 미리 보기 상태입니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

드리프트가 높을 때 데이터 드리프트를 모니터링 하 고 경고를 설정 하는 방법을 알아봅니다.  

Azure Machine Learning 데이터 집합 모니터 (미리 보기)를 사용 하 여 다음을 수행할 수 있습니다.
* **데이터의 드리프트를 분석** 하 여 시간이 지남에 따라 변경 되는 방식을 파악 합니다.
* 학습 및 데이터 집합 처리 간의 차이점에 대 한 **모델 데이터를 모니터링** 합니다.  먼저 [배포 된 모델에서 모델 데이터를 수집](how-to-enable-data-collection.md)합니다.
* 모든 기준선과 대상 데이터 집합 간의 차이점에 대해 **새 데이터를 모니터링** 합니다.
* **데이터의 기능을 프로 파일링** 하 여 시간에 따른 통계 속성의 변화를 추적 합니다.
* 잠재적 문제에 대 한 조기 경고에 대 한 **경고를 데이터 드리프트로 설정** 합니다. 

[Azure Machine learning 데이터 집합](how-to-create-register-datasets.md) 을 사용 하 여 모니터를 만듭니다. 데이터 집합에는 타임 스탬프 열이 포함 되어야 합니다.

Python SDK 또는 Azure Machine Learning studio를 사용 하 여 데이터 드리프트 메트릭을 볼 수 있습니다.  다른 메트릭 및 정보는 Azure Machine Learning 작업 영역과 연결 된 [Azure 애플리케이션 insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 리소스를 통해 제공 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터 집합 모니터를 만들고 사용 하려면 다음이 필요 합니다.
* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.
* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)있습니다.
* 데이터의 파일 경로, 파일 이름 또는 열에 지정 된 타임 스탬프를 사용 하는 구조화 된 (테이블 형식) 데이터입니다.

## <a name="what-is-data-drift"></a>데이터 드리프트 란?

데이터 드리프트는 시간이 지남에 따라 모델 정확성이 저하 되는 가장 중요 한 이유 중 하나입니다.  기계 학습 모델의 경우 데이터 드리프트는 모델의 성능 저하를 야기 하는 모델 입력 데이터의 변경 내용입니다.  데이터 드리프트 모니터링은 이러한 모델 성능 문제를 감지 하는 데 도움이 됩니다.

데이터 드리프트의 원인은 다음과 같습니다.

- 대체 중인 센서와 같이 측정 단위를 인치에서 센티미터 단위로 변경 하는 업스트림 프로세스 변경 내용 
- 손상 된 센서와 같은 데이터 품질 문제는 항상 0을 읽습니다.
- 계절으로 변화 하는 평균 온도와 같이 데이터의 자연 드리프트
- 기능 간의 관계를 변경 하거나 보조를 전환 합니다. 

Azure Machine Learning는 비교 되는 데이터 집합의 복잡성을 추상화 하는 단일 메트릭을 계산 하 여 드리프트 검색을 단순화 합니다.  이러한 데이터 집합에는 수백 개의 기능과 수십 개의 행이 있을 수 있습니다. 드리프트가 검색 되 면 드리프트를 발생 시키는 기능을 드릴 다운 합니다.  그런 다음 기능 수준 메트릭을 검사 하 여 드리프트의 근본 원인을 디버그 하 고 격리 합니다.

이 하향식 접근 방법을 사용 하면 기존의 규칙 기반 기술 대신 데이터를 쉽게 모니터링할 수 있습니다. 허용 되는 데이터 범위 또는 허용 되는 고유 값과 같은 규칙 기반 기술은 시간이 많이 걸리고 오류가 발생할 수 있습니다.

Azure Machine Learning 데이터 집합 모니터를 사용 하 여 데이터 드리프트를 검색 하 고 경고 합니다.
  
### <a name="dataset-monitors"></a>데이터 집합 모니터 

데이터 집합 모니터를 사용 하 여 다음을 수행할 수 있습니다.

* 데이터 집합의 새 데이터에 대 한 데이터 드리프트를 검색 하 고 경고 합니다.
* 드리프트에 대 한 기록 데이터를 분석 합니다.
* 시간이 지남에 따라 새 데이터를 프로 파일링 합니다.

데이터 드리프트 알고리즘은 전체 데이터 변경을 측정 하 고 추가 조사를 담당 하는 기능에 대 한 표시를 제공 합니다. 데이터 집합 모니터는 데이터 집합에서 새 데이터를 프로 파일링 하 여 다양 한 메트릭을 생성 `timeseries` 합니다. 

사용자 지정 경고는 [Azure 애플리케이션 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)를 통해 모니터에 의해 생성 되는 모든 메트릭에 설정할 수 있습니다. 데이터 집합 모니터를 사용 하 여 데이터 문제를 빠르게 파악 하 고 가능한 원인을 파악 하 여 문제를 디버그 하는 시간을 줄일 수 있습니다.  

개념적으로 Azure Machine Learning 데이터 집합 모니터를 설정 하는 세 가지 기본 시나리오가 있습니다.

시나리오 | 설명
---|---
학습 데이터에서 드리프트에 대 한 모델의 처리 데이터 모니터링 | 이 시나리오의 결과는 서비스 하는 데이터가 학습 데이터에서 상태가 때 모델 정확성이 저하 되므로 모델 정확도에 대 한 프록시 모니터링으로 해석 될 수 있습니다.
이전 기간에서 드리프트에 대 한 시계열 데이터 집합을 모니터링 합니다. | 이 시나리오는 보다 일반적 이며, 업스트림 또는 모델 빌드 다운스트림과 관련 된 데이터 집합을 모니터링 하는 데 사용할 수 있습니다.  대상 데이터 집합에는 타임 스탬프 열이 있어야 합니다. 기준 데이터 집합은 대상 데이터 집합에 공통 된 기능이 포함 된 모든 테이블 형식 데이터 집합이 될 수 있습니다.
이전 데이터에 대해 분석을 수행 합니다. | 이 시나리오는 데이터 집합 모니터의 설정에서 기록 데이터를 이해 하 고 결정을 내리는 데 사용할 수 있습니다.

데이터 집합 모니터는 다음 Azure 서비스에 따라 달라 집니다.

|Azure 서비스  |설명  |
|---------|---------|
| *데이터 세트* | 드리프트는 Machine Learning 데이터 집합을 사용 하 여 학습 데이터를 검색 하 고 모델 학습을 위해 데이터를 비교 합니다.  데이터의 프로필 생성은 최소, 최대, 고유 값, 고유 값 수와 같은 보고 된 메트릭 중 일부를 생성 하는 데 사용 됩니다. |
| *Azureml 파이프라인 및 계산* | 드리프트 계산 작업은 azureml 파이프라인에서 호스팅됩니다.  작업은 요청 시 또는 드리프트 모니터 생성 시간에 구성 된 계산에서 실행 되도록 일정에 따라 트리거됩니다.
| *애플리케이션 인사이트*| 드리프트는 machine learning 작업 영역에 속하는 Application Insights 메트릭을 내보냅니다.
| *Azure Blob 스토리지*| 드리프트는 json 형식의 메트릭을 Azure blob storage로 내보냅니다.

## <a name="how-dataset-monitors-data"></a>데이터 집합에서 데이터를 모니터링 하는 방법

데이터 드리프트를 모니터링 하려면 Machine Learning 데이터 집합을 사용 합니다. 기준선 데이터 집합을 지정 합니다. 일반적으로 모델에 대 한 학습 데이터 집합을 지정 합니다. 대상 데이터 집합-일반적으로 입력 데이터를 모델링 하는 것은 시간에 따라 기준선 데이터 집합에 비교 됩니다. 이 비교는 대상 데이터 집합에 타임 스탬프 열이 지정 되어 있어야 함을 의미 합니다.

## <a name="create-target-dataset"></a>대상 데이터 집합 만들기

대상 데이터 집합에는 `timeseries` 데이터의 열 또는 파일의 경로 패턴에서 파생 된 가상 열에서 타임 스탬프 열을 지정 하 여 해당 데이터 집합에 대 한 특성 집합이 필요 합니다. [PYTHON SDK](#sdk-dataset) 또는 [Azure Machine Learning studio](#studio-dataset)를 통해 타임 스탬프를 사용 하 여 데이터 집합을 만듭니다. 데이터 집합에 특성을 추가 하려면 "timestamp"를 나타내는 열을 지정 해야 합니다 `timeseries` . 데이터가 ' {yyyy/MM/dd} '와 같은 시간 정보를 사용 하 여 폴더 구조로 분할 된 경우 경로 패턴 설정을 통해 가상 열을 만들고이를 "파티션 타임 스탬프"로 설정 하 여 시계열 기능의 중요도를 향상 시킵니다.

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)클래스 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 메서드는 데이터 집합에 대 한 타임 스탬프 열을 정의 합니다.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

데이터 집합의 특성 사용에 대 한 전체 예제는 `timeseries` [예제 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) 또는 [데이터 집합 SDK 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)를 참조 하세요.

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure Machine Learning Studio

Azure Machine Learning studio를 사용 하 여 데이터 집합을 만드는 경우 데이터 경로에 타임 스탬프 정보가 포함 되 고, 데이터가 있는 모든 하위 폴더를 포함 하 고, 파티션 형식을 설정 해야 합니다.

다음 예제에서는 *NoaaIsdFlorida/2019* 하위 폴더 아래의 모든 데이터를 가져오고 파티션 형식은 타임 스탬프의 연도, 월 및 일을 지정 합니다.

[![파티션 형식](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**스키마** 설정에서 지정 된 데이터 집합의 가상 또는 실제 열에서 타임 스탬프 열을 지정 합니다.

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="타임 스탬프 설정":::

여기에 나와 있는 것 처럼 데이터가 날짜별로 분할 된 경우 partition_timestamp를 지정할 수도 있습니다.  이렇게 하면 보다 효율적인 날짜 처리를 수행할 수 있습니다.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="파티션 타임 스탬프":::


## <a name="create-dataset-monitors"></a>데이터 집합 모니터 만들기

새 데이터 집합에 대 한 데이터 드리프트를 검색 하 고 경고 하는 데이터 집합 모니터를 만듭니다.  [PYTHON SDK](#sdk-monitor) 또는 [Azure Machine Learning studio](#studio-monitor)중 하나를 사용 합니다.

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

전체 정보는 [데이터 드리프트에 대 한 PYTHON SDK 참조 설명서](/python/api/azureml-datadrift/azureml.datadrift) 를 참조 하세요. 

다음 예제에서는 Python SDK를 사용 하 여 데이터 집합 모니터를 만드는 방법을 보여 줍니다.

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

데이터 집합 및 데이터 드리프트 탐지기를 설정 하는 전체 예제는 `timeseries` [예제 노트북](https://aka.ms/datadrift-notebook)을 참조 하세요.

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Azure Machine Learning studio

1. [스튜디오 홈페이지](https://ml.azure.com)로 이동 합니다.
1. 왼쪽의 **데이터 집합** 탭을 선택 합니다. 
1. **데이터 집합 모니터**를 선택 합니다.
   ![모니터 목록](./media/how-to-monitor-datasets/monitor-list.png)

1. **+ 모니터 만들기** 단추를 클릭 하 고 **다음**을 클릭 하 여 마법사를 계속 진행 합니다.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="모니터 만들기 마법사":::

* **대상 데이터 집합을 선택**합니다.  대상 데이터 집합은 데이터 드리프트를 위해 분석 되는 타임 스탬프 열이 지정 된 테이블 형식 데이터 집합입니다. 대상 데이터 집합은 기준선 데이터 집합과 공통적으로 사용 되는 기능을 포함 해야 하며 `timeseries` 새 데이터가 추가 되는 데이터 집합 이어야 합니다. 대상 데이터 집합의 기록 데이터를 분석 하거나 새 데이터를 모니터링할 수 있습니다.

* **기준 데이터 집합을 선택 합니다.**  시간 경과에 따른 대상 데이터 집합 비교를 위한 기준으로 사용할 테이블 형식 데이터 집합을 선택 합니다.  기준선 데이터 집합에는 대상 데이터 집합과 공통 된 기능이 있어야 합니다.  대상 데이터 집합의 조각을 사용할 시간 범위를 선택 하거나 기준선으로 사용할 별도의 데이터 집합을 지정 하십시오.

* **모니터 설정**  이러한 설정은 예약 된 데이터 집합 모니터 파이프라인에 대 한 것으로, 생성 됩니다. 

    | 설정 | 설명 | 팁 | 변경 가능 | 
    | ------- | ----------- | ---- | ------- |
    | 이름 | 데이터 집합 모니터의 이름입니다. | | 예 |
    | 기능 | 시간에 따른 데이터 드리프트를 분석 하는 기능 목록입니다. | 개념 드리프트를 측정 하는 모델의 출력 기능으로 설정 합니다. 시간이 지남에 따라 자연스럽 게 드리프트 하는 기능 (월, 연도, 인덱스 등)은 포함 되지 않습니다. 기능 목록을 조정한 후에는 백필 및 기존 데이터 드리프트 모니터를 사용할 수 있습니다. | 예 | 
    | 컴퓨팅 대상 | 계산 대상을 Azure Machine Learning 하 여 데이터 집합 모니터 작업을 실행 합니다. | | 예 | 
    | 사용 | 데이터 집합 모니터 파이프라인에서 일정을 사용 하거나 사용 하지 않도록 설정 | 백필 설정을 사용 하 여 기록 데이터를 분석 하는 일정을 사용 하지 않도록 설정 합니다. 데이터 집합 모니터를 만든 후에 사용할 수 있습니다. | 예 | 
    | 빈도 | 백필을 실행 하는 경우 파이프라인 작업을 예약 하 고 기록 데이터를 분석 하는 데 사용 되는 빈도입니다. 옵션에는 매일, 매주 또는 매월이 포함 됩니다. | 각 실행은 빈도에 따라 대상 데이터 집합의 데이터를 비교 합니다. <li>매일: 대상 데이터 집합의 최근 완료 날짜를 기준선과 비교 합니다. <li>매주: 대상 데이터 집합에서 기준선과 가장 최근의 전체 주 (월요일-일요일) 비교 <li>월간: 대상 데이터 집합에서 가장 최근의 전체 월을 기준선과 비교 | 예 | 
    | 대기 시간 | 데이터 집합에 데이터가 도착할 때까지 걸리는 시간입니다. 예를 들어 데이터 집합이 캡슐화 하는 SQL DB에 데이터가 도착할 때까지 3 일이 걸리면 대기 시간을 72으로 설정 합니다. | 데이터 집합 모니터를 만든 후에는 변경할 수 없습니다. | 아니요 | 
    | 이메일 주소 | 데이터 드리프트 백분율 임계값 위반을 기준으로 경고에 대 한 전자 메일 주소입니다. | 전자 메일은 Azure Monitor를 통해 전송 됩니다. | 예 | 
    | 임계값 | 전자 메일 경고에 대 한 데이터 드리프트 백분율 임계값입니다. | 작업 영역에 연결 된 Application Insights 리소스의 다른 여러 메트릭에 대해 추가 경고 및 이벤트를 설정할 수 있습니다. | 예 |

마법사를 완료 하면 결과 데이터 집합 모니터가 목록에 표시 됩니다. 이를 선택 하 여 해당 모니터의 세부 정보 페이지로 이동 합니다.

## <a name="understand-data-drift-results"></a>데이터 드리프트 결과 이해

이 섹션에서는 Azure studio의 **데이터 집합**  /  **데이터 집합 모니터** 페이지에 있는 데이터 집합 모니터링 결과를 보여 줍니다.  설정을 업데이트 하 고이 페이지의 특정 기간에 대 한 기존 데이터를 분석할 수 있습니다.  

데이터 드리프트의 규모와 추가 조사 될 기능의 강조 표시로 최상위 수준 정보를 시작 합니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="드리프트 개요":::


| 메트릭 | 설명 | 
| ------ | ----------- | 
| 데이터 드리프트 크기 | 시간에 따른 기준선 및 대상 데이터 집합 간의 드리프트 비율입니다. 0에서 100 사이의 범위에서 0은 동일한 데이터 집합을 나타내고 100은 Azure Machine Learning 데이터 드리프트 모델이 두 데이터 집합을 완전히 구분할 수 있음을 나타냅니다. 이 크기를 생성 하는 데 사용 되는 기계 학습 기술로 인해 측정 된 정확한 비율의 노이즈가 예상 됩니다. | 
| Top 유동 기능 | 데이터베이스가 드리프트 가장 많이 영향을 주는 데이터 집합의 기능을 보여 줍니다. 따라서 최대 드리프트 크기 메트릭에 영향을 줍니다. Shift 키로 인해 기능의 기본 분포는 상대적으로 높은 기능 중요도를 갖도록 변경 해야 하는 것은 아닙니다. |
| 임계값 | 설정 된 임계값을 초과 하는 데이터 드리프트는 경고를 트리거합니다. 모니터 설정에서 구성할 수 있습니다. | 

### <a name="drift-magnitude-trend"></a>드리프트 크기 추세

지정 된 기간에 데이터 집합이 대상 데이터 집합과 어떻게 다른 지 확인 합니다.  100%에 가까울수록 두 데이터 집합이 다릅니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="드리프트 크기 추세":::

### <a name="drift-magnitude-by-features"></a>기능의 드리프트 크기

이 섹션에는 시간에 따른 기타 통계 뿐만 아니라 선택한 기능의 배포 변경 내용에 대 한 기능 수준 정보가 포함 되어 있습니다.

또한 대상 데이터 집합은 시간이 지남에 따라 프로 파일링 됩니다. 각 기능의 기준 분포 간 통계 거리는 시간에 따른 대상 데이터 집합에 비해 비교 됩니다.  개념적으로이는 데이터 드리프트 크기와 비슷합니다.  그러나이 통계 거리는 모든 기능이 아니라 개별 기능에 대 한 것입니다. Min, max 및 mean도 사용할 수 있습니다.

Azure Machine Learning studio에서 그래프의 막대를 클릭 하 여 해당 날짜에 대 한 기능 수준 세부 정보를 확인 합니다. 기본적으로 기본 데이터 집합의 배포와 동일한 기능의 가장 최근 실행 배포가 표시 됩니다.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="기능의 드리프트 크기":::

이러한 메트릭은 개체의 메서드를 통해 Python SDK에서 검색할 수도 있습니다 `get_metrics()` `DataDriftDetector` .

### <a name="feature-details"></a>기능 정보

마지막으로 아래로 스크롤하여 각 개별 기능에 대 한 세부 정보를 확인 합니다.  차트 위의 드롭다운을 사용 하 여 기능을 선택 하 고 보려는 메트릭을 추가로 선택 합니다.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="숫자 기능 그래프 및 비교":::

차트의 메트릭은 기능의 유형에 따라 달라 집니다.

* 숫자 기능

    | 메트릭 | 설명 |  
    | ------ | ----------- |  
    | Wasserstein 거리 | 기준 배포를 대상 배포로 변환 하기 위한 최소 작업 양입니다. |
    | 평균값 | 기능의 평균 값입니다. |
    | 최솟값 | 기능의 최소값입니다. |
    | 최댓값 | 기능의 최 댓 값입니다. |

* 범주 기능
    
    | 메트릭 | 설명 |  
    | ------ | ----------- |  
    | 유클리드 기하학과 거리     |  범주 열에 대해 계산 됩니다.유클리드 distance는 두 데이터 집합에서 동일한 범주 열의 경험적 배포에서 생성 된 두 벡터에 대해 계산 됩니다.0은 경험적 분포의 차이가 없음을 나타냅니다.더 많은 것이 0에서 데이터베이스가 드리프트이 열에는 더 많은 열이 있습니다.이 메트릭의 시계열 그림에서 추세를 관찰 하 여 유동 기능을 발견할 수 있습니다.  |
    | 고유한 값 | 기능의 고유 값 (카디널리티) 수입니다. |

이 차트에서 단일 날짜를 선택 하 여 표시 된 기능에 대 한 목표와이 날짜 간의 기능 분포를 비교 합니다. 숫자 기능의 경우 두 확률 분포가 표시 됩니다.  기능이 숫자인 경우 가로 막대형 차트가 표시 됩니다.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="대상과 비교할 날짜를 선택 합니다.":::

## <a name="metrics-alerts-and-events"></a>메트릭, 경고 및 이벤트

메트릭은 machine learning 작업 영역과 연결 된 [Azure 애플리케이션 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 리소스에서 쿼리할 수 있습니다. 사용자 지정 경고 규칙 및 작업 그룹에 대 한 설정을 포함 하 여 전자 메일/a p i/푸시/음성 또는 Azure 함수와 같은 작업을 트리거하는 Application Insights의 모든 기능에 액세스할 수 있습니다. 자세한 내용은 전체 Application Insights 설명서를 참조 하세요. 

시작 하려면 [Azure Portal](https://portal.azure.com) 로 이동 하 여 작업 영역의 **개요** 페이지를 선택 합니다.  연결 된 Application Insights 리소스는 맨 오른쪽에 있습니다.

[![Azure 포털 개요](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

왼쪽 창의 모니터링 아래에서 로그 (분석)를 선택 합니다.

![Application insights 개요](./media/how-to-monitor-datasets/ai-overview.png)

데이터 집합 모니터 메트릭은로 저장 됩니다 `customMetrics` . 데이터 집합 모니터를 설정한 후 쿼리를 작성 하 고 실행 하 여 해당 쿼리를 볼 수 있습니다.

[![Log analytics 쿼리](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

경고 규칙을 설정 하는 메트릭을 식별 한 후 새 경고 규칙을 만듭니다.

![새 경고 규칙](./media/how-to-monitor-datasets/alert-rule.png)

기존 작업 그룹을 사용 하거나 새 작업 그룹을 만들어 집합 조건이 충족 될 때 수행할 동작을 정의할 수 있습니다.

![새 작업 그룹](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>다음 단계

* 데이터 집합 모니터를 설정 하려면 [Azure Machine Learning studio](https://ml.azure.com) 또는 [Python 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) 을 시작 합니다.
* [Azure Kubernetes Service에 배포 된 모델](how-to-monitor-data-drift.md)에서 데이터 드리프트를 설정 하는 방법을 참조 하세요.
* [Event grid](how-to-use-event-grid.md)를 사용 하 여 데이터 집합 드리프트 모니터를 설정 합니다. 
* 문제가 발생 하는 경우 일반적인 [문제 해결 팁](resource-known-issues.md#data-drift) 을 참조 하세요.
