---
title: "Azure IoT Hub에서 센서 데이터의 실시간 데이터 시각화 – Power BI | Microsoft Docs"
description: "Power BI를 사용하여 센서에서 수집하여 Azure IoT Hub로 보낸 온도 및 습도 데이터를 시각화합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "실시간 데이터 시각화, 라이브 데이터 시각화, 센서 데이터 시각화"
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: bf685e4c59117497e27ff7e2ef7b3b39caac6d4a
ms.lasthandoff: 03/30/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화

> [!NOTE]
> 이 자습서를 시작하기 전에 [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)을 완료했는지 확인합니다. [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)에서 IoT 장치와 IoT Hub를 설정하고 장치에서 실행할 샘플 응용 프로그램을 배포합니다. 응용 프로그램에서 수집된 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

Azure IoT Hub에서 Power BI를 통해 받는 실시간 센서 데이터를 시각화하는 방법에 대해 알아봅니다. Web Apps를 사용하여 IoT Hub의 데이터를 시각화하려면 [Azure Web Apps를 사용하여 Azure IoT Hub의 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

- 소비자 그룹을 추가하여 IoT Hub에서 데이터 액세스 준비
- IoT Hub에서 Power BI 계정으로의 데이터 전송을 위한 Stream Analytics 작업 만들기, 구성 및 실행
- Power BI 보고서를 만들고 게시하여 데이터 시각화

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항을 다루는 [Azure IoT Hub에 ESP8266 연결](iot-hub-arduino-huzzah-esp8266-get-started.md) 자습서
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 응용 프로그램
- Power BI 계정 ([Power BI 평가판 사용](https://powerbi.microsoft.com/))

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

소비자 그룹은 응용 프로그램에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 이 단원에서는 Stream Analytics 작업에서 IoT Hub의 데이터를 읽는 데 사용할 소비자 그룹을 만듭니다.

IoT Hub에 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub를 엽니다.
1. 왼쪽 창에서 **끝점**을 클릭하고, 가운데 창에서 **이벤트**를 선택하고, 오른쪽 창의 **소비자 그룹** 아래에서 이름을 입력한 다음, **저장**을 클릭합니다.

   ![Azure IoT Hub에서 소비자 그룹 만들기](media/iot-hub-live-data-visualization-in-power-bi/1_iot-hub-create-consumer-group-azure.png)

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기, 구성 및 실행

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. Azure Portal에서 새로 만들기 > 사물 인터넷 > Stream Analytics 작업을 차례로 클릭합니다.
1. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: 리소스 그룹과 동일한 위치를 사용합니다.

   **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.

   ![Azure에서 Stream Analytics 작업 만들기](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. **만들기**를 클릭합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.
1. **작업 토폴로지**에서 **입력**을 클릭합니다.
1. **입력** 창에서 **추가**를 클릭하고 다음 정보를 입력합니다.

   **입력 별칭**: 입력에 대한 고유 별칭입니다.

   **원본**: **IoT Hub**를 선택합니다.

   **소비자 그룹**: 방금 만든 소비자 그룹을 선택합니다.
1. **만들기**를 클릭합니다.

   ![Azure에서 Stream Analytics 작업에 입력 추가](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 클릭합니다.
1. **출력** 창에서 **추가**를 클릭하고 다음 정보를 입력합니다.

   **출력 별칭**: 출력에 대한 고유 별칭입니다.

   **싱크**: **Power BI**를 선택합니다.
1. **권한 부여**를 클릭한 다음 Power BI 계정에 로그인합니다.
1. 권한이 부여되면 다음 정보를 입력합니다.

   **그룹 작업 영역**: 대상 그룹 작업 영역을 선택합니다.

   **데이터 집합 이름**: 데이터 집합 이름을 입력합니다.

   **테이블 이름**: 테이블 이름을 입력합니다.
1. **만들기**를 클릭합니다.

   ![Azure에서 Stream Analytics 작업에 출력 추가](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지**에서 **쿼리**를 클릭합니다.
1. `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다.
1. `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다.
1. **Save**를 클릭합니다.

   ![Azure에서 Stream Analytics 작업에 쿼리 추가](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>스트림 분석 작업 실행

Stream Analytics 작업에서 **시작** > **지금 시작** > **시작**을 차례로 클릭합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

![Azure에서 Stream Analytics 작업 실행](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Power BI 보고서를 만들고 게시하여 데이터 시각화

1. 샘플 응용 프로그램이 성공적으로 실행 중인지 확인합니다. 그렇지 않은 경우 다음 명령을 실행하여 Pi에서 응용 프로그램을 실행합니다.

   ```bash
   gulp run
   ```
1. [Power BI](https://powerbi.microsoft.com/en-us/) 계정에 로그인합니다.
1. Stream Analytics 작업에 대한 출력을 만들 때 설정한 그룹 작업 영역으로 이동합니다.
1. **스트리밍 데이터 집합**을 클릭합니다.

   Stream Analytics 작업에 대한 출력을 만들 때 지정한 나열된 데이터 집합이 표시됩니다.
1. **작업**에서 첫 번째 아이콘을 클릭하여 보고서를 만듭니다.

   ![Microsoft Power BI 보고서 만들기](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. 시간이 지남에 따라 실시간 온도를 표시하는 꺾은선형 차트를 만듭니다.
   1. 보고서 만들기 페이지에서 꺾은선형 차트를 추가합니다.
   1. **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다.
   1. **시각화** 창에서 **EventEnqueuedUtcTime**을 **축**으로 끌어갑니다.
   1. **온도**를 **값**으로 끌어갑니다.

      이제 꺾은선형 차트가 만들어집니다. 차트의 x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 센서의 온도를 표시합니다.

      ![온도에 대한 꺾은선형 차트를 Microsoft Power BI 보고서에 추가](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. 시간이 지남에 따라 실시간 습도를 표시하는 꺾은선형 차트를 만듭니다. 이렇게 하려면 위와 동일한 단계를 수행하고 **EventEnqueuedUtcTime**을 x축에, **습도**를 y축에 배치합니다.

   ![습도에 대한 꺾은선형 차트를 Microsoft Power BI 보고서에 추가](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. **저장**을 클릭하여 보고서를 저장합니다.
1. **파일** > **웹에 게시**를 차례로 클릭합니다.
1. **Embed 태그 만들기**를 클릭한 다음 **게시**를 클릭합니다.

보고서 액세스를 위해 누구와도 공유할 수 있는 보고서 링크와 블로그 또는 웹 사이트에 보고서를 통합하는 코드 조각이 제공됩니다.

![Microsoft Power BI 보고서 게시](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

모바일 장치에서 Power BI 대시보드 및 보고서를 보고 상호 작용할 수 있는 [Power BI 모바일 앱](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)도 제공합니다.

## <a name="next-steps"></a>다음 단계

Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터를 시각화했습니다.
Azure IoT Hub에서 데이터를 시각화하는 또 다른 방법이 있습니다. [Azure Web Apps를 사용하여 Azure IoT Hub의 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)를 참조하세요.

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](iot-hub-explorer-cloud-device-messaging.md)
- [IoT Hub 메시지를 Azure 데이터 저장소에 저장](iot-hub-store-data-in-azure-table-storage.md)

