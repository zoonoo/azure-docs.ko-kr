---
title: Azure 센티널에 IoT 용 Azure Defender 연결 | Microsoft Docs
description: IoT 데이터를 위해 Azure Defender (이전에 Azure Security Center)를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631444"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>IoT 용 Azure Defender (이전의 Azure Security Center)에서 Azure 센티널로 데이터 연결 


> [!IMPORTANT]
> IoT 용 Defender 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 용 Defender 커넥터를 사용 하 여 모든 IoT 이벤트 용 Defender를 Azure 센티널로 스트리밍합니다. 

이러한 통합을 통해 조직은 종종 교차와 경계를 교차 하는 다단계 공격을 신속 하 게 검색할 수 있습니다. 또한 Azure 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 기능을 사용 하 여 IoT 용 Defender를 통합 하면 기본 제공 되는 OT 기반 플레이 북을 사용 하 여 자동화 된 응답과 방지를 사용할 수 있습니다. 
## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널이 배포 된 작업 영역에 대 한 **읽기** 및 **쓰기** 권한
- **IoT 용 Defender는** 관련 IoT Hub에서 **사용 하도록 설정** 해야 합니다.
- 연결 하려는 **구독** 에 대 한 **참가자** 권한이 있어야 합니다.

## <a name="connect-to-defender-for-iot"></a>IoT 용 Defender에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음, 갤러리에서 **iot 용 Defender** (iot 용 Azure Security Center)를 선택 합니다.

1. 오른쪽 창의 맨 아래에서 **커넥터 페이지 열기** 를 클릭 합니다. 

1. 연결을 클릭 하 고, 경고 및 장치 경고가 Azure 센티널로 스트리밍할 각 IoT Hub 구독 옆에 있는 **연결** 을 클릭 합니다. 
    - 구독 내에서 하나 이상의 IoT Hub에서 IoT 용 Defender를 사용 하도록 설정 하지 않은 경우 오류 메시지가 표시 됩니다. IoT Hub 내에서 IoT에 대해 Defender를 사용 하도록 설정 하 여 오류를 제거 합니다.

1. IoT에 대해 Defender에서 경고를 통해 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 결정할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택 하 여 기본 분석 규칙을 사용 하도록 설정 하 여 생성 된 경고에서 인시던트를 자동으로 만듭니다. 이 규칙은 **분석**  >  **활성 규칙** 에서 변경 하거나 편집할 수 있습니다.

> [!NOTE]
> 연결을 변경한 후 **구독** 목록을 새로 고치는 데 10 초 이상 걸릴 수 있습니다. 

## <a name="log-analytics-alert-view"></a>Log Analytics 경고 보기

Log Analytics에서 관련 스키마를 사용 하 여 IoT 경고에 대 한 Defender를 표시 하려면 다음을 수행 합니다.

1. **Logs**  >  **securityinsights**  >  **securityinsights** 를 열거나 **securityinsights** 를 검색 합니다. 

2. 다음 kql 필터를 사용 하 여 IoT 생성 된 경고에 대 한 Defender만 표시 하도록 필터링 합니다.

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>서비스 정보

**구독** 을 연결한 후에는 약 15 분 후에 허브 데이터를 Azure 센티널에서 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 용 Defender를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
