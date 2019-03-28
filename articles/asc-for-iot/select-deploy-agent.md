---
title: 선택 하 고 IoT 에이전트 미리 보기에는 ASC를 배포 합니다. | Microsoft Docs
description: 에 대 한 방법을 선택 하 고 IoT 장치에서 IoT 보안 에이전트에 대 한 ASC를 배포에 대해 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541964"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>선택 하 고 IoT 장치의 보안 에이전트 배포

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


IoT 용 ASC 보안 에이전트 모니터링 하 고 IoT 장치에서 데이터 수집에 대 한 참조 아키텍처를 제공 합니다.

보안 에이전트 오픈 소스 프로젝트로 개발 됩니다 되며 두 버전에서 사용할 수 있습니다. <br> [C](https://aka.ms/iot-security-github-c), 및 [ C# ](https://aka.ms/iot-security-github-cs)합니다.

## <a name="supported-platforms-for-asc-for-iot-agents"></a>ASC IoT 에이전트에 대 한 지원 되는 플랫폼

다음은 현재 지원 되는 모든 플랫폼을 포함합니다.  

|ASC IoT 에이전트에 대 한 |운영 체제 |아키텍처 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core 빌드 17763 |x64|


## <a name="which-flavor-should-i-use"></a>어떤 버전을 사용 해야 합니까?

IoT 장치에 대해 다음과 같은 질문을 고려 합니다.

- 사용 하 고 _Windows Server_ 하거나 _Windows IoT Core_? 

    사용 하 여 [ASC를 사용 하 여 Windows에 대 한 IoT 설치에 대 한는 C#-보안 에이전트 기반](tutorial-deploy-windows-cs.md)합니다.

- 사용 하 고 Linux 배포를 x86을 사용 하 여 아키텍처? 

    사용 하 여 [ASC C 기반 보안 에이전트와 Linux에 대 한 IoT 설치용](tutorial-deploy-linux-c.md)합니다.
- 사용 하 고 Linux 배포를 x64 아키텍처?

    두 버전을 사용할 수 있습니다. <br>
    [ASC C 기반 보안 에이전트와 Linux에 대 한 IoT 설치용](tutorial-deploy-linux-c.md) 및/또는 [사용 하 여 Linux에 대 한 IoT 설치용 ASC를 C#-보안 에이전트 기반](tutorial-deploy-linux-cs.md)합니다.

모두 동일한 기능 집합이 및 유사한 구성 옵션을 지원 합니다. C 기반 보안 에이전트에는 메모리 사용 공간을 아래에 있습니다.


## <a name="next-steps"></a>다음 단계
- [개요](overview.md)
- [보안 에이전트](security-agent-architecture.md)
- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)
- [ASC for IoT FAQ](resources-frequently-asked-questions.md)