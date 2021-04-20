---
title: Anomaly Detector Python 다변량 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318775"
---
Python용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 서비스에서 제공하는 알고리즘을 사용하여 패키지 시작을 설치하려면 다음 단계를 따르세요. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류를 사전에 보호할 수 있습니다.

Python용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 양상을 측정하는 수천 개 유형의 센서를 사용하여 고가의 물리적 자산을 사전 예방적으로 유지 관리.

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.x](https://www.python.org/)
* [Pandas 데이터 분석 라이브러리](https://pandas.pydata.org/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

환경 변수, 시계열 데이터 파일의 경로 및 구독의 Azure 위치로 키에 대한 변수를 만듭니다. 예들 들어 `westus2`입니다.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [모델 학습](#train-the-model)
* [변칙 검색](#detect-anomalies)
* [모델 내보내기](#export-model)
* [모델 삭제](#delete-model)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 Anomaly Detector 클라이언트를 인스턴스화하려면 Anomaly Detector 구독 키 및 연결된 엔드포인트를 전달해야 합니다. 또한 데이터 원본을 설정합니다.  

Anomaly Detector 다변량 API를 사용하려면 검색을 사용하기 전에 고유한 모델을 학습시켜야 합니다. 학습에 사용되는 데이터는 시계열의 일괄 처리입니다. 각 시계열은 타임스탬프와 값이라는 두 열이 포함된 CSV 형식이어야 합니다. 모든 시계열은 하나의 zip 파일로 압축되고 [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs)에 업로드되어야 합니다. 기본적으로 파일 이름은 시계열의 변수를 나타내는 데 사용됩니다. 또는 변수의 이름을 .zip 파일 이름과 다르게 지정하려는 경우 추가 meta.js 파일을 zip 파일에 포함시킬 수 있습니다. [Blob SAS(공유 액세스 서명) URL](../../../../storage/common/storage-sas-overview.md)을 생성하면 학습을 위해 zip 파일에 대한 URL을 사용할 수 있습니다.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>모델 학습

먼저 모델을 학습시키고, 학습 중에 모델의 상태를 확인하여 학습 완료 시기를 파악한 다음, 검색 단계로 이동할 때 필요한 최신 모델 ID를 검색합니다.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>변칙 검색

`detect_anomaly` 및 `get_dectection_result`를 사용하여 데이터 원본 내에 변칙이 있는지 확인합니다. 앞서 학습한 모델의 모델 ID를 전달해야 합니다.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>모델 내보내기

모델을 내보내려면 `export_model`을 사용하고 내보내려는 모델의 모델 ID를 전달합니다.

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>모델 삭제

모델을 삭제하려면 `delete_multivariate_model`을 사용하고 삭제하려는 모델의 모델 ID를 전달합니다.

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하기 전에 새로 만든 함수를 호출하는 코드를 추가해야 합니다.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

`python` 명령 및 파일 이름을 사용하여 애플리케이션을 실행합니다.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
