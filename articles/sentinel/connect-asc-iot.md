---
title: IoT용 Azure 보안 센터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: IoT 데이터를 위한 Azure 보안 센터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588640"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>IoT용 Azure 보안 센터에서 Azure 센티넬에 데이터를 연결 


> [!IMPORTANT]
> IoT 데이터 커넥터용 Azure 보안 센터는 현재 공개 미리 보기 상태입니다. 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 커넥터용 Azure 보안 센터를 사용하여 IoT에 대한 모든 Azure 보안 센터를 Azure Sentinel으로 스트리밍합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel이 배포된 작업 영역에 대한 사용 권한 **읽기** 및 **쓰기**
- **IoT용 Azure 보안 센터는** 관련 IoT Hub에서 **활성화되어야** 합니다.
- 연결하려는 **Azure IoT Hub에서** 사용 권한 **읽기** 및 **쓰기**
- **Azure IoT Hub 리소스 그룹에** 대한 사용 권한 **읽기** 및 **쓰기**

> [!NOTE]
> Azure Sentinel에 IoT 리소스 경고를 스트리밍하려면 구독에서 Azure 보안 센터 **표준** 계층 라이선스를 사용하도록 설정해야 하지만 Azure Sentinel에서 Azure Security Center 경고를 보려면 구독에서 Azure 보안 센터 **프리** 티어 라이선스만 사용하도록 설정하면 됩니다. 

## <a name="connect-to-azure-security-center-for-iot"></a>IoT용 Azure 보안 센터에 연결

1. Azure Sentinel에서 데이터 커넥터를 선택한 다음 **IoT** 타일에 대한 Azure 보안 센터를 **클릭합니다.**
1. 오른쪽 하단 창에서 **연결선 열기 페이지를**클릭합니다. 
1. Azure Sentinel로 스트리밍하려는 경고 및 장치 경고를 각 IoT Hub 구독 옆에 있는 **연결**을 클릭합니다. 
    - 해당 Hub에서 IoT용 Azure 보안 센터를 사용하도록 설정하지 않으면 **활성화** 경고 메시지가 표시됩니다. 서비스를 시작하려면 **활성화** 링크를 클릭합니다. 
1. Azure Sentinel에서 인시던트를 자동으로 생성하도록 IoT용 Azure 보안 센터의 경고를 결정할 수 있습니다. **인시던트 만들기에서**기본 분석 규칙에서 연결된 보안 서비스에서 생성된 경고에서 인시던트를 자동으로 생성하도록 **활성화를** 선택합니다. 이 규칙은 **애널리틱스** > **활성** 규칙에 따라 변경하거나 수정할 수 있습니다.

> [!NOTE]
> 연결을 변경한 후 허브 목록을 새로 고치는 데 다소 시간이 걸릴 수 있습니다. 

## <a name="log-analytics-alert-display"></a>로그 분석 경고 표시

로그 분석에서 관련 스키마를 사용하여 IoT 경고를 위한 Azure 보안 센터를 표시하려면 다음을 수행합니다.

1. 열기 **로그** > **보안통찰력** > **보안경고**또는 **보안경고를 검색합니다.** 
2. 다음 kql 필터를 사용하여 IoT에 대해 생성된 Azure 보안 센터만 보려면 필터링합니다.

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>서비스 노트

IoT Hub를 연결한 후 약 15분 후에 Azure Sentinel에서 허브 데이터를 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 데이터에 대한 Azure 보안 센터를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.
