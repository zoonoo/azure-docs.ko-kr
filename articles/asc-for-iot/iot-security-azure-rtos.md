---
title: Azure RTOS 개요의 보안 모듈
description: IoT 서비스의 Azure Security Center 일부로 Azure RTOS 지원 및 구현에 대 한 보안 모듈에 대해 자세히 알아보세요.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514478"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>개요: Azure RTOS (미리 보기)에 대 한 보안 모듈

IoT RTOS 보안 모듈에 대 한 Azure Security Center는 Azure RTOS 장치에 대 한 포괄적인 보안 솔루션을 제공 합니다. Azure RTOS는 이제 기본 제공 되는 Azure IoT 보안 모듈과 함께 제공 되며 실시간 운영 체제 장치에서 일반적인 위협과 잠재적 악성 활동에 대 한 검사를 제공 합니다. 

![IoT Azure RTOS의 Azure Security Center](./media/architecture/azure-rtos-security-monitoring.png)


Azure RTOS의 보안 모듈은 다음과 같은 기능을 제공 합니다. 
- 악의적인 네트워크 활동 검색
- 사용자 지정 경고 기반, 장치 동작 기준 지정
- 장치 보안 예방 강화

### <a name="detection-of-malicious-network-activities"></a>악의적인 네트워크 작업 검색

각 장치의 인바운드 및 아웃 바운드 네트워크 활동이 모니터링 됩니다 (지원 되는 프로토콜: TCP, UDP, IPv4 및 IPv6의 ICMP). IoT에 대 한 Azure Security Center는 Microsoft 위협 인텔리전스 피드에 대해 이러한 각 네트워크 활동을 검사 합니다. 피드는 전 세계에 수집 된 수백만 개의 고유한 위협 지표로 실시간으로 업데이트 됩니다. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>사용자 지정 경고를 기반으로 하는 장치 동작 기준선 지정

기준 기준선을 통해 장치를 보안 그룹으로 클러스터링 하 고 각 그룹의 예상 동작을 정의할 수 있습니다. IoT 장치는 일반적으로 잘 정의 되 고 제한 된 시나리오에서 작동 하도록 설계 되었으므로 매개 변수 집합을 사용 하 여 예상 동작을 정의 하는 기준선을 쉽게 만들 수 있습니다. 기준선의 모든 편차는 경고를 트리거합니다. 

### <a name="improve-your-device-security-hygiene"></a>장치 보안 예방 강화

IoT에 권장 되는 인프라 Azure Security Center 활용 하면 장치의 보안 상태에 영향을 줄 수 있는 환경에서 발생 하는 문제에 대 한 지식과 정보를 얻을 수 있습니다. 약한 IoT 장치 보안 상태를 사용 하면 보안은 항상 모든 조직 내의 가장 약한 링크로 측정 되기 때문에 변경 되지 않은 상태로 유지 되는 경우 잠재적인 공격이 성공할 수 있습니다. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS 장치 보호 시작 하기

Azure RTOS의 보안 모듈은 장치에 대 한 무료 다운로드로 제공 됩니다. IoT cloud service에 대 한 Azure Security Center는 Azure 구독 당 30 일 평가판으로 제공 됩니다. 시작 하려면 [Azure RTOS 용 보안 모듈](https://github.com/azure-rtos/iot-security-module-preview) 을 다운로드 하세요. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure RTOS 서비스의 보안 모듈에 대해 알아보았습니다. 보안 모듈에 대 한 자세한 내용을 알아보고 시작 하려면 다음 문서를 참조 하세요.

- [Azure RTOS IoT 보안 모듈 개념](concept-rtos-security-module.md)
- [빠른 시작: Azure RTOS IoT 보안 모듈](quickstart-azure-rtos-security-module.md)


