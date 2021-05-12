---
title: Azure IoT Hub에서 데이터의 실시간 데이터 시각화 – Power BI
description: Power BI를 사용하여 센서에서 수집하여 Azure IoT Hub로 보낸 온도 및 습도 데이터를 시각화합니다.
author: robinsh
keywords: 실시간 데이터 시각화, 라이브 데이터 시각화, 센서 데이터 시각화
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 6ff0a0bb8bc1ca9b26968d002bb44d206c36e158
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109483914"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화

![엔드투엔드 다이어그램](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

이 문서에서는 Azure IoT 허브에서 Power BI를 통해 받는 실시간 센서 데이터를 시각화하는 방법을 알아봅니다. 웹앱을 사용하여 IoT 허브에서 데이터를 시각화하려면 [웹앱을 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)를 참조하세요.

## <a name="prerequisites"></a>필수 요건

* [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 디바이스 자습서 중 하나를 완료합니다. 예를 들어 [node.js를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)로 이동하거나 [원격 분석 전송](quickstart-send-telemetry-dotnet.md) 빠른 시작 중 하나로 이동할 수 있습니다. 이러한 문서는 다음 요구 사항을 다룹니다.
  
  * 활성화된 Azure 구독.
  * 구독의 Azure IoT 허브.
  * 메시지를 Azure IoT Hub로 보내는 클라이언트 애플리케이션

* Power BI 계정. ([Power BI를 무료로 사용해 보기](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기, 구성 및 실행

Stream Analytics 작업을 만들어 시작해 보겠습니다. 작업을 만든 후 데이터를 검색하는 데 사용된 입력, 출력 및 쿼리를 정의합니다.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업** 을 선택합니다.

2. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: 리소스 그룹과 동일한 위치를 사용합니다.

   ![Azure에서 Stream Analytics 작업 만들기](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. **만들기** 를 선택합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.

2. **작업 토폴로지** 에서 **입력** 을 선택합니다.

3. **입력** 창에서 **스트림 입력 추가** 를 선택하고 드롭다운 목록에서 **IoT Hub** 를 선택합니다. 새 입력 창에서 다음 정보를 입력합니다.

   **입력 별칭**: 입력의 고유한 별칭을 입력합니다.

   **구독에서 IoT Hub를 선택합니다.** : 이 라디오 단추를 선택합니다.

   **구독**: 이 자습서를 위해 사용 중인 Azure 구독을 선택합니다.

   **IoT Hub**: 이 자습서에 사용하고 있는 IoT Hub를 선택합니다.

   **엔드포인트**: **메시징** 을 선택합니다.

   **공유 액세스 정책 이름**: Stream Analytics 작업에서 IoT 허브에 사용할 공유 액세스 정책의 이름을 선택합니다. 이 자습서에서는 ‘서비스’를 선택할 수 있습니다. ‘서비스’ 정책은 기본적으로 새 IoT 허브에서 만들어지고 IoT 허브를 통해 공개되는 클라우드 쪽 엔드포인트에서 보내고 받을 수 있는 권한을 부여합니다. 자세히 알아보려면 [액세스 제어 및 권한](iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.

   **공유 액세스 정책 키**: 이 필드는 선택한 공유 액세스 정책 이름에 따라 자동으로 채워집니다.

   **소비자 그룹**: 이전에 만든 소비자 그룹을 선택합니다.

   다른 모든 필드는 기본값으로 그대로 둡니다.

   ![Azure에서 Stream Analytics 작업에 입력 추가](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. **저장** 을 선택합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지** 에서 **출력** 을 선택합니다.

2. **출력** 창에서 **추가** 및 **Power BI** 를 선택합니다.

3. **Power BI - 새 출력** 창에서 **권한 부여** 를 선택하고 프롬프트에 따라 Power BI 계정에 로그인합니다.

4. Power BI에 로그인한 후 다음 정보를 입력합니다.

   **출력 별칭**: 출력의 고유한 별칭입니다.

   **그룹 작업 영역**: 대상 그룹 작업 영역을 선택합니다.

   **데이터 세트 이름**: 데이터 세트 이름을 입력합니다.

   **테이블 이름**: 테이블 이름을 입력합니다.

   **인증 모드**: 기본값으로 유지합니다.

   ![Azure에서 Stream Analytics 작업에 출력 추가](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. **저장** 을 선택합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지** 에서 **쿼리** 를 선택합니다.

2. `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다.

3. `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다.

   ![Azure에서 Stream Analytics 작업에 쿼리 추가](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. **쿼리 저장** 을 선택합니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 **개요** 를 선택한 다음, **시작** > **지금** > **시작** 을 선택합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨** 에서 **실행 중** 으로 변경됩니다.

![Azure에서 Stream Analytics 작업 실행](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Power BI 보고서를 만들고 게시하여 데이터 시각화

다음 단계에서는 Power BI 서비스를 사용하여 보고서를 만들고 게시하는 방법을 보여 줍니다. Power BI에서 “새 모양”을 사용하려는 경우 일부 내용을 수정하여 다음 단계를 수행할 수 있습니다. “새 모양”의 차이점 및 탐색 방법을 이해하려면 [Power BI 서비스의 ‘새 모양’](/power-bi/consumer/service-new-look)을 참조하세요.

1. 애플리케이션 예제가 사용자 디바이스에서 실행 중인지 확인합니다. 그렇지 않은 경우 [사용자 디바이스 설정](./iot-hub-raspberry-pi-kit-node-get-started.md)에 있는 자습서를 참조할 수 있습니다.

2. [Power BI](https://powerbi.microsoft.com/en-us/) 계정에 로그인합니다.

3. 사용한 작업 영역인 **내 작업 영역** 을 클릭합니다.

4. **데이터 세트** 를 선택합니다.

   Stream Analytics 작업에 대한 출력을 만들 때 지정한 데이터 세트가 표시됩니다.

5. 만든 데이터 세트에 대해 **보고서 추가**(데이터 세트 이름 오른쪽의 첫 번째 아이콘)를 선택합니다.

   ![Microsoft Power BI 보고서 만들기](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. 시간이 지남에 따라 실시간 온도를 표시하는 꺾은선형 차트를 만듭니다.

   1. 보고서 만들기 페이지의 **시각화** 창에서 꺾은선형 차트 아이콘을 선택하여 꺾은선형 차트를 추가합니다.

   2. **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다.

   3. **시각화** 창에서 **EventEnqueuedUtcTime** 을 **축** 으로 끌어갑니다.

   4. **온도** 를 **값** 으로 끌어갑니다.

      꺾은선형 차트가 만들어집니다. x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 센서의 온도를 표시합니다.

      ![온도에 대한 꺾은선형 차트를 Microsoft Power BI 보고서에 추가](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. 시간이 지남에 따라 실시간 습도를 표시하는 꺾은선형 차트를 만듭니다. 이렇게 하려면 캔버스의 빈 부분을 클릭하고 위의 동일한 단계를 수행하여 x축에 **EventEnqueuedUtcTime** 을 배치하고 y축에 **humidity** 를 배치합니다.

   ![습도에 대한 꺾은선형 차트를 Microsoft Power BI 보고서에 추가](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. **저장** 을 선택하여 보고서를 저장합니다.

9. 왼쪽 창에서 **보고서** 를 선택한 다음, 방금 만든 보고서를 선택합니다.

10. **파일** > **웹에 게시** 를 선택합니다.

    ![Microsoft Power BI 보고서에 대해 웹에 게시 선택](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Embed 태그 만들기를 사용하려면 관리자에게 문의하라는 알림이 표시되면 관리자에게 문의해야 할 수 있습니다. 이 단계를 완료하려면 먼저 embed 태그 만들기를 사용하도록 설정해야 합니다.
    >
    > ![관리자에게 문의 알림](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. **Embed 태그 만들기** 를 선택하고 **게시** 를 선택합니다.

보고서 액세스를 위해 누구와도 공유할 수 있는 보고서 링크 및 보고서를 블로그나 웹 사이트에 통합하는 데 사용할 수 있는 코드 조각이 제공됩니다.

![Microsoft Power BI 보고서 게시](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

모바일 디바이스에서 Power BI 대시보드 및 보고서를 보고 상호 작용할 수 있는 [Power BI 모바일 앱](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)도 제공합니다.

## <a name="next-steps"></a>다음 단계

Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화하는 다른 방법은 [웹앱을 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)를 참조하세요.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
