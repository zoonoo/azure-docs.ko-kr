---
title: IoT Edge 용 ASC IoT 보안 모듈에 대 한 이해 | Microsoft Docs
description: IoT Edge에 대 한 아키텍처와 ASC의 IoT 보안 모듈에 대 한 기능을 이해 합니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 68117370e21c7b667bde5447cef510a4e7c77df0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580765"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge 보안 모듈

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전을 서비스 수준 계약 없이 제공 됩니다 및 프로덕션 worklo§1ads에 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) 에 지에서 비즈니스 워크플로 수행 하는 강력한 기능을 제공 합니다.
IoT Edge는 IoT 환경에서 수행 하는 핵심 있도록 특히 매력적인 악의적인 행위자에 대 한 합니다.

ASC IoT 보안 모듈에 대 한 포괄적인 보안 솔루션을 제공 하 여 IoT Edge 장치입니다.
ASC IoT 모듈에 대 한 집계 하며 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 분석 하 여 실행 가능한 보안 권장 사항 및 경고를 수집 합니다.

마찬가지로 ASC IoT 장치에 대 한 IoT 보안 에이전트에 대 한 IoT Edge 모듈에 대 한 ASC는 사용자 지정 모듈 쌍을 통해.
참조 [에이전트를 구성할](how-to-agent-configuration.md) 에 대해 자세히 알아보세요.

ASC IoT Edge에 대 한 IoT 보안 모듈에 대 한 다음 기능을 제공 합니다.

- 기본 운영 체제 (Linux) 및 IoT Edge 컨테이너 시스템에서 원시 보안 이벤트를 수집합니다.
  
  참조 [ASC IoT 에이전트 구성을](how-to-agent-configuration.md) 사용 가능한 보안 데이터 수집기에 자세히 알아보려면 합니다.

- IoT Edge 배포 매니페스트 분석 합니다.

- 원시 보안 이벤트를 보내는 메시지로 집계 [IoT Edge Hub](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub)합니다.

- 보안 모듈 쌍의 구성을 사용 하 여 제거 합니다.

  참조 [IoT 에이전트용를 ASC 구성](how-to-agent-configuration.md) 에 대해 자세히 알아보세요.

ASC IoT Edge에 대 한 IoT 보안 모듈에 대 한 IoT Edge에서 특권된 모드에서 실행 됩니다.
특권된 모드는 운영 체제를 모니터링 하는 모듈 및 다른 IoT Edge 모듈을 허용 해야 합니다.

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

IoT Edge에 대 한 IoT 보안 모듈에 대 한 ASC는 현재 Linux에 사용할만.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Edge에 대 한 아키텍처와 ASC의 IoT 보안 모듈에 대 한 기능에 대 한 학습 있습니다.

ASC를 사용 하 여 IoT 배포에 대 한 시작 작업을 계속 하려면 다음 문서를 사용 합니다.

- 배포 [IoT Edge에 대 한 보안 모듈](how-to-deploy-edge.md)
- 자세한 방법 [보안 모듈 구성](how-to-agent-configuration.md)
- IoT 용 ASC 검토 [필수 구성 요소 서비스](service-prerequisites.md)
- 자세한 방법 [IoT Hub에 IoT 서비스에 대 한 ASC를 사용 하도록 설정](quickstart-onboard-iot-hub.md)
- 서비스에 대 한 자세한 정보는 [ASC IoT faq](resources-frequently-asked-questions.md)