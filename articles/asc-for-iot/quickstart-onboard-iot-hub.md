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
ms.date: 10/17/2019
ms.author: mlottner
ms.openlocfilehash: 67361c402bdbc82d5df01709dc962b59671cb2aa
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991409"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>빠른 시작: IoT Hub에서 IoT용 Azure Security Center 서비스 온보딩

이 문서에서는 기존 IoT Hub에서 IoT용 Azure Security Center 서비스를 사용하도록 설정하는 방법에 대해 설명합니다. 현재 IoT Hub가 없는 경우 시작하려면 [Azure Portal을 사용하여 IoT Hub 만들기](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)를 참조하세요. 

> [!NOTE]
> IoT용 Azure Security Center는 현재 표준 계층 IoT Hub만 지원합니다.


## <a name="prerequisites-for-enabling-the-service"></a>서비스를 사용하도록 설정하기 위한 필수 구성 요소

- Log Analytics 작업 영역
  - IoT용 Azure Security Center는 기본적으로 **보안 경고** 및 **추천 사항**이라는 두 가지 유형의 정보를 Log Analytics 작업 영역에 저장합니다. 
  - 추가 정보 유형인 **원시 이벤트**의 스토리지를 추가하도록 선택할 수 있습니다. **원시 이벤트**를 Log Analytics에 저장하면 추가 스토리지 비용이 발생합니다. 
- IoT Hub(표준 계층)
- [서비스 필수 구성 요소](service-prerequisites.md) 모두 충족 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>IoT Hub에서 IoT용 Azure Security Center를 사용하도록 설정 

IoT Hub에서 보안을 사용하도록 설정하려면 다음을 수행합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다. 
1. **보안** 메뉴 아래에서 **IoT 솔루션 보안**을 클릭합니다.    


축하합니다! IoT Hub에서 IoT용 Azure Security Center를 사용하도록 설정했습니다. 

### <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리

IoT 솔루션의 보안을 유지하기 위해 IoT 디바이스, IoT Edge 및 IoT Hub와 주고 받는 수신 및 발신 연결의 IP 주소가 기본적으로 수집 및 저장됩니다. 이 정보는 의심스러운 IP 원본에서 시도하는 비정상적인 연결을 검색하는 데 필요합니다. 알려진 봇넷의 IP 원본 또는 지리적 위치 외부의 IP 원본에서 연결하려는 시도를 예로 들 수 있습니다. IoT용 Azure Security Center 서비스는 언제든지 IP 주소 데이터 수집을 설정 또는 해제할 수 있습니다. 

IP 주소 데이터 수집을 설정 또는 해제하는 방법은 다음과 같습니다. 

1. IoT Hub를 열고 **보안** 메뉴에서 **개요**를 선택합니다. 
2. **설정** 화면을 선택하고, 지리적 위치 및/또는 IP 처리 설정을 원하는 대로 수정합니다.

### <a name="log-analytics-creation"></a>Log Analytics 만들기

IoT용 Azure Security Center가 켜져 있으면 IoT 디바이스, IoT Edge 및 IoT Hub에 대한 원시 보안 이벤트, 경고 및 권장 사항을 저장하기 위한 기본 Azure Log Analytics 작업 영역이 생성됩니다. 매월 Azure Log Analytics 서비스에 수집되는 처음 5GB 데이터는 무료입니다. Azure Log Analytics 작업 영역에 수집된 모든 데이터(GB)는 처음 31일간 무료로 보존됩니다. [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) 가격 책정에 대해 자세히 알아보세요.

Log Analytics의 작업 영역 구성을 변경하는 방법은 다음과 같습니다.

1. IoT Hub를 열고 **보안** 메뉴에서 **개요**를 선택합니다. 
2. **설정** 화면을 선택하고, Log Analytics 설정의 작업 영역 구성을 원하는 대로 수정합니다.

### <a name="customize-your-iot-security-solution"></a>IoT 보안 솔루션 사용자 지정
기본적으로 IoT용 Azure Security Center 솔루션을 켜면 Azure 구독 내 모든 IoT Hub가 자동으로 보호됩니다. 

특정 IoT Hub에서 IoT용 Azure Security Center 서비스를 켜거나 끄는 방법은 다음과 같습니다. 

1. IoT Hub를 열고 **보안** 메뉴에서 **개요**를 선택합니다. 
2. **설정** 화면을 선택하고, Azure 구독에서 IoT Hub의 보안 설정을 원하는 대로 수정합니다.


## <a name="next-steps"></a>다음 단계

솔루션을 구성하려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [솔루션 구성](quickstart-configure-your-solution.md)


