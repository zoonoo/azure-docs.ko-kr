---
title: 보안 에이전트 아키텍처
description: IoT 서비스용 Azure Defender에서 사용 되는 에이전트에 대 한 보안 에이전트 아키텍처를 이해 합니다.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 64d90b4d9dc3efbe877230bbc20780b1c4f2d213
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939749"
---
# <a name="security-agent-reference-architecture"></a>보안 에이전트 참조 아키텍처

IoT 용 Azure Defender는 IoT Hub를 통해 보안 데이터를 기록, 처리, 집계 및 전송 하는 보안 에이전트에 대 한 참조 아키텍처를 제공 합니다.

보안 에이전트는 제한 된 IoT 환경에서 작동 하도록 설계 되었으며, 사용 하는 리소스와 비교할 때 제공 하는 값을 기준으로 사용자 지정이 매우 높습니다.

보안 에이전트는 다음과 같은 기능을 지원 합니다.

- 기본 운영 체제 (Linux, Windows)에서 원시 보안 이벤트를 수집 합니다. 사용 가능한 보안 데이터 수집기에 대 한 자세한 내용은 [IoT 에이전트 구성 용 Defender](how-to-agent-configuration.md)를 참조 하세요.

- IoT Hub를 통해 전송 되는 메시지에 원시 보안 이벤트를 집계 합니다.

- 기존 장치 id 또는 전용 모듈 id를 사용 하 여 인증 합니다. 자세히 알아보려면 [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 을 참조 하세요.

- **Azureiotsecurity** 모듈 쌍을 사용 하 여 원격으로 구성 합니다. 자세히 알아보려면 [IoT 에이전트에 대 한 Defender 구성](how-to-agent-configuration.md)을 참조 하세요.

IoT 용 Defender 보안 에이전트는 오픈 소스 프로젝트로 개발 되었으며 GitHub에서 사용할 수 있습니다.

- [IoT C 기반 에이전트의 Defender](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [IoT 용 Defender c # 기반 에이전트](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

IoT 용 Defender는 32 비트 및 64 비트 Windows 용 여러 설치 관리자 에이전트를 제공 하 고, 32 비트 및 64 비트 Linux의 경우 동일 합니다. 다음 표에 따라 각 장치에 대 한 올바른 에이전트 설치 관리자가 있는지 확인 합니다.

| Architecture | Linux | Windows |    세부 정보|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32비트  | C  | C#  ||
| 64비트  | C # 또는 C           | C#      | 더 제한 된 장치나 최소 장치 리소스를 사용 하는 장치에는 C 에이전트를 사용 하는 것이 좋습니다.|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 보안 에이전트 아키텍처와 사용 가능한 설치 관리자에 대 한 Defender에 대해 알아보았습니다.

IoT 배포용 Defender를 계속 시작 하려면 다음 문서를 사용 합니다.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- IoT [서비스 필수 구성 요소](service-prerequisites.md) 를 검토 합니다.
- [IoT Hub에서 IoT 서비스용 Defender를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법에 대해 알아봅니다.
- [IoT 용 DEFENDER FAQ](resources-frequently-asked-questions.md) 에서 서비스에 대 한 자세한 정보
