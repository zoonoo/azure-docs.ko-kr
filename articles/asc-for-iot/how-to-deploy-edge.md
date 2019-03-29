---
title: IoT Edge 모듈에 대 한를 ASC를 배포 합니다. | Microsoft Docs
description: 방법은 IoT Edge의 IoT 보안 에이전트용 ASC를 배포 합니다.
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
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580495"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>IoT Edge 장치의 보안 모듈 배포

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 용 ASC **azureiotsecurity** 모듈은 IoT Edge 장치에 대 한 완벽 한 보안 솔루션을 제공 합니다.
보안 모듈 수집, 집계 및 실행 가능한 보안 권장 사항 및 경고에 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 분석 합니다.
자세한 내용은 참조 하세요 [IoT Edge에 대 한 보안 모듈](security-edge-architecture.md)합니다.

이 가이드에서는 IoT Edge 장치의 보안 모듈을 배포 하는 방법을 알아봅니다.

## <a name="deploy-security-module"></a>보안 모듈 배포

IoT Edge에 대 한 IoT 보안 모듈에 대 한를 ASC를 배포 하려면 다음 단계를 사용 합니다.

### <a name="prerequisites"></a>필수 조건

1. 장치 인지 확인 [IoT Edge 장치로 등록](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal)합니다.

1. IoT Edge 모듈에 대 한 ASC 필요 합니다 [AuditD framework](https://linux.die.net/man/8/auditd) Edge 장치에 설치 합니다.

   Edge 장치에서 다음 명령을 실행 하 여 프레임 워크를 설치 합니다.
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Azure portal을 사용 하 여 배포

1. 오픈 **Marketplace** Azure portal에서 합니다.

1. 검색할 **azure iot 보안** 를 클릭 **Azure IoT 보안**합니다.

   ![](media/howto/edge_onboarding_7.png)

1. **만들기**를 클릭합니다.

1. 선택 하 **구독**, **IoT Hub** 하 고 **IoT Edge 장치 이름**, 클릭 **만들기**합니다.

1. 클릭 **다음** 를 두 번 **검토 배포**합니다.

1. 했는지 **edgeHub.settings.createOptions** 다음과 같이 구성 됩니다.

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. 클릭 **제출** 배포를 완료 합니다.


## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용은 모듈 구성에 대 한 방법 가이드를 계속 합니다. 
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)