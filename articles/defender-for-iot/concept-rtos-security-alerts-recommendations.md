---
title: Azure RTOS 기본 제공 & 사용자 지정 가능한 경고 및 권장 사항에 대 한 보안 모듈
description: Azure IoT 보안 모듈-RTOS를 사용 하 여 보안 경고 및 권장 수정 사항에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cf4924f8a9b97487e64e12ab80df92f2b2a81de2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939514"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Azure RTOS 보안 경고 및 권장 사항 (미리 보기)에 대 한 보안 모듈

Azure RTOS 용 보안 모듈은 고급 분석 및 위협 인텔리전스를 사용 하 여 IoT 솔루션을 지속적으로 분석 하 여 잠재적인 악의적인 활동 및 의심 스러운 시스템 수정에 대해 경고 합니다. 예상 되는 장치 동작 및 기준에 대 한 지식을 바탕으로 사용자 지정 경고를 만들 수도 있습니다.

Azure RTOS 경고에 대 한 보안 모듈은 잠재적 손상에 대 한 표시기로 작동 하며 조사 하 고 수정 해야 합니다. Azure RTOS 권장 사항에 대 한 보안 모듈은 수정 및 업데이트할 약한 보안 상태를 식별 합니다. 

이 문서에서는 기본 범위를 기준으로 트리거된 기본 제공 경고 및 권장 사항 목록과, 예상 또는 기준 동작에 따라 사용자 고유의 값으로 사용자 지정할 수 있습니다. 

IoT 서비스의 Defender에서 경고 사용자 지정이 작동 하는 방식에 대 한 자세한 내용은 [사용자 지정 가능한 경고](concept-customizable-security-alerts.md)를 참조 하세요. Azure RTOS의 보안 모듈을 사용할 때 사용자 지정에 사용할 수 있는 특정 경고 및 권장 사항은 다음 표에 자세히 설명 되어 있습니다. 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Azure RTOS 지원 되는 보안 경고에 대 한 보안 모듈

### <a name="device-related-security-alerts"></a>장치 관련 보안 경고

|장치 관련 보안 경고 활동  |경고 이름  |
|---------|---------|
|IP 주소| 의심 스러운 IP 주소와의 통신이 검색 됨|
|X.509 장치 인증서 지문|X.509 장치 인증서 지문 불일치|
|X.509 인증서| X.509 인증서가 만료 됨|
|SAS 토큰| 만료 된 SAS 토큰|
|SAS 토큰| 잘못 된 SAS 토큰 서명|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub 관련 보안 경고

|IoT Hub 보안 경고 작업  |경고 이름  |
|---------|---------|
|인증서 추가    |  IoT Hub에 인증서를 추가 하는 데 실패 했습니다.       |
|진단 설정 추가 또는 편집    | IoT Hub의 진단 설정을 추가 하거나 편집 하려고 했습니다.      |
|인증서 삭제    |  IoT Hub에서 인증서를 삭제 하는 데 실패 했습니다.       |
|진단 설정 삭제    |  IoT Hub에서 진단 설정 삭제 시도를 검색 했습니다.      |
|인증서 삭제 됨    | IoT Hub에서 인증서 삭제가 검색 됨        |
|새 인증서     |  IoT Hub에 새 인증서를 추가 했습니다.       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>Azure RTOS에서 지원 되는 사용자 지정 가능한 경고에 대 한 보안 모듈

### <a name="device-related-customizable-alerts"></a>장치 관련 사용자 지정 가능 경고

|장치 관련 활동 |경고 이름  |
|---------|---------|
|활성 연결|허용 되는 범위에 없는 활성 연결 수|
|**Mqtt** 프로토콜의 클라우드-장치 메시지|**Mqtt** 프로토콜의 클라우드-장치 메시지 수가 허용 되는 범위에 없습니다.|
|아웃 바운드 연결| 허용 되지 않는 IP에 대 한 아웃 바운드 연결|

### <a name="hub-related-customizable-alerts"></a>허브 관련 사용자 지정 가능 경고 

|허브 관련 작업  |경고 이름  |
|---------|---------|
|명령 큐 제거     |  허용 되는 범위를 벗어난 명령 큐 제거 수       |
|**Mqtt** 프로토콜의 클라우드-장치 메시지    |  허용 되는 범위를 벗어난 **Mqtt** 프로토콜의 클라우드-장치 메시지 수       |
|**Mqtt** 프로토콜의 장치-클라우드 메시지    | 허용 되는 범위를 벗어난 **Mqtt** 프로토콜의 장치-클라우드 메시지 수        |
|직접 메서드 호출     |  허용 되는 범위 밖의 직접 메서드 호출 수       |
|**Mqtt** 프로토콜에서 거부 된 클라우드-장치 메시지     |   허용 되는 범위 외부의 **Mqtt** 프로토콜에서 거부 된 클라우드-장치 메시지 수      |
|쌍 모듈 업데이트     |  허용 된 범위를 벗어난 쌍 모듈의 업데이트 수       |
|권한 없는 작업    |  허용 되는 범위를 벗어난 권한 없는 작업 수       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Azure RTOS 지원 권장 사항에 대 한 보안 모듈

### <a name="device-related-recommendations"></a>장치 관련 권장 사항

|장치 관련 활동  |권장 사항 이름 |
|---------|---------|
|인증 자격 증명    |  여러 장치에서 사용 하는 동일한 인증 자격 증명       |

### <a name="hub-related-recommendations"></a>허브 관련 권장 사항

|IoT Hub 관련 작업  |권장 사항 이름 |
|---------|---------|
|IP 필터 정책   |  기본 IP 필터 정책은 **거부** 로 설정 해야 합니다.  |
|IP 필터 규칙| IP 필터 규칙에 많은 IP 범위가 포함 되어 있습니다.|
|진단 로그|IoT Hub에서 진단 로그를 사용 하도록 설정 하는 제안|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>IoT 경고 및 권장 사항에 대 한 모든 Defender

IoT 서비스 관련 경고 및 권장 사항에 대 한 모든 Defender의 전체 목록은 IoT [보안 경고](concept-security-alerts.md), iot 보안 [권장 사항](concept-recommendations.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure RTOS 용 보안 모듈](quickstart-azure-rtos-security-module.md)
- [Azure RTOS의 보안 모듈 구성 및 사용자 지정](how-to-azure-rtos-security-module.md)
- [Azure RTOS API에 대 한 보안 모듈](azure-rtos-security-module-api.md) 을 참조 하세요.