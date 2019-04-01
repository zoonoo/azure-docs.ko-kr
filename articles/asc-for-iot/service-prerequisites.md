---
title: IoT 필수 구성 요소 미리 보기에 대 한 azure Security Center | Microsoft Docs
description: IoT 서비스 필수 구성 요소에 대 한 Azure Security Center를 사용 하 여 시작 하는 데 필요한 모든 세부 정보입니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 5293fe6a8d4a8a44a89890af1c259e2b4b2b7827
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755084"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>IoT 필수 구성 요소에 대 한 azure Security Center

> [!IMPORTANT]
> IoT 용 azure Security Center는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전을 서비스 수준 계약 없이 제공 됩니다 및 프로덕션 워크 로드에 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 다양 한 빌딩 블록의는 Security Center ASC (Azure) IoT 서비스를 시작 해야 할 작업 및 기본 개념에 대 한 서비스를 안전 하 게 이해할 수의 설명이 표시 됩니다. 

## <a name="minimum-requirements"></a>최소 요구 사항

- IoT Hub 표준 계층
    - RBAC 역할 **소유자** 권한 수준 
- [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (권장)
    - Azure Security Center의 사용이 권장 사항을 고 없으면를 요구 하지 IoT Hub 내에서 다른 Azure 리소스를 볼 수 없습니다. 
 
## <a name="working-with-asc-for-iot-service"></a>ASC IoT 서비스에 대 한 작업

ASC IoT insights 및 보고에 대 한 Azure IoT Hub 및 Azure Security Center를 사용 하 여 사용할 수 있습니다. Azure IoT Hub를 사용 하 여 계정에 IoT 용 ASC를 사용 하도록 설정 하려면 **소유자** 수준 권한이 필요 합니다. IoT Hub에 IoT 용 ASC를 활성화 한 후 ASC IoT insights 용으로 표시 됩니다는 **Security** 및 Azure IoT Hub의 기능 **IoT** Azure Security Center에서. 

## <a name="supported-service-regions"></a>지원 되는 서비스 영역 

ASC IoT에 대 한 Azure 지역에 IoT Hub에 대 한 현재 지원 됩니다.
  - 미국 중부
  - 북유럽
  - 동남아시아

## <a name="wheres-my-iot-hub"></a>내 IoT 허브는 어디 입니까?

시작 하기 전에 서비스 가용성을 확인 하려면 IoT Hub 위치를 확인 합니다. 

1. IoT Hub를 엽니다. 
2. **개요**를 클릭합니다. 
3. 나열 된 위치 중 하 나와 일치를 확인 합니다 [지원 되는 서비스 지역](#supported-service-regions)합니다. 


## <a name="supported-platforms-for-agents"></a>에이전트에 대 한 지원 되는 플랫폼 

ASC IoT 에이전트에 대 한 목록에 장치 및 플랫폼을 지원합니다. 참조 된 [지원 되는 플랫폼 목록](how-to-deploy-agent.md) 기존 또는 계획 된 장치 라이브러리를 확인 합니다.  

## <a name="next-steps"></a>다음 단계
- [개요](overview.md)
- [서비스를 사용 하도록 설정](quickstart-onboard-iot-hub.md)
- [ASC for IoT FAQ](resources-frequently-asked-questions.md)
- [ASC IoT 경고에 대 한 이해](concept-security-alerts.md)
