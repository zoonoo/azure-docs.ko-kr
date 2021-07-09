---
title: Anomaly Detector 다변량 JavaScript 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 087640433d592c7e2885e68240e98e7283bf3407
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110165784"
---
JavaScript용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 패키지를 설치하고 서비스에서 제공하는 알고리즘을 사용합니다. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류로부터 사전에 보호할 수 있습니다.

JavaScript용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 양상을 측정하는 수천 개 유형의 센서를 사용하여 고가의 물리적 자산을 사전 예방적으로 유지 관리.

[라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector) | [패키지(npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector) | [샘플 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## <a name="prerequisites"></a>사전 요구 사항

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

> [!NOTE]
> 항상 두 키 중 하나를 사용할 수 있습니다. 이는 보안 키 회전을 허용하기 위한 것입니다. 이 빠른 시작의 목적을 위해 첫 번째 키를 사용합니다. 
   

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

Anomaly Detector 다변량 API를 사용하려면 먼저 고유한 모델을 학습해야 합니다. 학습 데이터는 다음 요구 사항을 충족하는 여러 시계열 세트입니다.

각 시계열은 헤더 행으로 "timestamp" 및 "value"(모두 소문자)라는 2개(및 2개만)의 열이 포함된 CSV 파일이어야 합니다. "timestamp" 값은 ISO 8601을 준수해야 합니다. "value"는 소수 자릿수가 있는 정수 또는 소수일 수 있습니다. 예를 들면 다음과 같습니다.

|timestamp | 값|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

각 CSV 파일의 이름을 모델 학습에 사용되는 다른 변수 뒤에 지정해야 합니다. 예를 들면 "temperature.csv" 및 "humidity.csv"입니다. 모든 CSV 파일은 하위 폴더 없이 하나의 zip 파일로 압축되어야 합니다. zip 파일에는 원하는 이름을 사용할 수 있습니다. zip 파일을 Azure Blob Storage에 업로드해야 합니다. zip 파일에 대한 Blob SAS(공유 액세스 서명) URL을 생성한 후에는 학습에 사용할 수 있습니다. Azure Blob Storage에서 SAS URL을 생성하는 방법은 이 문서를 참조하세요.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ms-rest-azure` 및 `azure-ai-anomalydetector` NPM 패키지를 설치합니다. csv-구문 분석 라이브러리는 이 빠른 시작에서도 사용됩니다.

```console
npm install @azure/ai-anomaly-detector csv-parse
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
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
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
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

모델 학습이 완료되었는지 확인하려면 모델의 상태를 추적하면 됩니다.

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
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
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>모델 내보내기

> [!NOTE]
> 내보내기 명령은 컨테이너화된 환경에서 Anomaly Detector 다변량 모델을 실행할 수 있도록 하는 데 사용됩니다. 이는 현재 다변량에 대해 지원되지 않지만 나중에 지원이 추가될 예정입니다.

학습된 모델을 내보내려면 `exportModel` 함수를 사용합니다.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>모델 삭제

현재 리소스에 사용할 수 있는 기존 모델을 삭제하려면 `deleteMultivariateModel` 함수를 사용합니다.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하기 전에 [전체 샘플 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)에서 코드를 확인하는 것이 도움이 될 수 있습니다.

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../../overview-multivariate.md)
* [Anomaly Detector API를 사용하는 경우의 모범 사례입니다.](../../concepts/best-practices-multivariate.md) 