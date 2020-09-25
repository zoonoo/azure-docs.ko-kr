---
title: '빠른 시작: Azure RTOS용 보안 모듈 구성 및 사용'
description: Azure IoT Hub에서 Azure RTOS용 보안 모듈 서비스를 온보드하고 활성화하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945536"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>빠른 시작: Azure RTOS용 보안 모듈(미리 보기)

이 문서에서는 시작하기 전에 필수 구성 요소에 대해 설명하고 IoT Hub에서 Azure RTOS용 보안 모듈 서비스를 활성화하는 방법을 설명합니다. 현재 IoT Hub가 없는 경우 시작하려면 [Azure Portal을 사용하여 IoT Hub 만들기](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)를 참조하세요.

> [!NOTE]
> Azure RTOS용 보안 모듈은 표준 계층 IoT Hub에서만 지원됩니다.

## <a name="prerequisites"></a>필수 구성 요소 

### <a name="supported-devices"></a>지원되는 디바이스

- ST STM32F746G 검색 키트
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

[Azure RTOS GitHub용 보안 모듈 리소스](hhtps://github.com/azure-rtos/azure-iot-preview/releases)에서 선택한 특정 보드 및 도구(IAR, 세미 IDE 또는 PC)에 대한 .zip 파일 중 하나를 다운로드, 컴파일 및 실행합니다.

### <a name="azure-resources"></a>Azure 리소스

시작하기 위한 다음 단계는 Azure 리소스를 준비하는 것입니다. IoT Hub가 필요하며 Log Analytics 작업 영역을 제안합니다. IoT Hub의 경우 디바이스에 연결하기 위한 IoT Hub 연결 문자열이 필요합니다. 
  
### <a name="iot-hub-connection"></a>IoT Hub 연결

시작하려면 IoT Hub 연결이 필요합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다.
1. IoT 연결 문자열을 [구성 파일](how-to-azure-rtos-security-module.md)에 복사합니다.


연결 자격 증명은 사용자 애플리케이션 구성 **HOST_NAME**, **DEVICE_ID** 및 **DEVICE_SYMMETRIC_KEY**에서 가져옵니다.

Azure RTOS용 보안 모듈은 **MQTT** 프로토콜을 기반으로 하는 Azure IoT 미들웨어 연결을 사용합니다.


### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역

IoT Hub의 Log Analytics 수집은 기본적으로 Defender for IoT 솔루션이 해제되어 있습니다. Azure RTOS용 보안 모듈을 사용하도록 설정하려면 다음을 수행합니다. 
1. Azure Portal에서 IoT Hub로 이동합니다.
1. **보안** 메뉴에서 **설정**을 선택합니다.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Azure RTOS에 대한 액세스 데이터 수집 옵션"::: 
1. **데이터 수집**을 선택합니다. 
1. **작업 영역 구성** 옵션에서 토글을 **껴기**로 전환합니다. 
1. 새 Log Analytics 작업 영역을 만들거나 기존 작업 영역을 선택합니다. **원시 보안 데이터에 대한 액세스** 옵션이 선택되어 있는지 확인합니다. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="데이터 수집 옵션과 원시 보안 데이터 옵션이 모두 선택되어 표시되는 Azure RTOS 구성":::
1. **저장**을 선택합니다.
1. Azure 리소스 목록으로 돌아가서 사용자가 만들었거나 연결한 Log Analytics 작업 영역이 IoT Hub에 대해 사용하도록 설정되어 있는지 확인합니다.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Azure 리소스 목록을 확인하여 IoT Hub에 대해 추가된 올바른 Log Analytics 작업 영역이 추가되었는지 확인"::: 

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 솔루션 구성 및 사용자 지정을 완료합니다.

> [!div class="nextstepaction"]
> [Azure RTOS용 보안 모듈 구성](how-to-azure-rtos-security-module.md)
