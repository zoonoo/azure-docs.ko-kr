---
title: IoT Hub에서 IoT용 Azure Security Center 서비스 사용 - 미리 보기 | Microsoft Docs
description: IoT Hub에서 IoT용 Azure Security Center 서비스를 사용하도록 설정하는 방법을 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: 7030ae1c3a28cdd74671dc95dce59cf86cacf4c9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786170"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>빠른 시작: IoT Hub에서 서비스 사용

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT Hub에서 IoT용 Azure Security Center(ASC) 미리 보기 서비스를 사용하도록 설정하는 방법에 대해 설명합니다.  

> [!NOTE]
> IoT용 Azure Security Center는 현재 표준 계층 IoT Hub만 지원합니다.
> IoT용 Azure Security Center는 단일 허브 솔루션입니다. 여러 개의 허브가 필요한 경우 여러 개의 솔루션도 필요합니다. 

## <a name="prerequisites-for-enabling-the-service"></a>서비스를 사용하도록 설정하기 위한 필수 구성 요소

- Log Analytics 작업 영역
  - IoT용 ASC는 기본적으로 **보안 경고** 및  **추천 사항**이라는 두 가지 유형의 정보를 Log Analytics 작업 영역에 저장합니다. 
  - 추가 정보 유형인 **원시 이벤트**의 스토리지를 추가하도록 선택할 수 있습니다. **원시 이벤트**를 Log Analytics에 저장하면 추가 스토리지 비용이 발생합니다. 
- IoT Hub(표준 계층)
- [서비스 필수 구성 요소](service-prerequisites.md) 모두 충족 
- 지원되는 서비스 지역
  - 미국 중부
  - 북유럽
  - 동남아시아

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>IoT Hub에서 IoT용 ASC를 사용하도록 설정 

IoT Hub에서 보안을 사용하도록 설정하려면 다음을 수행합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다. 
2. **보안** 메뉴에서 **개요**를 클릭한 다음, **미리 보기 시작**을 클릭합니다. 
3. **IoT 보안 사용**을 선택합니다. 
4. Log Analytics 작업 영역 세부 정보를 제공합니다. 
   - **원시 이벤트** 토글을 **켜기**로 전환하여 기본 스토리지 정보 유형 외에도 **원시 이벤트**를 저장하도록 선택합니다. 
   - **컬렉션 쌍** 토글을 **켜기**로 전환하여 **컬렉션 쌍**을 사용하도록 선택합니다. 
5. **저장**을 클릭합니다. 

축하합니다! IoT Hub에서 IoT용 ASC를 사용하도록 설정했습니다. 

## <a name="next-steps"></a>다음 단계

솔루션을 구성하는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [솔루션 구성](quickstart-configure-your-solution.md)
