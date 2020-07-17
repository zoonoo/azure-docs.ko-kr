---
title: 'Azure Peering Service: 연결 원격 분석 측정 방법 '
description: 이 자습서에서는 연결 원격 분석을 측정하는 방법을 알아봅니다.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "84870555"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>자습서: Peering Service 연결 원격 분석 측정

 이 자습서에서는 Peering Service 연결에 대한 원격 분석을 측정하는 방법을 알아봅니다.
 
 연결 원격 분석은 고객의 위치와 Microsoft 네트워크 간의 연결에 대해 수집된 인사이트를 제공합니다. 이 문서에서는 특정 Azure Peering Service 연결에 대한 대기 시간 보고서를 보는 방법에 대해 알아봅니다. 

Peering Service 연결 원격 분석을 측정하려면 Azure Portal에서 Peering Service 연결을 등록해야 합니다. 연결을 등록하는 방법에 대한 자세한 내용은 [Peering Service 연결 등록 - Azure Portal](azure-portal.md)을 참조하세요.


## <a name="view-a-latency-report"></a>대기 시간 보고서 보기

특정 Peering Service 연결에 대한 대기 시간 보고서를 보려면 다음 단계를 수행합니다.

1. 왼쪽 창에서 **모든 리소스**를 선택하고, Peering Service 연결을 선택합니다. 그런 다음, **접두사**에서 **열기**를 선택합니다. 

   ![Peering Service 연결 선택](./media/peering-service-measure/peering-service-measure-menu.png)

2. 해당 Peering Service 연결과 관련된 모든 접두사의 대기 시간 보고서 페이지가 나타납니다. 

      ![대기 시간 보고서 페이지](./media/peering-service-measure/peering-service-latency-report.png)

3. 기본적으로 이 페이지에 표시되는 매시간에 대한 보고서가 업데이트됩니다. 다른 타임라인에 대한 보고서를 보려면 **마지막 데이터 표시**에서 적절한 옵션을 선택합니다. 

4. 특정 접두사에 대한 이벤트를 보려면 접두사 이름을 선택하고 왼쪽 창에서 **접두사 이벤트**를 선택합니다. 캡처한 이벤트가 표시됩니다.


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