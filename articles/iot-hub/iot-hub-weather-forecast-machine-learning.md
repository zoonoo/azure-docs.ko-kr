---
title: IoT Hub 데이터와 Azure Machine Learning Studio (클래식)을 사용한 날씨 예측
description: Azure Machine Learning Studio (클래식)를 사용 하 여 IoT hub가 센서에서 수집한 온도 및 습도 데이터를 기반으로 하는 비가의 확률을 예측 합니다.
author: robinsh
manager: philmea
keywords: 일기 예보 기계 학습
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 8ba68e56d2475b1ff2fb3e63f291f76063ca62e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777159"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio에서 IoT hub의 센서 데이터를 사용한 날씨 예측 (클래식)

![엔드투엔드 다이어그램](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

기계 학습은 컴퓨터에서 기존 데이터로부터 학습하여 미래 동작, 결과 및 추세를 예측하는 데 유용한 데이터 과학 기술입니다. Azure Machine Learning Studio(클래식)는 예측 모델을 신속하게 만들고 분석 솔루션으로 배포할 수 있게 해주는 클라우드 예측 분석 서비스입니다.

## <a name="what-you-learn"></a>학습 내용

Azure Machine Learning Studio (클래식)를 사용 하 여 Azure IoT hub에서 온도 및 습도 데이터를 사용 하 여 날씨 예측을 수행 하는 방법에 대해 알아봅니다. 강우 확률은 준비된 날씨 예측 모델의 결과입니다. 모델에서는 기록 데이터를 기반으로 하여 온도 및 습도에 따라 강우 확률을 예측합니다.

## <a name="what-you-do"></a>수행할 작업

- 날씨 예측 모델을 웹 서비스로 배포합니다.
- 소비자 그룹을 추가하여 IoT Hub에서 데이터 액세스 준비
- Stream Analytics 작업을 만들고 해당 작업을 다음과 같이 구성합니다.
  - IoT Hub에서 온도 및 습도 데이터를 읽습니다.
  - 웹 서비스를 호출하여 강우 확률을 가져옵니다.
  - Azure Blob Storage에 결과를 저장합니다.
- Microsoft Azure Storage Explorer를 사용하여 일기 예보를 살펴봅니다.

## <a name="what-you-need"></a>필요한 항목

- [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 디바이스 자습서(예: [Node.js를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)) 중 하나를 완료합니다. 이러한 자습서는 다음 요구 사항을 충족합니다.
  - 활성화된 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 애플리케이션
- [Azure Machine Learning Studio(클래식)](https://studio.azureml.net/) 계정입니다.
- [Azure Storage 계정은](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json#types-of-storage-accounts) **범용 V2** 계정 이지만 Azure Blob 저장소를 지 원하는 Azure Storage 계정도 작동 합니다.

> [!Note]
> 이 문서에서는 Azure Stream Analytics 및 기타 유료 서비스를 사용 합니다. 추가 요금은 Azure 지역 간에 데이터를 전송 해야 하는 경우 Azure Stream Analytics에 발생 합니다. 이러한 이유로이 자습서의 뒷부분에서 추가 된 리소스 그룹, IoT Hub 및 Azure Storage 계정 뿐만 아니라 Machine Learning Studio (클래식) 작업 영역과이 자습서의 뒷부분에 추가 된 Azure Stream Analytics 작업을 확인 하는 것이 좋습니다.-는 모두 동일한 Azure 지역에 있습니다. 지역별 [azure 제품 가용성 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)에서 Azure Machine Learning Studio (클래식) 및 기타 azure 서비스에 대 한 지역별 지원을 확인할 수 있습니다.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>날씨 예측 모델을 웹 서비스로 배포

이 섹션에서는 Azure AI Library에서 날씨 예측 모델을 가져옵니다. 그런 다음 모델에 R 스크립트 모듈을 추가하여 온도 및 습도 데이터를 정리합니다. 마지막으로 모델을 예측 웹 서비스로 배포합니다.

### <a name="get-the-weather-prediction-model"></a>날씨 예측 모델 가져오기

이 섹션에서는 Azure AI Gallery에서 날씨 예측 모델을 가져와 Azure Machine Learning Studio(클래식)에서 엽니다.

1. [날씨 예측 모델 페이지](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)로 이동합니다.

   ![Azure AI Gallery에서 날씨 예측 모델 페이지 열기](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. **스튜디오에서 열기 (클래식)** 를 선택 하 여 Microsoft Azure Machine Learning Studio (클래식)에서 모델을 엽니다. IoT hub 근처의 영역을 선택 하 고 **갤러리에서 실험 복사** 팝업의 올바른 작업 영역을 선택 합니다.

   ![Azure Machine Learning Studio(클래식)에서 날씨 예측 모델 열기](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>온도 및 습도 데이터를 정리하는 R 스크립트 모듈 추가

모델이 올바르게 동작하려면 온도 및 습도 데이터를 숫자 데이터로 변환할 수 있어야 합니다. 이 섹션에서는 숫자 값으로 변환할 수 없는 온도 또는 습도 데이터 값이 있는 행을 모두 제거하는 R 스크립트 모듈을 날씨 예측 모델에 추가합니다.

1. Azure Machine Learning Studio (클래식) 창의 왼쪽에서 화살표를 선택 하 여 도구 패널을 확장 합니다. 검색 상자에 "실행"을 입력합니다. **R 스크립트 실행** 모듈을 선택합니다.

   ![R 스크립트 실행 모듈 선택](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. 다이어그램에서 **R 스크립트 실행** 모듈을 **누락 데이터 정리** 모듈 및 기존 **R 스크립트 실행** 모듈 가까이 끌어 놓습니다. **누락 데이터 정리** 모듈과 **R 스크립트 실행** 모듈 간의 연결을 삭제하고 그림에 표시된 것처럼 새 모듈의 입력과 출력을 연결합니다.

   ![R 스크립트 실행 모듈 추가](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 새 **R 스크립트 실행** 모듈을 선택하여 속성 창을 엽니다. 다음 코드를 복사하여 **R 스크립트** 상자에 붙여넣습니다.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   완료되면 속성 창이 다음과 같이 표시됩니다.

   ![R 스크립트 실행 모듈에 코드 추가](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>예측 웹 서비스 배포

이 섹션에서는 모델의 유효성을 검사하고 모델을 기반으로 예측 웹 서비스를 설정한 다음 웹 서비스를 배포합니다.

1. **실행** 을 선택 하 여 모델의 단계에 대 한 유효성을 검사 합니다. 이 단계를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

   ![실험을 실행하여 단계의 유효성을 검사합니다.](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. **웹 서비스**  >  **예측 웹 서비스**설정을 선택 합니다. 예측 실험 다이어그램이 열립니다.

   ![Azure Machine Learning Studio(클래식)에서 날씨 예측 모델 배포](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 예측 실험 다이어그램에서 **웹 서비스 입력** 모듈과 맨 위에 있는 **데이터 집합의 열 선택** 에서 연결을 삭제 합니다. 그런 다음 **웹 서비스 입력** 모듈을 **모델 점수 매기기** 모듈 가까이 끌어 놓고 그림과 같이 연결합니다.

   ![Azure Machine Learning Studio(클래식)에서 두 모듈 연결](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. **실행** 을 선택 하 여 모델의 단계에 대 한 유효성을 검사 합니다.

1. 웹 서비스 **배포** 를 선택 하 여 모델을 웹 서비스로 배포 합니다.

1. 모델의 대시보드에서 **요청/응답**에 대한 **Excel 2010 또는 이전 통합 문서**를 다운로드합니다.

   > [!Note]
   > 컴퓨터에서 최신 버전의 Excel을 실행하더라도 **Excel 2010 또는 이전 통합 문서**를 다운로드해야 합니다.

   ![REQUEST RESPONSE 엔드포인트에 대한 Excel 다운로드](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Excel 통합 문서를 열고 **웹 서비스 URL** 및 **액세스 키**를 기록해 둡니다.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기, 구성 및 실행

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기**를 선택 합니다. 검색 상자에 "stream analytics 작업"을 입력 하 고 결과 드롭다운에서 **Stream Analytics 작업** 을 선택 합니다. **Stream Analytics 작업** 창이 열리면 **만들기**를 선택 합니다.
1. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **구독**: 기본값과 다른 경우 구독을 선택 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: 리소스 그룹과 동일한 위치를 사용합니다.

   다른 모든 필드는 기본값으로 둡니다.

   ![Azure에서 Stream Analytics 작업 만들기](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. **만들기**를 선택합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.
1. **작업 토폴로지**에서 **입력**을 선택합니다.
1. **입력** 창에서 **스트림 입력 추가**를 선택 하 고 드롭다운에서 **IoT Hub** 를 선택 합니다. **새 입력** 창에서 **구독에서 IoT Hub 선택** 을 선택 하 고 다음 정보를 입력 합니다.

   **입력 별칭**: 입력에 대한 고유 별칭입니다.

   **구독**: 기본값과 다른 경우 구독을 선택 합니다.

   **IoT Hub**: 구독에서 IoT Hub를 선택 합니다.

   **공유 액세스 정책 이름**:  **서비스**를 선택 합니다. **Iothubowner**를 사용할 수도 있습니다.

   **소비자 그룹**: 앞서 만든 소비자 그룹을 선택합니다.

   다른 모든 필드는 기본값으로 둡니다.

   ![Azure에서 Stream Analytics 작업에 입력 추가](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. **저장**을 선택합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 선택합니다.
1. **출력** 창에서 **추가**를 선택한 다음 드롭다운에서 **Blob storage/Data Lake Storage** 를 선택 합니다. **새 출력** 창에서 **구독에서 저장소 선택** 을 선택 하 고 다음 정보를 입력 합니다.

   **출력 별칭**: 출력에 대한 고유 별칭입니다.

   **구독**: 기본값과 다른 경우 구독을 선택 합니다.

   **스토리지 계정**: Blob Storage용 스토리지 계정입니다. 스토리지 계정을 만들거나 기존 계정을 사용할 수 있습니다.

   **컨테이너**: Blob이 저장되는 컨테이너입니다. 컨테이너를 만들거나 기존 컨테이너를 사용할 수 있습니다.

   **이벤트 직렬화 형식**: **CSV**를 선택합니다.

   ![Azure에서 Stream Analytics 작업에 출력 추가](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. **저장**을 선택합니다.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Stream Analytics 작업에 배포된 웹 서비스를 호출하는 함수 추가

1. **작업 토폴로지**아래에서 **함수**를 선택 합니다.
1. **함수** 창에서 **추가**를 선택한 다음 드롭다운에서 **Azure ML Studio** 를 선택 합니다. Azure **ML 서비스가**아닌 **azure ML Studio**를 선택 했는지 확인 합니다. **새 함수** 창에서 **Azure Machine Learning 함수 설정 수동으로 제공** 을 선택 하 고 다음 정보를 입력 합니다.

   **함수 별칭**: `machinelearning`를 입력합니다.

   **URL**: Excel 통합 문서에서 기록해 둔 웹 서비스 URL을 입력합니다.

   **키**: Excel 통합 문서에서 기록해 둔 액세스 키를 입력합니다.

   ![Azure에서 Stream Analytics 작업에 함수 추가](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. **저장**을 선택합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지**에서 **쿼리**를 선택합니다.
1. 기존 코드를 다음 코드로 바꿉니다.

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다.

   `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다.

1. **쿼리 저장**을 선택합니다.

> [!Note]
> **쿼리 테스트**를 선택 하면 다음 메시지가 표시 됩니다. Machine Learning 함수로 쿼리 테스트는 지원 되지 않습니다. 쿼리를 수정 하 고 다시 시도 하세요. 이 메시지를 무시 하 고 **확인** 을 선택 하 여 메시지 상자를 닫을 수 있습니다. 다음 섹션으로 진행 하기 전에 쿼리를 저장 해야 합니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업의 왼쪽 창에서 **개요** 를 선택 합니다. 이제 시작 **시작**을 선택  >  **Now**  >  **Start**합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

![Stream Analytics 작업 실행](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage Explorer를 사용하여 일기 예보 보기

클라이언트 애플리케이션을 실행하여 온도 및 습도 데이터를 수집하여 IoT Hub로 보냅니다. Stream Analytics 작업에서는 IoT Hub에서 받은 각 메시지에 대해 일기 예보 웹 서비스를 호출하여 강우 확률을 생성합니다. 그런 다음 Azure Blob Storage에 결과를 저장합니다. Azure Storage Explorer는 결과를 보는 데 사용할 수 있는 도구입니다.

1. [Microsoft Azure Storage Explorer를 다운로드하고 설치합니다](https://storageexplorer.com/).
1. Azure Storage Explorer를 엽니다.
1. Azure 계정에 로그인합니다.
1. 구독을 선택합니다.
1. 컨테이너 > **Blob 컨테이너** **> 저장소 계정 > 구독** > 선택 합니다.
1. .csv 파일을 다운로드하여 결과를 확인합니다. 마지막 열은 강우 확률을 기록하고 있습니다.

   ![Azure Machine Learning Studio를 사용 하 여 날씨 예측 결과 가져오기 (클래식)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>요약

IoT hub에서 수신 하는 온도 및 습도 데이터를 기반으로 하는 경우에는 Azure Machine Learning Studio (클래식)을 사용 했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
