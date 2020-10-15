---
title: 구성 요소 & 필수 구성 요소
description: IoT 서비스 필수 구성 요소에 대해 Azure Defender를 시작 하는 데 필요한 모든 항목에 대 한 세부 정보입니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089182"
---
# <a name="azure-defender-for-iot-prerequisites"></a>IoT 용 Azure Defender 필수 구성 요소

이 문서에서는 IoT 서비스용 Defender 서비스의 다양 한 구성 요소에 대해 설명 하 고, 서비스를 이해 하는 데 도움이 되는 기본 개념을 설명 합니다.

## <a name="minimum-requirements"></a>최소 요구 사항

- IoT 및 OT 장치에 대 한 에이전트 없는 모니터링 (CyberX 기술을 기반으로)
    - 범위 포트를 통한 트래픽 모니터링을 지 원하는 네트워크 스위치
    - NTA 센서에 대 한 하드웨어 어플라이언스, 자세한 내용은 [인증 된 하드웨어](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 를 참조 하세요.
    - Azure Subscription **기여자** 역할 (커밋된 장치를 정의 하기 위해 등록 하는 동안에만 필요)
    - IoT Hub (무료 또는 표준 계층) **기여자** 역할 (클라우드 연결 관리의 경우)
- Azure IoT Hub를 통해 관리 되는 IoT 장치에 대 한 보안
    - IoT Hub (표준 계층) **참가자** 역할
    - IoT Hub: **IoT 용 Azure Defender** 기능 토글을 사용 하도록 설정 해야 합니다.
    - 장치 수준 보안 모듈 지원  
        - IoT 용 Defender 에이전트는 장치 및 플랫폼의 증가 하는 목록을 지원 합니다. [지원 되는 플랫폼 목록](how-to-deploy-agent.md) 을 참조 하세요.


## <a name="supported-service-regions"></a>지원되는 서비스 지역

자세한 내용은 [지원 되는 지역 IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) 를 참조 하세요. 

IoT 용 Defender는 모든 유럽 지역의 모든 트래픽을 유럽 서부 지역 데이터 센터 및 모든 나머지 지역에서 미국 중부 지역 데이터 센터로 라우팅합니다.

## <a name="next-steps"></a>다음 단계

- Azure IoT 보안 [개요](overview.md) 를 참조 하십시오.
- [서비스를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법 알아보기
- [IoT 용 DEFENDER FAQ](resources-frequently-asked-questions.md) 읽기
- [IoT 경고에 대 한 Defender를 이해](concept-security-alerts.md) 하는 방법 살펴보기
