---
title: '자습서: 1시간 내에 다변량 변칙 검색에 대해 알아보기'
titleSuffix: Azure Cognitive Services
description: 다변량 변칙 검색에 대한 엔드투엔드 자습서입니다.
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292649"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>자습서: 1시간 내에 다변량 변칙 검색에 대해 알아보기

MVAD(다변량 변칙 검색)를 사용하는 Anomaly Detector는 **자율** 방식으로 메트릭 그룹의 변칙을 검색하기 위한 고급 AI 도구입니다.

일반적으로 다음 단계를 수행하여 MVAD를 사용할 수 있습니다.

  1. Azure에서 MVAD를 지원하는 Anomaly Detector 리소스를 만듭니다.
  1. 데이터를 준비합니다.
  1. MVAD 모델을 학습시킵니다.
  1. 모델의 상태를 쿼리합니다.
  1. 학습된 MVAD 모델로 변칙을 검색합니다.
  1. 추론 결과를 검색하고 해석합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 올바른 형식으로 데이터를 준비하는 방법을 이해합니다.
> * MVAD로 학습하고 추론하는 방법을 이해합니다.
> * 입력 매개 변수와 추론 결과에서 출력을 해석하는 방법을 이해합니다.

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. MVAD를 지원하는 Anomaly Detector 리소스 만들기

