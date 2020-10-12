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
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659611"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>IoT 용 Azure Defender (이전의 Azure Security Center)에서 Azure 센티널로 데이터 연결 


> [!IMPORTANT]
> IoT 용 Azure Defender 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 용 Azure Defender 커넥터를 사용 하 여 모든 IoT 용 Azure Defender 이벤트를 Azure 센티널로 스트리밍합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널이 배포 된 작업 영역에 대 한 **읽기** 및 **쓰기** 권한
- **IoT 용 Azure Defender는** 관련 IoT Hub에서 **사용 하도록 설정** 해야 합니다.
- 연결 하려는 **Azure IoT Hub** 에 대 한 **읽기** 및 **쓰기** 권한
- **Azure IoT Hub 리소스 그룹** 에 대 한 **읽기** 및 **쓰기** 권한

## <a name="connect-to-azure-defender-for-iot"></a>IoT 용 Azure Defender에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음, 갤러리에서 **Iot 용 Azure Defender** 를 선택 합니다 (iot에 대 한 Azure Security Center 계속 호출 될 수 있음).
1. 오른쪽 아래 창에서 **커넥터 페이지 열기**를 클릭 합니다. 
1. 연결을 클릭 하 고, 경고 및 장치 경고가 Azure 센티널로 스트리밍할 각 IoT Hub 구독 옆에 있는 **연결**을 클릭 합니다. 
    - 해당 허브에서 IoT 용 Azure Defender를 사용 하도록 설정 하지 않으면 경고 **사용** 메시지가 표시 됩니다. **사용** 링크를 클릭 하 여 서비스를 시작 합니다. 
1. Azure Defender에서 IoT에 대 한 경고를 통해 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 결정할 수 있습니다. **인시던트 만들기**에서 **사용** 을 선택 하 여 기본 분석 규칙을 사용 하도록 설정 하 여 연결 된 보안 서비스에 생성 된 경고에서 인시던트를 자동으로 만듭니다. 이 규칙은 **분석**  >  **활성** 규칙에서 변경 하거나 편집할 수 있습니다.

> [!NOTE]
> 연결을 변경한 후에는 허브 목록을 새로 고치는 데 약간의 시간이 걸릴 수 있습니다. 

## <a name="log-analytics-alert-display"></a>Log Analytics 경고 표시

Log Analytics에서 관련 스키마를 사용 하 여 IoT 경고에 대 한 Azure Defender를 표시 하려면 다음을 수행 합니다.

1. **Logs**  >  **securityinsights**  >  **securityinsights**를 열거나 **securityinsights**를 검색 합니다. 
2. 다음 kql 필터를 사용 하 여 IoT 생성 경고만 Azure Defender를 표시 하도록 필터링 합니다.

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>서비스 정보

IoT Hub 연결한 후에는 약 15 분 후에 허브 데이터를 Azure 센티널에서 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 azure에 IoT 데이터를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
