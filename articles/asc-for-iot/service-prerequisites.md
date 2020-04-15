---
title: 구성 요소 & 전제 조건
description: IoT 서비스 필수 구성 품에 대 한 Azure 보안 센터를 시작 하는 데 필요한 모든 것에 대 한 세부 정보입니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310576"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>IoT 필수 구성 조건을 위한 Azure 보안 센터

이 문서에서는 IoT 서비스에 대한 Azure 보안 센터의 다양한 구성 요소, 시작해야 하는 사항에 대한 설명을 제공하고 서비스를 이해하는 데 도움이 되는 기본 개념에 대해 설명합니다.

## <a name="minimum-requirements"></a>최소 요구 사항

- IoT 허브 표준 계층
  - RBAC 역할 **소유자** 수준 권한
- [로그 분석 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure 보안 센터(권장)
  - Azure 보안 센터의 사용은 권장 사항이지 요구 사항이 아닙니다. Azure 보안 센터가 없으면 IoT Hub 내에서 다른 Azure 리소스를 볼 수 없습니다.

## <a name="working-with-azure-security-center-for-iot-service"></a>IoT 서비스를 위한 Azure 보안 센터와 협력

Azure IoT 허브 및 Azure 보안 센터를 사용하여 IoT 인사이트 및 보고를 위한 Azure 보안 센터를 사용할 수 있습니다. Azure IoT Hub에서 IoT용 Azure 보안 센터를 사용하려면 **소유자** 수준 권한이 있는 계정이 필요합니다. IoT Hub에서 IoT용 ASC를 사용하도록 설정하면 IoT 통찰력을 위한 Azure 보안 센터가 Azure IoT Hub의 **보안** 기능으로 표시되고 Azure 보안 센터의 **IoT로** 표시됩니다.

## <a name="supported-service-regions"></a>지원되는 서비스 지역

IoT용 Azure 보안 센터는 현재 다음 Azure 리전에서 IoT 허브에 대해 지원됩니다.

- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부2
- 미국 중남부
- 미국 중북부
- 캐나다 중부
- 캐나다 동부
- 북유럽
- 브라질 남부
- 프랑스 중부
- 영국 서부
- 영국 남부
- 서유럽
- 북유럽
- 일본 서부
- 일본 동부
- 오스트레일리아 남동부
- 오스트레일리아 동부
- 동아시아
- 동남아시아
- 한국 중부
- 한국 남부
- 인도 중부
- 인도 남부

IoT용 Azure 보안 센터는 모든 유럽 지역의 모든 트래픽을 서유럽 지역 데이터 센터로, 나머지 모든 지역을 미국 중부 지역 데이터 센터로 라우팅합니다.

## <a name="wheres-my-iot-hub"></a>내 IoT 허브는 어디에 있습니까?

시작하기 전에 IoT Hub 위치를 확인하여 서비스 가용성을 확인하십시오.

1. IoT Hub를 엽니다.
1. **개요**를 클릭합니다.
1. 나열된 위치가 [지원되는 서비스 지역](#supported-service-regions)중 하나와 일치하는지 확인합니다.

## <a name="supported-platforms-for-agents"></a>에이전트를 위한 지원되는 플랫폼

IoT 에이전트용 Azure 보안 센터는 증가하는 장치 및 플랫폼 목록을 지원합니다. [지원되는 플랫폼 목록을](how-to-deploy-agent.md) 참조하여 기존 또는 계획된 장치 라이브러리를 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure IoT 보안 [개요](overview.md) 읽기
- [서비스 사용](quickstart-onboard-iot-hub.md) 방법 알아보기
- [IoT FAQ를 위한 Azure 보안 센터](resources-frequently-asked-questions.md) 읽기
- [IoT 경고를 위한 Azure 보안 센터를 이해하는](concept-security-alerts.md) 방법 알아보기