* Azure 구독이 없는 경우 만들기 - [무료로 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독이 있으면 Azure Portal에서 [Anomaly Detector 리소스를 만들어](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) API 키와 API 엔드포인트를 가져옵니다.

> [!NOTE]
> 미리 보기 단계 중에는 MVAD를 제한된 지역에서만 사용할 수 있습니다. MVAD 지역 롤아웃에 대해 최신 상태를 유지하려면 [Anomaly Detector의 새로운 기능](../whats-new.md)에 책갈피를 설정하세요. GitHub 문제를 제출하거나 [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com)으로 연락하여 특정 지역을 요청할 수도 있습니다.

## <a name="2-data-preparation"></a>2. 데이터 준비

그런 다음 학습 데이터(및 추론 데이터)를 준비해야 합니다.

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>데이터 압축 및 업로드 도구

이 섹션에서는 MVAD 입력 데이터를 처리하는 자체 애플리케이션 논리에 추가하기 위해 복사 및 편집할 수 있는 몇 가지 샘플 코드와 도구를 공유합니다.

#### <a name="compressing-csv-files-in-nix"></a>\*nix에서 CSV 파일 압축

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>Windows에서 CSV 파일 압축

* 모든 CSV 파일이 있는 *폴더로 이동* 합니다.
* 필요한 모든 CSV 파일을 선택합니다.
* CSV 파일 중 하나를 마우스 오른쪽 단추로 클릭하고 `Send to`를 선택합니다.
* 드롭다운에서 `Compressed (zipped) folder`를 선택합니다.
* 필요에 따라 zip 파일의 이름을 바꿉니다.

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>Python 코드 압축 및 Azure Blob Storage에 데이터 업로드

Azure Blob에 파일을 업로드하는 방법을 알아보려면 [이 문서](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)를 참조하세요.

또는 압축 및 업로드를 수행할 수 있는 아래 샘플 코드를 참조할 수 있습니다. 이 섹션의 Python 코드를 .py 파일(예: `zipAndUpload.py`)로 복사 및 저장하고 다음과 같은 명령줄을 사용하여 실행할 수 있습니다.

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    이 명령은 `foo\bar`의 모든 CSV 파일을 `test123.zip`이라는 단일 zip 파일로 압축합니다. Blob의 `container_xxx` 컨테이너에 `test123.zip`을 업로드합니다.
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    이 명령은 위와 동일한 작업을 수행하지만 성공적으로 업로드한 후 zip 파일 `test123.zip`을 삭제합니다. 

인수:

* `--source-folder`, `-s`, CSV 파일이 포함된 원본 폴더의 경로
* `--zipfile-name`, `-z`, zip 파일의 이름
* `--connection-string`, `-c`, Blob에 대한 연결 문자열
* `--container-name`, `-n`, 컨테이너의 이름
* `--remove-zipfile`, `-r`, 켜져 있는 경우 zip 파일 제거

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. MVAD 모델 학습

다음은 MVAD 모델을 학습시키기 위한 Python의 샘플 요청 본문과 샘플 코드입니다.

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

응답 코드 `201`은 성공적인 요청을 나타냅니다.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. 모델 상태 가져오기

교육 API는 비동기식이므로 교육 API를 호출한 직후에 모델을 가져오지 않습니다. 그러나 모든 모델을 나열하는 API 키 또는 특정 모델에 대한 정보를 나열하는 모델 ID로 모델의 상태를 쿼리할 수 있습니다.

### <a name="list-all-the-models"></a>모든 모델 나열

요청 URL 및 요청 헤더에 대한 정보는 [이 페이지](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel)를 참조하세요. 업데이트 시간별로 정렬된 10개 모델만 반환하지만 요청 URL에서 `$skip` 및 `$top` 매개 변수를 설정하여 다른 모델을 방문할 수 있습니다. 예를 들어 요청 URL이 `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20`이면 최신 10개 모델을 건너뛰고 다음 20개 모델을 반환합니다.

샘플 응답은 다음과 같습니다. 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

응답에는 `models`, `currentCount`, `maxCount` 및 `nextLink`의 4개 필드가 있습니다.

* `models`에는 생성 시간, 마지막 업데이트 시간, 모델 ID, 표시 이름, 변수 개수 및 각 모델의 상태가 포함됩니다.
* `currentCount`에는 학습된 다변량 모델의 수가 포함됩니다.
* `maxCount`는 이 Anomaly Detector 리소스에서 지원하는 최대 모델 수입니다.
* `nextLink`를 사용하여 더 많은 모델을 가져올 수 있습니다.

### <a name="get-models-by-model-id"></a>모델 ID로 모델 가져오기

[이 페이지](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel)에서는 모델 ID별로 모델 정보를 쿼리하기 위한 요청 URL에 대해 설명합니다. 아래에 샘플 응답이 나와 있습니다.

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

쿼리된 모델에 대한 자세한 정보를 받게 됩니다. 응답에는 모델, 학습 매개 변수 및 진단 정보에 대한 메타 정보가 포함됩니다. 진단 정보는 학습 진행 상황을 디버깅하고 추적하는 데 유용합니다.

* `epochIds`는 총 100개 Epoch 중에서 모델이 학습된 Epoch 수를 나타냅니다. 예를 들어 모델이 아직 학습 상태인 경우 `epochId`는 `[10, 20, 30, 40, 50]`일 수 있습니다. 즉, 50번째 학습 Epoch를 완료했고 절반이 남아 있습니다.
* `trainLosses` 및 `validationLosses`는 최적화 진행이 수렴하는지 확인하는 데 사용되며, 이 경우 두 손실이 점차 감소해야 합니다.
* `latenciesInSeconds`는 각 Epoch에 대한 시간 비용을 포함하며 10 Epoch마다 기록됩니다. 이 예에서 10번째 Epoch는 약 0.34초가 걸립니다. 이는 학습의 완료 시간을 예측하는 데 도움이 됩니다.
* `variableStates`는 각 변수에 대한 정보를 요약합니다. `filledNARatio`에 의해 내림차순으로 순위가 매겨진 목록입니다. 각 변수에 사용된 데이터 포인트 수를 알려주고 `filledNARatio`는 누락된 포인트 수를 알려줍니다. 일반적으로 가능한 한 `filledNARatio`를 줄여야 합니다.
누락된 데이터 포인트가 너무 많으면 모델 정확도가 저하됩니다.
* 데이터 처리 중 오류는 `errors` 필드에 포함됩니다.

## <a name="5-inference-with-mvad"></a>5. MVAD를 사용한 추론

추론을 수행하려면 추론 데이터, 시작 시간 및 종료 시간이 포함된 zip 파일에 Blob 원본을 제공하기만 하면 됩니다.

추론도 비동기식이므로 결과가 즉시 반환되지 않습니다. 나중에 결과를 얻을 수 있는 위치를 알 수 있도록 `resultId`가 포함된 **응답 헤더** 의 결과 링크를 변수에 저장해야 합니다.

실패는 일반적으로 모델 문제 또는 데이터 문제로 인해 발생합니다. 모델이 준비되지 않았거나 데이터 링크가 유효하지 않은 경우 추론을 수행할 수 없습니다. 학습 데이터와 추론 데이터가 일치하는지 확인합니다. 즉, **정확히** 동일한 변수여야 하지만 타임스탬프는 달라야 합니다. 변수가 더 많거나 더 적은 경우 또는 다른 변수 집합에 대한 추론인 경우에는 데이터 확인 단계를 통과하지 못하고 오류가 발생합니다. 데이터 확인이 지연되어 결과를 쿼리할 때만 오류 메시지를 받게 됩니다.

## <a name="6-get-inference-results"></a>6. 추론 결과 얻기

결과를 얻으려면 `resultId`가 필요합니다. `resultId`는 추론 요청을 제출할 때 응답 헤더에서 가져옵니다. [이 페이지](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult)에는 추론 결과를 쿼리하기 위한 지침이 포함되어 있습니다. 

아래에 샘플 응답이 나와 있습니다.

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

응답에는 결과 상태, 변수 정보, 추론 매개 변수 및 추론 결과가 포함됩니다.

* `variableStates`는 추론 요청에 있는 각 변수의 정보를 나열합니다.
* `setupInfo`는 이 추론을 위해 제출된 요청 본문입니다.
* `results`에는 검색 결과가 포함됩니다. 검색 결과에는 세 가지 일반적인 유형이 있습니다.
    1. 오류 코드 `InsufficientHistoricalData`. 이는 모델이 기간 기반 방식으로 데이터를 추론하고 결정을 내리기 위해 과거 데이터가 필요하기 때문에 일반적으로 처음 몇 개의 타임스탬프에서만 발생합니다. 처음 몇 개의 타임스탬프에는 기록 데이터가 충분하지 않으므로 추론을 수행할 수 없습니다. 이 경우 오류 메시지는 무시할 수 있습니다.
    1. `"isAnomaly": false`는 현재 타임스탬프가 변칙이 아님을 나타냅니다.
        * `severity `는 변칙의 상대적 심각도를 나타내며 일반 데이터의 경우 항상 0입니다.
        * `score`는 모델이 일반 데이터 포인트에 대해서도 0이 아닐 수 있는 결정을 내리는 모델의 원시 출력입니다.
    1. `"isAnomaly": true`는 현재 타임스탬프의 변칙을 나타냅니다.
        * `severity `는 변칙의 상대적 심각도를 나타내며 변칙 데이터의 경우 항상 0보다 큽니다.
        * `score`는 모델에서 결정하는 모델의 원시 출력입니다. `severity`는 `score`에서 파생된 값입니다. 모든 데이터 포인트에는 `score`가 있습니다.
        * `contributors`는 각 변수의 기여도 점수를 포함하는 목록입니다. 기여도 점수가 높을수록 근본 원인의 가능성이 높음을 나타냅니다. 이 목록은 종종 변칙을 해석하고 근본 원인을 진단하는 데 사용됩니다.

> [!NOTE]
> 일반적인 문제는 `isAnomaly`=`true`가 있는 모든 데이터 포인트를 변칙으로 간주하는 것입니다. 이로 인해 너무 많은 가양성이 발생할 수 있습니다.
> `isAnomaly` 및 `severity`(또는 `score`)를 모두 사용하여 심각하지 않은 변칙을 선별하고 선택적으로 그룹화를 사용하여 변칙 기간을 확인하여 임의 노이즈를 억제해야 합니다. `severity`와 `score`의 차이점은 모범 사례 문서의 [FAQ](../concepts/best-practices-multivariate.md#faq)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [모범 사례: 다변량 Anomaly Detector API를 사용할 때 따라야 할 권장 사례](../concepts/best-practices-multivariate.md)
* [빠른 시작: Anomaly Detector 다변량 클라이언트 라이브러리 사용](../quickstarts/client-libraries-multivariate.md)