---
title: IoT Hub에서 IoT용 Azure Security Center 서비스 사용 | Microsoft Docs
description: IoT Hub에서 IoT용 Azure Security Center 서비스를 사용하도록 설정하는 방법을 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596954"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>빠른 시작: IoT Hub에서 IoT용 Azure Security Center 서비스 온보딩

이 문서에서는 기존 IoT Hub에서 IoT용 Azure Security Center 서비스를 사용하도록 설정하는 방법에 대해 설명합니다. 현재 IoT Hub가 없는 경우 시작하려면 [Azure Portal을 사용하여 IoT Hub 만들기](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)를 참조하세요. 

> [!NOTE]
> IoT용 Azure Security Center는 현재 표준 계층 IoT Hub만 지원합니다.
> IoT용 Azure Security Center는 단일 허브 솔루션입니다. 여러 허브가 필요한 경우 여러 개의 IoT용 Azure Security Center 솔루션이 필요합니다. 

## <a name="prerequisites-for-enabling-the-service"></a>서비스를 사용하도록 설정하기 위한 필수 구성 요소

- Log Analytics 작업 영역
  - IoT용 Azure Security Center는 기본적으로 **보안 경고** 및 **추천 사항**이라는 두 가지 유형의 정보를 Log Analytics 작업 영역에 저장합니다. 
  - 추가 정보 유형인 **원시 이벤트**의 스토리지를 추가하도록 선택할 수 있습니다. **원시 이벤트**를 Log Analytics에 저장하면 추가 스토리지 비용이 발생합니다. 
- IoT Hub(표준 계층)
- [서비스 필수 구성 요소](service-prerequisites.md) 모두 충족 

|지원되는 Azure 서비스 지역 | ||
|---|---|---|
| 미국 중부 |미국 동부 |미국 동부 2 |
| 미국 중서부 |미국 서부 |미국 서부2 |
| 미국 중남부|미국 중북부 | 캐나다 중부|
| 캐나다 동부| 북유럽|브라질 남부|
| 프랑스 중부| 영국 서부|영국 남부|
|서유럽|북유럽| 일본 서부|
|일본 동부 | 오스트레일리아 남동부|오스트레일리아 동부|
|동아시아| 동남아시아| 한국 중부|
|한국 남부| 인도 중부| 인도 남부|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>IoT Hub에서 IoT용 Azure Security Center를 사용하도록 설정 

IoT Hub에서 보안을 사용하도록 설정하려면 다음을 수행합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다. 
1. **보안** 메뉴 아래에서 **IoT 솔루션 보안**을 클릭합니다.
1. **사용**을 기본값으로 선택한 상태로 둡니다. 
1. Log Analytics 작업 영역을 선택합니다.
1. Log Analytics 작업 영역 세부 정보를 제공합니다. 
   - **컬렉션 쌍** 토글을 **켜기**로 전환하여 **컬렉션 쌍**을 사용하도록 선택합니다.
   - Log Analytics에서 **원시 디바이스 보안 이벤트 저장**을 선택하여 스토리지의 기본 정보 유형 외에도 **원시 이벤트**를 저장하도록 선택합니다. **원시 이벤트** 토글을 **켜기**로 그대로 둡니다. 
    
1. **저장**을 클릭합니다. 

축하합니다! IoT Hub에서 IoT용 Azure Security Center를 사용하도록 설정했습니다. 

## <a name="next-steps"></a>다음 단계

솔루션을 구성하려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [솔루션 구성](quickstart-configure-your-solution.md)


