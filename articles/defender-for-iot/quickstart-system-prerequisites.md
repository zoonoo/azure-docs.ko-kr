---
title: '빠른 시작: 시스템 필수 구성 요소'
description: 이 빠른 시작에서는 Azure Defender for IoT를 실행하는 데 필요한 시스템 필수 구성 요소를 가져옵니다.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382949"
---
# <a name="quickstart-system-prerequisites"></a>빠른 시작: 시스템 필수 구성 요소

이 문서에서는 Azure Defender for IoT를 실행하기 위한 시스템 필수 구성 요소가 나열되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 없음

## <a name="minimum-requirements"></a>최소 요구 사항

- SPAN 포트를 통한 트래픽 모니터링을 지원하는 네트워크 스위치입니다.
- NTA 센서용 하드웨어 어플라이언스.
- Azure 구독 기여자 역할. 커밋된 디바이스를 정의하고 Azure Sentinel에 연결하기 위해 등록하는 동안에만 필요합니다.
- 클라우드 연결 관리를 위한 Azure IoT Hub(체험 또는 표준 계층) **기여자** 역할. **Azure Defender for IoT** 기능이 활성화되어 있는지 확인합니다.
- 디바이스 수준 Defender-IoT-micro-agent 지원을 위해 Defender for IoT 에이전트는 증가하는 디바이스 및 플랫폼 목록을 지원합니다. [지원되는 플랫폼 목록](how-to-deploy-agent.md)을 참조하세요.

## <a name="supported-service-regions"></a>지원되는 서비스 지역

Defender for IoT는 모든 유럽 지역에서 유럽 서부 지역 데이터 센터로 모든 트래픽을 라우팅합니다. 나머지 모든 지역의 트래픽을 미국 중부 지역 데이터 센터로 라우팅합니다.

자세한 내용은 [IoT Hub 지원 지역](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [필수 어플라이언스 식별](how-to-identify-required-appliances.md)
