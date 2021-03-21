---
title: 에이전트 기반 솔루션용 Azure Defender for IoT 구성
description: IoT 에이전트 기반 솔루션용 Azure Defender에서 데이터 수집을 구성 하는 방법에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733298"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>에이전트 기반 솔루션용 Azure Defender for IoT 구성  

이 문서에서는 Azure Defender에서 IoT 에이전트 기반 솔루션에 대 한 데이터 수집을 구성 하는 방법을 설명 합니다.

## <a name="configure-data-collection"></a>데이터 수집 구성

Azure Defender에서 IoT 에이전트 기반 솔루션에 대 한 데이터 수집을 구성 하려면 다음을 수행 합니다. 

1. Azure Portal로 이동 하 고 IoT 용 Defender가 연결 된 IoT Hub를 선택 합니다. 

1.  **보안**   메뉴에서 **설정** 을 선택 합니다. 

1.  **데이터 컬렉션** 을 선택 합니다. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="보안 메뉴 설정에서 데이터 컬렉션을 선택 합니다.":::

## <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리 

IoT 솔루션의 보안을 유지 하기 위해 IoT 장치, IoT Edge 및 IoT Hub에 대 한 들어오는 연결 및 나가는 연결의 IP 주소를 기본적으로 수집 하 고 저장 합니다. 이 정보는 필수적 이며 의심 스러운 IP 주소 원본에서 비정상적인 연결을 검색 하는 데 사용 됩니다. 예를 들어 알려진 봇 넷의 IP 주소 원본 또는 지리적 위치 외부의 IP 주소 원본에서 연결을 설정 하려고 시도 하는 경우를 예로 들 수 있습니다. IoT 서비스용 Defender는 언제 든 지 IP 주소 데이터 수집을 사용 하도록 설정 하 고 사용 하지 않도록 설정할 수 있는 유연성을 제공 합니다. 

IP 주소 데이터의 컬렉션을 사용 하거나 사용 하지 않도록 설정 하려면: 

1. IoT Hub를 연 후 ****   보안 메뉴에서 설정을 선택 ****   합니다. 

1.  **데이터 수집** 화면을 선택 하   고, 지리적 위치 및 IP 주소 처리 설정을 필요에 맞게 수정 합니다. 

## <a name="log-analytics-creation"></a>Log Analytics 만들기 

IoT 용 Defender를 사용 하면 보안 경고, 권장 사항 및 원시 보안 데이터를 Log Analytics 작업 영역에 저장할 수 있습니다. IoT for IoT 솔루션에서 IoT Hub의 Log Analytics 수집은 기본적으로 **off** 로 설정 되어 있습니다. IoT 용 Defender를 로그 분석 작업 영역에 연결 하 고 보안 데이터도 저장할 수 있습니다. 

IoT 용 Defender에서 Log Analytics 작업 영역에 기본적으로 저장 되는 정보에는 다음 두 가지 유형이 있습니다.
 
- 보안 경고

- 관련. 

추가 정보 유형의 저장소를로 추가 하도록 선택할 수 있습니다 `raw events` . 

> [!Note] 
>  `raw events`   Log Analytics에 저장 하면 추가 저장소 비용이 발생 합니다. 

Log Analytics에서 마이크로 에이전트로 작업 하도록 설정 하려면 다음을 수행 합니다. 

1. **작업 영역 구성**  >  **데이터 컬렉션** 으로 이동 하 고 설정/해제를  **켜기** 로 전환 합니다. 

1. 새 Log Analytics 작업 영역을 만들거나 기존 작업 영역을 연결 합니다. 

1.  **원시 보안 데이터에 대 한 액세스**   옵션이 선택 되어 있는지 확인 합니다.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="원시 보안 데이터에 대 한 액세스가 선택 되어 있는지 확인 합니다.":::

1.  **저장** 을 선택합니다.

매월 Azure Log Analytics 서비스에 대 한 고객 당 처음 5gb의 데이터 수집는 무료입니다. 모든 기가바이트의 데이터는 Azure Log Analytics 작업 영역에 수집 처음 31 일 동안 무료로 유지 됩니다. 가격 책정에 대 한 자세한 내용은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요. 

Log Analytics의 작업 영역 구성을 변경하는 방법은 다음과 같습니다. 

1. IoT Hub의 **보안** 메뉴에서  **설정** 을 선택 합니다. 

1.  **데이터 수집**   화면을 선택 하 고 필요에 따라 Log Analytics 설정의 작업 영역 구성을 수정 합니다. 

구성 후 Log Analytics 작업 영역에서 경고에 액세스 하려면 다음을 수행 합니다.

1. IoT 용 Defender에서 경고를 선택 합니다.

1. **Log Analytics 작업 영역에서 경고 조사를** 선택 합니다.

구성 후 Log Analytics 작업 영역에서 경고에 액세스 하려면 다음을 수행 합니다.

1. IoT 용 Defender에서 권장 사항을 선택 합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사를** 선택 합니다. 
 
Log Analytics에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [Log Analytics에서 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조 하세요. 

## <a name="turn-off-defender-for-iot"></a>IoT에 대 한 Defender 끄기 

특정 IoT Hub에서 IoT 서비스용 Defender를 설정 하거나 해제 하려면 다음을 수행 합니다. 

1. IoT Hub의 **보안** 메뉴에서  **설정** 을 선택 합니다.

1.  **데이터 수집**   화면을 선택 하 고 필요에 따라 Log Analytics 설정의 작업 영역 구성을 수정 합니다.

## <a name="next-steps"></a>다음 단계 

[솔루션을 구성](quickstart-configure-your-solution.md)하려면 다음 문서로 이동 합니다.