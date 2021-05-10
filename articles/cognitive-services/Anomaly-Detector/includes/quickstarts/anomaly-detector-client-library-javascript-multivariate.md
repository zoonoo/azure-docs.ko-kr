---
title: Anomaly Detector 다변량 JavaScript 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318786"
---
JavaScript용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 패키지를 설치하고 서비스에서 제공하는 알고리즘을 사용합니다. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류로부터 사전에 보호할 수 있습니다.

JavaScript용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 측면을 측정하는 수십~수백 가지 유형의 센서를 통해 고가의 물리적 자산을 예측하여 유지 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

`index.js`라는 파일을 만들고 다음 라이브러리를 가져옵니다.
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 예제 데이터 파일에 대한 다른 변수를 만듭니다.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Anomaly Detector 다변량 API를 사용하려면 검색을 사용하기 전에 고유한 모델을 학습해야 합니다. 학습에 사용되는 데이터는 일련의 시계열의 일괄 처리입니다. 각 시계열은 타임스탬프와 값이라는 두 개의 열이 포함된 CSV 형식이어야 합니다. 모든 시계열은 하나의 zip 파일로 압축되고 [Azure Blob 스토리지](../../../../storage/blobs/storage-blobs-introduction.md)에 업로드되어야 합니다. 기본적으로 파일 이름은 시계열의 변수를 나타내는 데 사용됩니다. 또는 변수의 이름을 .zip 파일 이름과 다르게 지정하려는 경우 추가 meta.json 파일을 zip 파일에 포함시킬 수 있습니다. [Blob SAS(공유 액세스 서명) URL](../../../../storage/common/storage-sas-overview.md)을 생성하면 학습을 위해 zip 파일에 대한 URL을 사용할 수 있습니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ms-rest-azure` 및 `azure-ai-anomalydetector` NPM 패키지를 설치합니다. csv-구문 분석 라이브러리는 이 빠른 시작에서도 사용됩니다.

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Node.js용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [모델 학습](#train-a-model)
* [변칙 검색](#detect-anomalies)
* [모델 내보내기](#export-model)
* [모델 삭제](#delete-model)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트와 자격 증명을 사용하여 `AnomalyDetectorClient` 개체를 인스턴스화합니다.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>모델 학습

### <a name="construct-a-model-result"></a>모델 결과 생성

먼저 모델 요청을 생성해야 합니다. 시작 및 종료 시간이 데이터 원본과 일치하는지 확인합니다.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>새 모델 학습

모델 요청을 Anomaly Detector 클라이언트 `trainMultivariateModel` 메서드에 전달해야 합니다.

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

모델 학습이 완료되었는지 확인하려면 모델의 상태를 추적하면 됩니다.

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>변칙 검색

`detectAnomaly` 및 `getDectectionResult` 함수를 사용하여 데이터 원본 내에 변칙이 있는지 확인합니다.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>모델 내보내기

학습된 모델을 내보내려면 `exportModel` 함수를 사용합니다.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>모델 삭제

현재 리소스에 사용할 수 있는 기존 모델을 삭제하려면 `deleteMultivariateModel` 함수를 사용합니다.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector 다변량 모범 사례](../../concepts/best-practices-multivariate.md)
