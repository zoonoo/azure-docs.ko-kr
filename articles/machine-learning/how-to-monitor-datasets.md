---
title: 데이터 세트의 데이터 드리프트 분석 및 모니터링(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 데이터 집합 을 생성(미리 보기), 데이터 집합의 데이터 드리프트 모니터링 및 경고 설정.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682717"
---
# <a name="detect-data-drift-preview-on-datasets"></a>데이터 집합에서 데이터 드리프트(미리 보기) 감지
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 데이터 집합 모니터(미리 보기)를 만들고, 데이터 드리프트 및 데이터 집합의 통계 적 변화를 모니터링하고, 경고를 설정하는 방법을 알아봅니다.

Azure 기계 학습 데이터 집합 모니터를 사용하면 다음을 수행할 수 있습니다.
* **데이터의 드리프트를 분석하여** 시간이 지남에 따라 어떻게 변하는지 파악합니다.
* 학습과 데이터 집합 제공 간의 차이점을 모델 데이터를 **모니터링합니다.**
* **기준과** 대상 데이터 집합 간의 차이점이 있는지 새 데이터를 모니터링합니다.
* **데이터의 프로파일 기능을** 통해 시간이 지남에 따라 통계 속성이 어떻게 변하는지 추적할 수 있습니다.
* 잠재적인 문제에 대한 조기 경고에 대한 **데이터 드리프트에** 대한 경고를 설정합니다. 

메트릭 및 인사이트는 Azure 기계 학습 작업 영역과 연결된 [Azure 응용 프로그램 통찰력](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 리소스를 통해 사용할 수 있습니다.

> [!Important]
> SDK를 사용하여 데이터 드리프트를 모니터링하는 것은 모든 버전에서 사용할 수 있으며 웹의 스튜디오를 통해 데이터 드리프트를 모니터링하는 것은 엔터프라이즈 버전일 뿐입니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터 집합 모니터를 만들고 작업하려면 다음이 필요합니다.
* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure 기계 학습 작업 영역](how-to-manage-workspace.md).
* Azureml 데이터 집합 패키지를 포함하는 [파이썬용 Azure 기계 학습 SDK가 설치되었습니다.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* 파일 경로, 파일 이름 또는 데이터의 열에 지정된 타임스탬프가 있는 구조화(테이블 형식) 데이터입니다.

## <a name="what-is-data-drift"></a>데이터 드리프트란 무엇입니까?

기계 학습의 맥락에서 데이터 드리프트는 모델 성능 저하로 이어지는 모델 입력 데이터의 변화입니다. 이는 시간이 지남에 따라 모델 정확도가 저하되는 가장 큰 이유 중 하나이므로 데이터 드리프트를 모니터링하면 모델 성능 문제를 감지하는 데 도움이 됩니다.

데이터 드리프트의 원인은 다음과 같습니다. 

- 측정 단위를 인치에서 센티미터로 변경하는 센서 교체와 같은 업스트림 프로세스가 변경됩니다. 
- 깨진 센서와 같은 데이터 품질 문제는 항상 0을 읽습니다.
- 계절에 따라 평균 온도 가 변하는 등의 데이터에서 자연 스리프트.
- 피처 또는 공동 변이 사이의 관계 변경. 

Azure Machine Learning 데이터 집합 모니터를 사용하면 시간이 지남에 따라 데이터 집합에서 데이터 드리프트 검색을 지원하는 경고를 설정할 수 있습니다. 

### <a name="dataset-monitors"></a>데이터 집합 모니터 

데이터 집합 모니터를 만들어 데이터 집합의 새 데이터에 대한 데이터 드리프트를 감지하고 경고하고, 기록 데이터를 분석하여 드리프트를 분석하고, 시간에 따라 새 데이터를 프로파일할 수 있습니다. 데이터 드리프트 알고리즘은 데이터의 변화에 대한 전반적인 측정값과 추가 조사를 담당하는 기능의 표시를 제공합니다. 데이터 집합 모니터는 데이터 집합의 새 데이터를 프로파일링하여 여러 가지 다른 메트릭을 `timeseries` 생성합니다. 사용자 지정 경고는 [Azure 응용 프로그램 인사이트를](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)통해 모니터에서 생성된 모든 메트릭에 대해 설정할 수 있습니다. 데이터 집합 모니터를 사용하여 데이터 문제를 신속하게 파악하고 발생할 수 있는 원인을 식별하여 문제를 디버깅하는 시간을 줄일 수 있습니다.  

개념적으로 Azure 기계 학습에서 데이터 집합 모니터를 설정하기 위한 세 가지 기본 시나리오가 있습니다.

시나리오 | 설명
---|---
모델의 학습 데이터에서 드리프트에 대한 모델의 게재 데이터 모니터링 | 이 시나리오의 결과는 학습 데이터에서 서빙 데이터가 드리프트하는 경우 모델 정확도가 저하된다는 점을 감안할 때 모델의 정확도에 대한 프록시를 모니터링하는 것으로 해석할 수 있습니다.
이전 기간의 드리프트에 대한 타임계열 데이터 집합 모니터링. | 이 시나리오는 보다 일반적이며 모델 빌드의 업스트림 또는 다운스트림과 관련된 데이터 집합을 모니터링하는 데 사용할 수 있습니다.  대상 데이터 집합에는 타임스탬프 열이 있어야 하며 기준 데이터 집합은 대상 데이터 집합과 공통된 기능이 있는 모든 테이블 형식 데이터 집합일 수 있습니다.
과거 데이터에 대한 분석 수행. | 이 시나리오는 기록 데이터를 이해하고 데이터 집합 모니터에 대한 설정에서 의사 결정을 알리는 데 사용할 수 있습니다.

## <a name="how-dataset-can-monitor-data"></a>데이터 집합이 데이터를 모니터링하는 방법

Azure 기계 학습을 사용하여 데이터 드리프트는 데이터 집합을 통해 모니터링됩니다. 데이터 드리프트를 모니터링하기 위해 모델의 학습 데이터 집합인 기준 데이터 집합이 지정됩니다. 일반적으로 입력 데이터를 모델링하는 대상 데이터 집합은 시간이 지남에 따라 기준 데이터 집합과 비교됩니다. 이 비교는 대상 데이터 집합에 타임스탬프 열이 지정되어 있어야 함을 의미합니다.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>대상 `timeseries` 데이터 집합에서 특성 설정

대상 데이터 집합은 데이터의 `timeseries` 열또는 파일의 경로 패턴에서 파생된 가상 열에서 타임스탬프 열을 지정하여 특성을 설정해야 합니다. 이것은 파이썬 SDK 또는 Azure 기계 학습 스튜디오를 통해 수행 할 수 있습니다. 데이터 집합에 특성을 추가하려면 `timeseries` "미세 한 입자" 타임스탬프를 나타내는 열을 지정해야 합니다. '{yyyy/MM/dd}'와 같은 시간 정보가 있는 폴더 구조로 데이터가 분할된 경우 경로 패턴 설정을 통해 가상 열을 만들고 "거친 그레인" 타임스탬프로 설정하여 열열 기능의 중요성을 향상시킬 수 있습니다. 

#### <a name="python-sdk"></a>Python SDK

클래스의 [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 메서드는 데이터 집합에 대한 타임스탬프 열을 정의합니다. 

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

데이터 집합의 `timeseries` 특성을 사용하는 전체 예제는 [예제 전자 필기장](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) 또는 데이터 집합 [SDK 설명서를](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)참조하십시오.

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Azure Machine Learning 스튜디오를 사용하여 데이터 집합을 만드는 경우 데이터에 대한 경로에 타임스탬프 정보가 포함되어 있는지 확인하고, 데이터가 포함된 모든 하위 폴더를 포함하고, 파티션 형식을 설정합니다. 

다음 예제에서는 하위 폴더 *NoaaIsdFlorida/2019* 아래의 모든 데이터가 수행되고 파티션 형식은 타임스탬프의 연도, 월 및 일을 지정합니다. 

[![파티션 형식](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**스키마** 설정에서 지정된 데이터 집합에서 가상 또는 실제 열의 타임스탬프 열을 지정합니다.

![타임스탬프](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>데이터 집합 모니터 설정

지정된 타임스탬프 설정으로 데이터 집합을 만들면 데이터 집합 모니터를 구성할 준비가 된 것입니다.

다양한 데이터 집합 모니터 설정은 **기본 정보, 모니터 설정** 및 채우기 **설정의**세 그룹으로 나뉩니다.

### <a name="basic-info"></a>기본 정보

이 테이블에는 데이터 집합 모니터에 사용되는 기본 설정이 포함되어 있습니다.

| 설정 | Description | 팁 | 변경 가능 | 
| ------- | ----------- | ---- | ------- | 
| 이름 | 데이터 집합 모니터의 이름입니다. | | 예 |
| 기준선 데이터 집합 | 시간에 따라 대상 데이터 집합을 비교하기 위한 기준선으로 사용되는 테이블 형식 데이터 집합입니다. | 기준 데이터 집합에는 대상 데이터 집합과 공통된 피쳐가 있어야 합니다. 일반적으로 기준은 모델의 학습 데이터 집합 또는 대상 데이터 집합의 조각으로 설정되어야 합니다. | 예 |
| 대상 데이터 세트 | 데이터 드리프트에 대해 분석할 타임스탬프 열이 지정된 테이블 형식 데이터 집합입니다. | 대상 데이터 집합에는 기준 데이터 집합과 공통된 기능이 `timeseries` 있어야 하며 새 데이터가 추가되는 데이터 집합이어야 합니다. 대상 데이터 집합의 기록 데이터를 분석하거나 새 데이터를 모니터링할 수 있습니다. | 예 | 
| 빈도 | 파이프라인 작업을 예약하고 채우기를 실행하는 경우 기록 데이터를 분석하는 데 사용되는 빈도입니다. 옵션은 일일, 주간 또는 월별을 포함합니다. | 기준선과 유사한 데이터 크기를 포함하도록 이 설정을 조정합니다. | 예 | 
| 기능 | 시간이 지남에 따라 데이터 드리프트에 대해 분석할 기능 목록입니다. | 개념 드리프트를 측정하기 위해 모델의 출력 피쳐로 설정합니다. 시간이 지남에 따라 자연스럽게 표류하는 기능(월, 연도, 인덱스 등)은 포함하지 마십시오. 피처 목록을 조정한 후 백필 및 기존 데이터 드리프트 모니터를 채울 수 있습니다. | 예 | 
| 컴퓨팅 대상 | Azure 기계 학습은 데이터 집합 모니터 작업을 실행하기 위해 대상을 계산합니다. | | 예 | 

### <a name="monitor-settings"></a>모니터 설정

이러한 설정은 생성될 예약된 데이터 집합 모니터 파이프라인에 대한 것입니다. 

| 설정 | Description | 팁 | 변경 가능 | 
| ------- | ----------- | ---- | ------- |
| 사용 | 데이터 집합 모니터 파이프라인의 일정 사용 또는 비활성화 | 일정을 비활성화하여 채우기 설정을 사용하여 기록 데이터를 분석합니다. 데이터 집합 모니터를 만든 후 활성화할 수 있습니다. | 예 | 
| 대기 시간 | 시간, 시간, 시간, 데이터 집합에 데이터가 도착 하는 데 걸립니다. 예를 들어 데이터가 SQL DB에 도착하는 데 3일이 걸리는 경우 데이터 집합이 캡슐화되어 있는 경우 대기 시간을 72로 설정합니다. | 데이터 집합 모니터를 만든 후에는 변경할 수 없습니다. | 예 | 
| 메일 주소 | 데이터 드리프트 백분율 임계값의 위반을 기반으로 경고를 위한 전자 메일 주소입니다. | 전자 메일은 Azure 모니터를 통해 전송됩니다. | 예 | 
| 임계값 | 이메일 알림에 대한 데이터 드리프트 백분율 임계값입니다. | 추가 경고 및 이벤트는 작업 영역의 연결된 Application Insights 리소스의 다른 많은 메트릭에서 설정할 수 있습니다. | 예 | 

### <a name="backfill-settings"></a>채우기 설정

이러한 설정은 데이터 드리프트 메트릭에 대한 이전 데이터에 대한 채우기를 실행하기 위한 것입니다.

| 설정 | Description | 팁 |
| ------- | ----------- | ---- |
| 시작 날짜 | 채우기 작업의 시작 날짜입니다. | | 
| 종료 날짜 | 채우기 작업의 종료 날짜입니다. | 종료 날짜는 시작 날짜로부터 31*빈도 단위를 초과할 수 없습니다. 기존 데이터 집합 모니터에서 메트릭을 다시 채워 기록 데이터를 분석하거나 메트릭을 업데이트된 설정으로 대체할 수 있습니다. |

## <a name="create-dataset-monitors"></a>데이터 집합 모니터 만들기 

Azure Machine Learning 스튜디오 또는 Python SDK를 사용하여 새 데이터 집합에서 데이터 드리프트를 감지하고 경고하는 데이터 집합 모니터를 만듭니다. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

데이터 집합 모니터에 경고를 설정하려면 모니터를 만들려는 데이터 집합이 포함된 작업 영역에 엔터프라이즈 에디션 기능이 있어야 합니다. 

작업 영역 기능이 확인된 후 스튜디오의 홈페이지로 이동하여 왼쪽의 데이터 집합 탭을 선택합니다. 데이터 집합 모니터를 선택합니다.

![모니터 목록](./media/how-to-monitor-datasets/monitor-list.png)

**모니터 만들기** 버튼을 클릭하고 **다음**을 클릭하여 마법사를 계속 진행합니다.

![마법사](./media/how-to-monitor-datasets/wizard.png)

결과 데이터 집합 모니터가 목록에 나타납니다. 해당 모니터의 세부 정보 페이지로 이동하려면 선택합니다.

### <a name="from-python-sdk"></a>파이썬 SDK 출신

자세한 내용은 [데이터 드리프트에 대한 Python SDK 참조 문서를](/python/api/azureml-datadrift/azureml.datadrift) 참조하십시오. 

다음 예제에서는 Python SDK를 사용하여 데이터 집합 모니터를 만드는 방법을 보여 주며

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

데이터 집합 및 데이터 드리프트 검출기 설정의 전체 예는 예제 노트북 을 참조하십시오. [example notebook](https://aka.ms/datadrift-notebook) `timeseries`

## <a name="understanding-data-drift-results"></a>데이터 드리프트 결과 이해

데이터 모니터는 드리프트 개요 및 피쳐 세부 사항이라는 두 가지 결과 그룹을 생성합니다. 다음 애니메이션은 선택한 피처 및 메트릭을 기반으로 사용 가능한 드리프트 모니터 차트를 보여 주며, 이 애니메이션은 다음과 같은 것입니다. 

![데모 비디오](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>드리프트 개요

**드리프트 개요** 섹션에는 데이터 드리프트의 규모와 추가 조사해야 할 기능에 대한 최상위 인사이트가 포함되어 있습니다. 

| 메트릭 | 설명 | 팁 | 
| ------ | ----------- | ---- | 
| 데이터 드리프트 크기 | 시간 지남에 따라 기준과 대상 데이터 집합 간의 백분율로 지정됩니다. 0에서 100까지의 범위0은 동일한 데이터 집합을 나타내고 100은 Azure Machine Learning 데이터 드리프트 기능이 두 데이터 집합을 완전히 구분할 수 있음을 나타냅니다. | 측정된 정확한 백분율의 노이즈는 이러한 규모를 생성하는 데 사용되는 기계 학습 기술로 인해 예상됩니다. | 
| 기능별 드리프트 기여도 | 측정된 드리프트 크기에 대한 대상 데이터 집합의 각 피처의 기여도입니다. |  공동 변량 이동으로 인해 피쳐의 기본 분포가 상대적으로 높은 피쳐 중요도를 갖도록 변경할 필요는 없습니다. | 

다음 이미지는 [NOAA 통합 표면 데이터의](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)백필에서 발생한 Azure 기계 학습 스튜디오의 **드리프트 개요** 결과에 표시되는 차트의 예입니다. `stationName contains 'FLORIDA'`데이터는 2019년 1월에 기준 데이터 집합으로 사용되고 모든 2019 데이터를 대상으로 샘플링했습니다.
 
![드리프트 개요](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>기능 세부 정보

**기능 세부 정보** 섹션에는 시간이 지남에 따라 선택한 피처 분포의 변경 내용과 기타 통계에 대한 기능 수준 인사이트가 포함되어 있습니다. 

대상 데이터 집합도 시간이 지남에 따라 프로파일화됩니다. 각 피처의 기준선 분포 사이의 통계 거리는 시간 동안의 대상 데이터 집합과 비교되며, 이는 개별 피처에 대한 통계 적 거리가 있다는 점을 제외하고 는 데이터 드리프트 크기와 개념적으로 유사합니다. 최소, 최대 및 평균도 사용할 수 있습니다. 

Azure 기계 학습 스튜디오에서 그래프에서 데이터 포인트를 클릭하면 표시되는 기능의 분포가 그에 따라 조정됩니다. 기본적으로 기준 데이터 집합의 분포와 동일한 피처의 가장 최근 실행 분포를 표시합니다. 

이러한 메트릭은 `get_metrics()` `DataDriftDetector` 개체의 메서드를 통해 Python SDK에서 검색할 수도 있습니다. 

#### <a name="numeric-features"></a>숫자 기능 

숫자 기능은 각 데이터 집합 모니터 실행에서 프로파일됩니다. 다음은 Azure 기계 학습 스튜디오에서 볼 수 있습니다. 분포에 대한 확률 밀도가 표시됩니다.

| 메트릭 | 설명 |  
| ------ | ----------- |  
| 바서슈타인 거리 | 기준 분포를 대상 분포로 변환하는 최소 작업량입니다. |
| 평균값 | 피쳐의 평균 값입니다. |
| 최솟값 | 피쳐의 최소 값입니다. |
| 최댓값 | 피쳐의 최대 값입니다. |

![피처 세부 정보 숫자](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>범주형 기능 

숫자 기능은 각 데이터 집합 모니터 실행에서 프로파일됩니다. 다음은 Azure 기계 학습 스튜디오에서 볼 수 있습니다. 분포에 대한 히스토그램이 표시됩니다.

| 메트릭 | 설명 |  
| ------ | ----------- |  
| 유클리디안 거리 | 기준선과 대상 분포 사이의 기하학적 거리입니다. |
| 고유한 값 | 피처의 고유 값(카디널리티)의 수입니다. |


![기능 세부 사항 범주형](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>메트릭, 경고 및 이벤트

메트릭은 기계 학습 작업 영역과 연결된 [Azure 응용 프로그램 인사이트](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 리소스에서 쿼리할 수 있습니다. 사용자 지정 경고 규칙 및 작업 그룹에 대 한 설정 을 포함 하 여 응용 프로그램 통찰력의 모든 기능에 대 한 액세스를 제공 합니다. 자세한 내용은 전체 애플리케이션 인사이트 설명서를 참조하십시오. 

시작하려면 Azure 포털로 이동하여 작업 영역의 **개요** 페이지를 선택합니다.  연결된 응용 프로그램 인사이트 리소스는 맨 오른쪽에 있습니다.

[![Azure 포털 개요](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

왼쪽 창에서 모니터링 아래에 로그(분석)를 선택합니다.

![애플리케이션 인사이트 개요](./media/how-to-monitor-datasets/ai-overview.png)

데이터 집합 모니터 메트릭은 `customMetrics`로 저장됩니다. 데이터 집합 모니터를 설정한 후 쿼리를 작성하여 실행하여 볼 수 있습니다.

[![로그 분석 쿼리](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

경고 규칙을 설정하는 메트릭을 식별한 후 새 경고 규칙을 만듭니다.

![새 경고 규칙](./media/how-to-monitor-datasets/alert-rule.png)

기존 작업 그룹을 사용하거나 새 작업 그룹을 만들어 설정된 조건이 충족될 때 수행할 작업을 정의할 수 있습니다.

![새 작업 그룹](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>문제 해결

제한 사항 및 알려진 문제:

* 채우기 작업의 시간 범위는 모니터의 주파수 설정의 31 간격으로 제한됩니다. 
* 피처 리스트를 지정하지 않는 한 200개의 피처제한(모든 피쳐 사용).
* 계산 크기는 데이터를 처리할 수 있을 만큼 커야 합니다. 
* 데이터 집합에 지정된 모니터 실행의 시작 및 종료 날짜 내에 데이터가 있는지 확인합니다.
* 데이터 집합 모니터는 50개 이상의 행이 포함된 데이터 집합에서만 작동합니다. 

데이터 집합의 열 또는 피쳐는 다음 표의 조건에 따라 범주형 또는 숫자로 분류됩니다. 기능이 이러한 조건을 충족하지 않는 경우(예: >100개의 고유 값을 가진 형식 문자열 의 열) 기능은 데이터 드리프트 알고리즘에서 삭제되지만 여전히 프로파일링됩니다. 

| 기능 유형 | 데이터 형식 | 조건 | 제한 사항 | 
| ------------ | --------- | --------- | ----------- |
| 범주 | 문자열, 부울, int, 플로트 | 피쳐의 고유 값 수는 100개 미만이며 행 수의 5% 미만입니다. | Null은 자체 범주로 처리됩니다. | 
| 숫자 | int, 플로트 | 피쳐의 값은 숫자 데이터 형식이며 범주형 피쳐의 조건을 충족하지 않습니다. | 값의 15%>null인 경우 기능이 삭제되었습니다. | 

## <a name="next-steps"></a>다음 단계

* [Azure 기계 학습 스튜디오](https://ml.azure.com) 또는 Python [노트북으로](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) 이동하여 데이터 집합 모니터를 설정합니다.
* [Azure Kubernetes 서비스에 배포된 모델에](how-to-monitor-data-drift.md)대한 데이터 드리프트를 설정하는 방법을 알아보십시오.
* [이벤트 그리드를](how-to-use-event-grid.md)사용하여 데이터 집합 드리프트 모니터를 설정합니다. 
