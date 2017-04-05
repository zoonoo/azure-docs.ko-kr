---
title: "Azure IoT Hub에서 센서 데이터의 실시간 데이터 시각화 – Web Apps | Microsoft Docs"
description: "Azure Web Apps를 사용하여 센서에서 수집하여 Azure IoT Hub로 보낸 온도 및 습도 데이터를 시각화합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "실시간 데이터 시각화, 라이브 데이터 시각화, 센서 데이터 시각화"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 788b3a6ce98c6668a8efc41f66c7e39b8aeda2db
ms.lasthandoff: 03/30/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Azure Web Apps를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화

![센서, IoT 장치, IoT Hub 및 Azure 웹앱 간 연결](media/iot-hub-live-data-visualization-in-web-apps/1_sensor-iot-device-azure-iot-hub-web-app-connection.png)

> [!NOTE]
> 이 자습서를 시작하기 전에 [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)을 완료했는지 확인합니다. [Azure IoT Hub에 ESP8266 연결](/iot-hub-arduino-huzzah-esp8266-get-started.md)에서 IoT 장치와 IoT Hub를 설정하고 장치에서 실행할 샘플 응용 프로그램을 배포합니다. 응용 프로그램에서 수집된 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

이 단원에서는 Azure 웹앱에서 호스팅되는 웹 응용 프로그램을 실행하여 Azure IoT Hub에서 받는 실시간 센서 데이터를 시각화하는 방법에 대해 알아봅니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="what-you-do"></a>수행할 작업

- Azure Portal에서 Azure 웹앱을 만듭니다.
- 소비자 그룹을 추가하여 IoT Hub에서 데이터 액세스 준비
- IoT Hub에서 센서 데이터를 읽도록 웹앱 구성
- 웹앱에서 호스팅할 웹 응용 프로그램 업로드
- 웹앱을 열어 IoT Hub에서 실시간 온도 및 습도 데이터 확인

## <a name="what-you-need"></a>필요한 항목

- 다음 요구 사항을 다루는 [Azure IoT Hub에 ESP8266 연결](iot-hub-arduino-huzzah-esp8266-get-started.md) 자습서
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 응용 프로그램
- Git. ([Git 다운로드](https://www.git-scm.com/downloads))

## <a name="create-an-azure-web-app"></a>Azure 웹앱 만들기

1. [Azure Portal](https://ms.portal.azure.com/)에서 **새로 만들기** > **웹 + 모바일** > **웹앱**을 차례로 클릭합니다.
1. 고유한 작업 이름을 입력하고, 구독을 확인하며, 리소스 그룹 및 위치를 지정하고, **대시보드에 고정**을 선택한 다음, **만들기**를 클릭합니다.

   리소스 그룹이 있는 위치와 동일한 위치를 선택하는 것이 좋습니다. 이렇게 하면 데이터 전송 속도를 높이고 비용을 절감할 수 있습니다.

   ![Azure 웹앱 만들기](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

소비자 그룹은 응용 프로그램에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 이 단원에서는 웹앱에서 IoT Hub의 데이터를 읽는 데 사용할 소비자 그룹을 만듭니다.

IoT Hub에 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub를 엽니다.
1. 왼쪽 창에서 **끝점**을 클릭하고, 가운데 창에서 **이벤트**를 선택하고, 오른쪽 창의 **소비자 그룹** 아래에서 이름을 입력한 다음, **저장**을 클릭합니다.

   ![Azure IoT Hub에서 소비자 그룹 만들기](media/iot-hub-live-data-visualization-in-web-apps/3_add-consumer-group-iot-hub-azure.png)

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>IoT Hub에서 데이터를 읽도록 웹앱 구성

1. 방금 프로비전한 웹앱을 엽니다.
1. **응용 프로그램 설정**을 클릭한 다음 **앱 설정** 아래에서 다음 키/값 쌍을 추가합니다.

   | 키                                   | 값                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | IoT Hub 탐색기에서 얻음                               |
   | Azure.IoT.IoTHub.DeviceId             | IoT Hub 탐색기에서 얻음                               |
   | Azure.IoT.IoTHub.ConsumerGroup        | IoT Hub에 추가하는 소비자 그룹의 이름  |

   ![Azure 웹앱에 키/값 쌍이 있는 설정 추가](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>웹앱에서 호스팅할 웹 응용 프로그램 업로드

GitHub에서 IoT Hub의 실시간 센서 데이터를 표시하는 웹 응용 프로그램을 사용할 수 있었습니다. Git 리포지토리에서 작동하도록 웹앱을 구성하고, GitHub에서 웹 응용 프로그램을 다운로드하고, Azure에 업로드하여 웹앱에서 호스트하기만 하면 됩니다.

1. 웹앱에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리**를 차례로 클릭합니다.

   ![로컬 Git 리포지토리를 사용하도록 Azure 웹앱 배포 구성](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. **연결 설정**을 클릭하고 Azure에서 Git 리포지토리에 연결하는 데 사용할 사용자 이름과 암호를 만든 다음 **확인**을 클릭합니다.

   ![웹앱을 위해 Azure에서 Git 리포지토리의 사용자 이름과 암호를 설정합니다.](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. **확인**을 클릭하여 구성을 완료합니다.
1. **개요**를 클릭하고 **Git 복제 URL**의 값을 기록해 둡니다.

   ![Azure 웹앱의 Git 복제 URL 가져오기](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. 로컬 컴퓨터에서 명령 창 또는 터미널 창을 엽니다.
1. GitHub에서 웹 응용 프로그램을 다운로드하고, Azure를 업로드하여 웹앱을 호스팅합니다. 이렇게 하려면 다음 명령을 실행합니다.

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<Git 복제 URL\>은 웹앱의 **개요** 페이지에서 있는 Git 리포지토리의 URL입니다.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>웹앱을 열어 IoT Hub에서 실시간 온도 및 습도 데이터 확인

웹앱의 **개요** 페이지에서 URL을 클릭하여 웹앱을 엽니다.

![Azure 웹앱의 URL 가져오기](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

IoT Hub에서 실시간 온도 및 습도 데이터를 볼 수 있습니다.

![실시간 온도 및 습도를 보여 주는 Azure 웹앱 페이지](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>다음 단계
Azure 웹앱을 사용하여 Azure IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화하는 또 다른 방법이 있습니다. [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](iot-hub-explorer-cloud-device-messaging.md)
- [IoT Hub 메시지를 Azure 데이터 저장소에 저장](iot-hub-store-data-in-azure-table-storage.md)

