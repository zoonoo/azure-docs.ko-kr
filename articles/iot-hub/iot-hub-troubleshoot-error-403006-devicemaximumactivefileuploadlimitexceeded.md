---
title: 문제 해결 Azure IoT Hub 오류 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: 403006 오류를 해결 하는 방법 이해 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960842"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

이 문서에서는 **403006 DeviceMaximumActiveFileUploadLimitExceeded** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

파일 업로드 요청이 실패 하 고 오류 코드 **403006** 및 "활성 파일 업로드 요청 수가 10을 초과할 수 없습니다." 메시지가 표시 됩니다.

## <a name="cause"></a>원인

각 장치 클라이언트는 [동시 파일 업로드 10](./iot-hub-devguide-quotas-throttling.md#other-limits)개로 제한 됩니다. 

장치에서 파일 업로드가 완료 되 면 IoT Hub에 알리지 않는 경우 제한을 쉽게 초과할 수 있습니다. 이 문제는 일반적으로 신뢰할 수 없는 장치 쪽 네트워크에 의해 발생 합니다.

## <a name="solution"></a>솔루션

장치에서 [파일 업로드 완료 IoT Hub 즉시 알릴](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)수 있는지 확인 합니다. 그런 다음 [파일 업로드 구성에 대해 SAS 토큰 TTL을 줄여](iot-hub-configure-file-upload.md)보세요.

## <a name="next-steps"></a>다음 단계

파일 업로드에 대 한 자세한 내용은 [IoT Hub를 사용 하 여 파일 업로드](./iot-hub-devguide-file-upload.md) 및 [Azure Portal를 사용 하 여 파일 업로드 IoT Hub 구성](./iot-hub-configure-file-upload.md)을 참조 하세요.