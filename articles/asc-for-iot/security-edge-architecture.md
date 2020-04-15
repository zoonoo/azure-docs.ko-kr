---
title: IoT 에지용 보안 모듈
description: IoT Edge용 IoT 보안 모듈용 Azure 보안 센터의 아키텍처 및 기능을 이해합니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310639"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT 에지 보안 모듈

[Azure IoT Edge는](https://docs.microsoft.com/azure/iot-edge/) 에지에서 비즈니스 워크플로를 관리하고 수행할 수 있는 강력한 기능을 제공합니다.
IoT Edge가 IoT 환경에서 수행하는 핵심 부분은 악의적인 행위자에게 특히 매력적입니다.

IoT 보안 을 위한 Azure 보안 센터 모듈은 IoT Edge 장치에 대한 포괄적인 보안 솔루션을 제공합니다.
IoT용 Azure 보안 센터 모듈은 운영 체제 및 컨테이너 시스템의 원시 보안 데이터를 수집, 집계 및 분석하여 실행 가능한 보안 권장 사항 및 경고로 변환합니다.

IoT 장치를 위한 IoT 보안 에이전트용 Azure 보안 센터와 마찬가지로 IoT Edge 용 Azure 보안 센터 모듈은 모듈 쌍으로 고도로 사용자 정의할 수 있습니다.
자세한 내용은 [에이전트 구성을](how-to-agent-configuration.md) 참조하십시오.

IoT Edge용 IoT 보안 모듈용 Azure 보안 센터는 다음과 같은 기능을 제공합니다.

- 기본 운영 체제(Linux) 및 IoT Edge 컨테이너 시스템에서 원시 보안 이벤트를 수집합니다.

  사용 가능한 보안 데이터 수집기에 대해 자세히 알아보려면 [IoT 에이전트 구성용 Azure 보안 센터를](how-to-agent-configuration.md) 참조하십시오.

- IoT 에지 배포 매니페스트 분석

- 원시 보안 이벤트를 [IoT Edge Hub를](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)통해 전송된 메시지로 집계합니다.

- 보안 모듈 트윈을 사용하여 구성을 제거합니다.

  자세한 내용은 [IoT 에이전트용 Azure 보안 센터 구성을](how-to-agent-configuration.md) 참조하십시오.

IoT Edge용 IoT 보안 모듈용 Azure 보안 센터는 IoT Edge에서 권한 있는 모드에서 실행됩니다.
모듈이 운영 체제 및 기타 IoT Edge 모듈을 모니터링할 수 있도록 하려면 권한 모드가 필요합니다.

## <a name="module-supported-platforms"></a>모듈 지원 플랫폼

IoT Edge용 IoT 보안 모듈용 Azure 보안 센터는 현재 Linux에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Edge용 IoT 보안 모듈용 Azure 보안 센터의 아키텍처 및 기능에 대해 배웠습니다.

IoT 배포를 위한 Azure 보안 센터를 계속 시작하려면 다음 문서를 사용하십시오.

- [IoT 에지를 위한 보안 모듈](how-to-deploy-edge.md) 배포
- [보안 모듈 을 구성하는](how-to-agent-configuration.md) 방법 알아보기
- IoT [서비스 필수 구성 조건을](service-prerequisites.md) 위한 Azure 보안 센터 검토
- [IoT 허브에서 IoT 서비스를 위한 Azure 보안 센터를 활성화하는](quickstart-onboard-iot-hub.md) 방법 알아보기
- [IoT FAQ용 Azure 보안 센터에서](resources-frequently-asked-questions.md) 서비스에 대해 자세히 알아보기
