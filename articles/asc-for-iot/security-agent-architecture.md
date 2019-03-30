---
title: ASC IoT 보안 에이전트 아키텍처 미리 보기에 대 한 이해 | Microsoft Docs
description: IoT 서비스에 대 한 ASC에서 사용 하는 에이전트에 대 한 보안 에이전트 아키텍처를 이해 합니다.
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
ms.openlocfilehash: d81054e95b9f712262fe5fa46453749b54a7ac24
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651400"
---
# <a name="security-agent-reference-architecture"></a>보안 에이전트 참조 아키텍처

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전을 서비스 수준 계약 없이 제공 됩니다 및 프로덕션 워크 로드에 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


IoT 용 ASC 보안 에이전트 로그, 처리, 집계 및 IoT hub 통해 보안 데이터 전송에 대 한 참조 아키텍처를 제공 합니다.

보안 에이전트는 제한 된 IoT 환경에서 작동 하도록 설계 하 고 폭넓게 사용 하는 리소스와 비교할 때 제공 하는 값을 기준으로 합니다.

보안 에이전트는 다음 기능을 지원 합니다.

- 기본 운영 체제 (Linux, Windows)에서 원시 보안 이벤트를 수집 합니다. 사용 가능한 보안 데이터 수집기에 대 한 자세한 내용은 참조 하세요 [ASC IoT 에이전트 구성을](how-to-agent-configuration.md)합니다.

- IoT hub를 통해 전송 된 메시지에는 원시 보안 이벤트를 집계 합니다.

- 기존 장치 id 또는 전용된 모듈 id를 사용 하 여 인증 합니다. 참조 [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 에 대해 자세히 알아보세요.

- 사용 하 여 원격으로 구성 합니다 **azureiotsecurity** 모듈 쌍입니다. 자세한 내용은 참조 하세요 [IoT 에이전트용를 ASC 구성](how-to-agent-configuration.md)합니다.

ASC IoT 보안 에이전트에 대 한 오픈 소스 프로젝트로 개발 됩니다 되며 GitHub에서 사용할 수 있습니다. 

- [ASC IoT C 기반 에이전트](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC IoT에 대 한 C#-에이전트 기반](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

IoT 용 ASC는 32 비트 및 64 비트 Windows 실행 하는 것에 대 한 다양 한 설치 관리자 에이전트 및 32 비트 및 64 비트 Linux에 대 한 동일한를 제공합니다. 다음 표에 따라 장치의 각각에 대 한 올바른 에이전트 설치 관리자를가 있는지 확인 합니다.

| 32 또는 64 비트 | Linux | Windows |    세부 정보|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 비트  | C  | C#  ||
| 64 비트  | C#또는 C           | C#      | 최소한의 리소스를 사용 하 여 장치에 대 한 에이전트 C 사용 하 여|

## <a name="next-steps"></a>다음 단계

이 문서에 대해 알아보았습니다 ASC IoT 보안 에이전트 아키텍처 및 설치 관리자를 사용할 수 있습니다.

ASC를 사용 하 여 IoT 배포에 대 한 시작 작업을 계속 하려면 다음 문서를 사용 합니다.

- 이해 [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)
- 선택 하 고 배포를 [보안 에이전트](how-to-deploy-agent.md)
- IoT 용 ASC 검토 [필수 구성 요소 서비스](service-prerequisites.md)
- 자세한 방법 [IoT Hub에 IoT 서비스에 대 한 ASC를 사용 하도록 설정](quickstart-onboard-iot-hub.md)
- 서비스에 대 한 자세한 정보는 [ASC IoT faq](resources-frequently-asked-questions.md)
