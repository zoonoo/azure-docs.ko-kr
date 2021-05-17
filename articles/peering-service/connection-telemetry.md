---
title: 'Azure Peering Service: 연결 원격 분석에 액세스 방법 '
description: 이 자습서에서는 연결 원격 분석에 액세스하는 방법을 알아봅니다.
services: peering-service
author: gthareja
ms.service: peering-service
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: gatharej
ms.openlocfilehash: 2b019596c87ef3beca1ff26a9366250d188bdfbc
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202680"
---
# <a name="tutorial-accessing-peering-service-connection-telemetry"></a>자습서: Peering Service 연결 원격 분석 액세스

 이 자습서에서는 Peering Service 연결에 대한 원격 분석에 액세스하는 방법을 알아봅니다.
 
 연결 원격 분석은 고객의 위치와 Microsoft 네트워크 간의 연결에 대해 수집된 인사이트를 제공합니다. 이 문서에서는 특정 Azure Peering Service 연결에 대한 대기 시간 보고서 및 접두사 상태를 보는 방법에 대해 알아봅니다. 

Peering Service 연결 원격 분석에 액세스하려면 Azure Portal에서 Peering Service 연결을 만들어야 합니다. 연결을 만드는 방법에 대한 자세한 내용은 [Peering Service 연결 만들기 - Azure Portal](azure-portal.md)을 참조하세요.


## <a name="view-a-latency-report"></a>대기 시간 보고서 보기

특정 Peering Service 연결에 대한 대기 시간 보고서를 보려면 다음 단계를 수행합니다.

1. 왼쪽 창에서 **모든 리소스** 를 선택하고, Peering Service 연결을 선택합니다. 그런 다음, **접두사** 에서 **열기** 를 선택합니다. 

   ![Peering Service 연결 선택](./media/peering-service-measure/peering-service-measure-menu.png)

2. 해당 Peering Service 연결과 관련된 모든 접두사의 대기 시간 보고서 페이지가 나타납니다. **피어링 서비스 연결은 /24 이상의 접두사에 대해서만 지연 시간 데이터를 지원합니다.**

      ![대기 시간 보고서 페이지](./media/peering-service-measure/peering-service-latency-report.png)

3. 기본적으로 이 페이지에 표시되는 매시간에 대한 보고서가 업데이트됩니다. 다른 타임라인에 대한 보고서를 보려면 **마지막 데이터 표시** 에서 적절한 옵션을 선택합니다. 

## <a name="view-prefix-state-report"></a>접두사 상태 보고서 보기

1. 특정 접두사에 대한 이벤트를 보려면 접두사 이름을 선택하고 왼쪽 창에서 **접두사 이벤트** 를 선택합니다. 캡처한 이벤트가 표시됩니다.


   ![접두사 이벤트](./media/peering-service-measure/peering-service-prefix-event.png)

 **접두사 이벤트** 목록에서 캡처한 몇 가지 가능한 이벤트가 여기에 표시됩니다.

| **접두사 이벤트** | **이벤트 유형**|**지각 장애**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |정보|접두사 알림이 수신됨|
| PrefixWithdrawalEvent|Warning| 접두사 출금이 수신됨 |
| PrefixBackupRouteAnnouncementEvent |정보|접두사 백업 경로 알림이 수신됨 |
| PrefixBackupRouteWithdrawalEvent|Warning|접두사 백업 경로 출금이 수신됨 |
| PrefixActivePath |정보| 현재 접두사 활성 경로   |
| PrefixBackupPath | 정보|현재 접두사 백업 경로   |
| PrefixOriginAsChangeEvent|위험| 다른 원본 익명 시스템 번호로 받은 정확한 접두사(활성 경로용)| 
| PrefixBackupRouteOriginAsChangeEvent  | Error|다른 원본 익명 시스템 번호로 받은 접두사(백업 경로용)  |

## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
