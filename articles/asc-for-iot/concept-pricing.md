---
title: IoT 비용에 대한 Azure 보안 센터 이해 | 마이크로 소프트 문서
description: IoT용 Azure 보안 센터와 관련된 비용과 이를 제어하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348514"
---
# <a name="pricing-and-associated-costs"></a>가격 책정 및 관련 비용

이 문서에서는 IoT 가격 책정 모델에 대한 Azure 보안 센터에 대해 설명하고, 관련된 모든 비용을 요약하고, 이를 관리하는 방법을 설명합니다.

## <a name="pricing"></a>가격 책정

IoT 가격 책정 모델에 대한 Azure 보안 센터는 두 부분으로 구성되며 IoT용 Azure 보안 센터에서 IoT Hub를 [사용하도록 설정하면](quickstart-onboard-iot-hub.md) 요금이 청구됩니다.

- 장치별 비용 - IoT Hub 로그 분석을 기반으로 하는 기본 제공 보안 기능입니다.

- 메시지별 비용 - IoT Edge 또는 리프 디바이스의 보안 메시지를 기반으로 향상된 보안 기능.


자세한 내용은 [보안 센터 가격을](https://azure.microsoft.com/pricing/details/security-center/)참조하십시오.

## <a name="associated-costs"></a>관련 비용

IoT용 Azure 보안 센터에는 직접 가격 책정에 포함되지 않은 관련 비용이 있습니다.


- 로그 분석 스토리지 비용

특정 솔루션 기능을 선택 해제하여 관련 비용을 절감할 수 있습니다. 설정을 변경하여 옵트아웃합니다.

설정을 변경하려면 다음 을 수행하십시오.

1. IoT 허브를 엽니다.

2. **보안에서** **개요를 클릭합니다.**

3. **설정**을 클릭합니다.

다음 표에서는 각 옵션의 관련 비용과 의미에 대한 요약을 제공합니다.

|     | 사용 | 주석 |
| --- | --- | --- |
| **로그 분석 스토리지** |  |
| 장치 권장 사항 및 경고| 서비스에서 생성된 보안 권장 사항 및 경고 | 선택 사항이 아닙니다. |
| 원시 보안 데이터| 보안 에이전트가 수집한 IoT 장치의 원시 보안 데이터 | _원시 장치 보안 이벤트 저장_ 사용 안 함 |
|

>[!Important]
> 옵트아웃은 IoT 보안 기능 가용성을 위해 Azure 보안 센터에 심각한 영향을 미칩니다. 
  
| 옵트아웃 | 영향 |
| --- | --- |
| _트윈 메타데이터 컬렉션_ | [사용자 지정 경고](quickstart-create-custom-alerts.md) 사용 안 함 |
| | IoT 에지 매니페스트 권장 사항 사용 안 함 |
| | 장치 ID 기반 권장 사항 및 경고 사용 안 함 |
| _원시 장치 보안 이벤트 저장_ | 장치 OS 기준 권장 사항에 대한 세부 정보를 사용할 수 없습니다. |
| | [경고](concept-security-alerts.md) 및 [권장 사항](concept-recommendations.md) 조사에 대한 세부 정보는 제공되지 않습니다. |
|


## <a name="see-also"></a>참조

- 원시 [보안 데이터에](how-to-security-data-access.md) 액세스
- [디바이스 조사](how-to-investigate-device.md)
- [보안 권장 사항](concept-recommendations.md) 이해 및 탐색
- [보안 경고](concept-security-alerts.md) 이해 및 탐색
