---
title: "Azure Portal을 사용하여 파일 업로드 구성 | Microsoft Docs"
description: "Azure Portal을 사용하여 파일 업로드를 구성하는 방법 개요"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e3ac3e8cee2724b76f51423d1a6757382cca04f0


---
# <a name="configure-file-uploads-using-the-azure-portal"></a>Azure Portal을 사용하여 파일 업로드 구성
## <a name="file-upload"></a>파일 업로드
[file upload functionality in IoT Hub에서 파일 업로드 기능][lnk-upload]을 사용하려면 먼저 Azure Storage 계정을 허브에 연결해야 합니다. **파일 업로드** 설정을 선택하여 수정하려는 IoT hub에 대한 파일 업로드 속성 목록을 표시합니다.

![][13]

**저장소 컨테이너**: Azure Portal을 사용하여 현재 Azure 구독의 Azure Storage 계정에서 Blob 컨테이너를 선택하고 IoT Hub와 연결합니다. 필요에 따라 **Storage 계정** 블레이드에 Azure Storage 계정을 만들고 **컨테이너** 블레이드에 Blob 컨테이너를 만들 수 있습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 장치에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

![][14]

**업로드된 파일에 대한 알림 받기**: 토글을 통해 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

**SAS TTL**: 이 설정은 IoT Hub에서 장치로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

**파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1시간으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

**파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정되지만 슬라이더를 사용하여 다른 값으로 사용자 지정할 수 있습니다.

![][15]

## <a name="next-steps"></a>다음 단계
IoT Hub의 파일 업로드 기능에 대한 자세한 내용은 개발자 가이드의 [장치에서 파일 업로드][lnk-upload]를 참조하세요.

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 장치 대량 관리][lnk-bulk]
* [사용 현황 메트릭][lnk-metrics]
* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [개발자 가이드][lnk-devguide]
* [IoT Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Nov16_HO3-->


