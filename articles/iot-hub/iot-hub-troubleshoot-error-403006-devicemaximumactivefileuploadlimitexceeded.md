---
title: Azure IoT 허브 오류 403006 장치최대ActiveFileUploadLimit초과 문제 해결
description: 오류 403006 장치해결 방법 이해최대ActiveFileUploadLimit초과
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960842"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

이 문서에서는 **403006 DeviceMaximumActiveFileUploadLimit초과** 오류의 원인과 해결에 대해 설명합니다.

## <a name="symptoms"></a>증상

오류 코드 **403006과** "활성 파일 업로드 요청 수가 10을 초과할 수 없습니다"라는 메시지가 파일 업로드 요청에 실패합니다.

## <a name="cause"></a>원인

각 장치 클라이언트는 [10개의 동시 파일 업로드로](./iot-hub-devguide-quotas-throttling.md#other-limits)제한됩니다. 

파일 업로드가 완료되면 장치가 IoT Hub에 알리지 않으면 제한을 쉽게 초과할 수 있습니다. 이 문제는 일반적으로 신뢰할 수 없는 장치 측 네트워크로 인해 발생 합니다.

## <a name="solution"></a>해결 방법

장치가 [IoT Hub 파일 업로드 완료를](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)즉시 알릴 수 있는지 확인합니다. 그런 다음 [파일 업로드 구성에 대한 SAS 토큰 TTL을 줄이십시오.](iot-hub-configure-file-upload.md)

## <a name="next-steps"></a>다음 단계

파일 업로드에 대한 자세한 내용은 Azure 포털을 사용하여 [IoT Hub를 사용하여 파일 업로드](./iot-hub-devguide-file-upload.md) 및 [IoT Hub 파일 업로드 구성을 참조하세요.](./iot-hub-configure-file-upload.md)