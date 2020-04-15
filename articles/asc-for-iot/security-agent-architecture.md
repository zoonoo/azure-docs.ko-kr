---
title: 보안 에이전트 아키텍처
description: IoT 서비스에 대한 Azure 보안 센터에서 사용되는 에이전트에 대한 보안 에이전트 아키텍처를 이해합니다.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310679"
---
# <a name="security-agent-reference-architecture"></a>보안 에이전트 참조 아키텍처

IoT용 Azure 보안 센터는 IoT Hub를 통해 보안 데이터를 기록, 처리, 집계 및 보내는 보안 에이전트에 대한 참조 아키텍처를 제공합니다.

보안 에이전트는 제한된 IoT 환경에서 작동하도록 설계되었으며 사용하는 리소스와 비교할 때 제공하는 값 측면에서 매우 사용자 정의할 수 있습니다.

보안 에이전트는 다음 기능을 지원합니다.

- 기본 운영 체제 (리눅스, 윈도우)에서 원시 보안 이벤트를 수집합니다. 사용 가능한 보안 데이터 수집기에 대한 자세한 내용은 [IoT 에이전트 구성용 Azure 보안 센터를](how-to-agent-configuration.md)참조하십시오.

- 원시 보안 이벤트를 IoT Hub를 통해 전송된 메시지로 집계합니다.

- 기존 장치 ID 또는 전용 모듈 ID로 인증합니다. 자세한 내용은 [보안 에이전트 인증 방법을](concept-security-agent-authentication-methods.md) 참조하십시오.

- **azureiot보안** 모듈 쌍둥이를 사용하여 원격으로 구성합니다. 자세한 내용은 [IoT 에이전트용 Azure 보안 센터 구성을](how-to-agent-configuration.md)참조하십시오.

IoT 보안 에이전트용 Azure 보안 센터는 오픈 소스 프로젝트로 개발되며 GitHub에서 사용할 수 있습니다.

- [IoT C 기반 에이전트용 Azure 보안 센터](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [IoT C#기반 에이전트용 Azure 보안 센터](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

IoT용 Azure 보안 센터는 32비트 및 64비트 Windows용 다양한 설치 관리자 에이전트를 제공하며, 32비트 및 64비트 Linux에서도 동일합니다. 다음 표에 따라 각 장치에 대해 올바른 에이전트 설치 관리자가 있는지 확인합니다.

| Architecture | Linux | Windows |    세부 정보|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32비트  | C  | C#  ||
| 64비트  | C# 또는 C           | C#      | 제한 또는 최소 장치 리소스가 있는 장치에는 C 에이전트를 사용하는 것이 좋습니다.|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 보안 에이전트 아키텍처용 Azure 보안 센터 및 사용 가능한 설치 관리자에 대해 배웠습니다.

IoT 배포를 위한 Azure 보안 센터를 계속 시작하려면 다음 문서를 사용하십시오.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- IoT [서비스 전제 조건에](service-prerequisites.md) 대한 Azure 보안 센터 검토
- [IoT 허브에서 IoT 서비스를 위한 Azure 보안 센터를 사용하도록 설정하는](quickstart-onboard-iot-hub.md) 방법 알아보기
- [IoT FAQ용 Azure 보안 센터에서](resources-frequently-asked-questions.md) 서비스에 대해 자세히 알아보기
