---
title: ASC 미리 보기 IoT 보안 모듈 쌍 이해 | Microsoft Docs
description: 보안 모듈 쌍 및 IoT에 대 한 ASC에서 사용 하는 방법의 개념에 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541844"
---
# <a name="security-module"></a>보안 모듈

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서는 ASC IoT에 대 한 장치 쌍 및 모듈을 사용 하는 방법을 설명 합니다. 

## <a name="device-twins"></a>디바이스 쌍

Azure에서 구축 된 IoT 솔루션에 대 한 장치 쌍 장치 관리 및 프로세스 자동화에 중요 한 역할을 재생 합니다.  

IoT 용 ASC 제공 기존 IoT 장치 관리 플랫폼에 완벽 한 통합에서 장치 보안 상태를 관리할 수 있도록 확인 뿐 아니라 기존 장치 제어 기능을 사용 합니다. IoT Hub를 사용 하 여 통합을 구현 하는 메커니즘을 쌍입니다.  

개념에 자세히 알아보려면 [장치](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) Azure IoT Hub에서 쌍입니다. 

## <a name="security-module-twins"></a>보안 모듈 쌍

ASC IoT에 대 한 서비스에서 각 장치에 대 한 보안 모듈 쌍을 유지 관리합니다. 보안 모듈 쌍 솔루션의 각 특정 장치에 대 한 장치 보안에 관련 된 모든 정보를 보유 합니다. 장치 보안 속성 보다 안전한 통신 및 업데이트 하 고 더 적은 리소스가 필요한 유지 관리를 사용 하도록 설정 하는 것에 대 한 전용된 보안 모듈 쌍에 유지 됩니다.  

참조 [만들기 보안 모듈 쌍](quickstart-create-security-twin.md) 하 고 [보안 에이전트 구성](concept-agent-configuration.md) 만드는 방법에 알아보려면 사용자 지정 및 쌍을 구성 합니다. 참조 [이해 모듈 트윈스](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) IoT Hub의 모듈 쌍의 개념에 자세히 알아보려면 합니다. 
 

## <a name="see-also"></a>참고 항목
- [ASC IoT 미리 보기](overview.md)
- [보안 에이전트 배포](select-deploy-agent.md)
- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)