---
title: 연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션
titleSuffix: Azure Network Watcher
description: 연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441819"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션

몇 번의 클릭 만으로 가동 중지 시간 없이 기존 연결 모니터를 새롭게 향상 된 연결 모니터 (미리 보기)로 마이그레이션할 수 있습니다. 이점에 대 한 자세한 내용은 [연결 모니터 (미리 보기)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)를 참조 하세요.

## <a name="key-points-to-note"></a>핵심 사항

마이그레이션은 다음과 같은 결과를 생성 하는 데 도움이 됩니다.

* 에이전트 및 방화벽 설정이 그대로 작동 합니다. 변경할 필요가 없습니다. 
* 기존 연결 모니터는 테스트 그룹 > 테스트 형식 > 연결 모니터 (미리 보기)에 매핑됩니다. **편집**을 선택 하 여 새 연결 모니터의 속성을 보고 수정 하 고, 연결 모니터를 변경 하 여 템플릿을 다운로드 하 고, Azure Resource Manager를 통해 제출할 수 있습니다. 
* Network Watcher 확장을 포함 하는 Azure virtual machines는 작업 영역과 메트릭에 데이터를 모두 보냅니다. 연결 모니터는 이전 메트릭 (ProbesFailedPercent 및 AverageRoundtripMs) 대신 새 메트릭 (ChecksFailedPercent [Preview] 및 RoundTripTimeMs [Preview])을 통해 데이터를 사용할 수 있도록 합니다. 
* 데이터 모니터링:
   * **경고**: 새 메트릭에 자동으로 마이그레이션됩니다.
   * **대시보드 및 통합**: 메트릭 집합을 수동으로 편집 해야 합니다. 
    
## <a name="prerequisites"></a>필수 구성 요소

사용자 지정 작업 영역을 사용 하는 경우 구독 및 Log Analytics 작업 영역의 지역에서 Network Watcher를 사용 하도록 설정 해야 합니다. 

## <a name="migrate-the-connection-monitors"></a>연결 모니터 마이그레이션

1. 이전 연결 모니터를 최신으로 마이그레이션하려면 **연결 모니터**를 선택한 다음 **연결 모니터 마이그레이션**을 선택 합니다.

    ![연결 모니터 (미리 보기)에 대 한 연결 모니터의 마이그레이션을 보여 주는 스크린샷](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 마이그레이션할 구독 및 연결 모니터를 선택 하 고 **선택한 마이그레이션**을 선택 합니다. 

몇 번의 클릭 만으로 기존 연결 모니터를 연결 모니터 (미리 보기)로 마이그레이션 했습니다. 

이제 연결 모니터 (미리 보기) 속성을 사용자 지정 하 고, 기본 작업 영역을 변경 하 고, 템플릿을 다운로드 하 고, 마이그레이션 상태를 확인할 수 있습니다. 

마이그레이션이 시작 된 후에는 다음 변경 내용이 적용 됩니다. 
* Azure Resource Manager 리소스가 최신 연결 모니터로 변경 됩니다.
    * 연결 모니터의 이름, 지역 및 구독은 변경 되지 않은 상태로 유지 됩니다. 리소스 ID는 영향을 받지 않습니다.
    * 연결 모니터가 사용자 지정 되지 않은 경우 기본 Log Analytics 작업 영역은 구독 및 연결 모니터 지역에서 만들어집니다. 이 작업 영역은 모니터링 데이터가 저장 되는 위치입니다. 테스트 결과 데이터도 메트릭에 저장 됩니다.
    * 각 테스트는 *Defaulttestgroup*이라는 테스트 그룹으로 마이그레이션됩니다.
    * 원본 및 대상 끝점은 새 테스트 그룹에서 만들어지고 사용 됩니다. 기본 이름은 *Defaultsourceendpoint* 및 *defaultsourceendpoint*입니다.
    * 대상 포트 및 검색 간격이 *Defaulttestconfiguration*라는 테스트 구성으로 이동 됩니다. 프로토콜은 포트 값을 기반으로 설정 됩니다. 성공 임계값 및 기타 선택적 속성은 비워 둡니다.
* 메트릭 경고는 연결 모니터 (미리 보기) 메트릭 경고로 마이그레이션됩니다. 메트릭은 다르지만 변경 됩니다. 자세한 내용은 [연결 모니터를 사용 하 여 네트워크 연결 모니터링 (미리 보기)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)을 참조 하세요.
* 마이그레이션된 연결 모니터는 더 이상 이전 연결 모니터 솔루션으로 표시 되지 않습니다. 이제 연결 모니터 (미리 보기) 에서만 사용할 수 있습니다.
* Power BI 및 Grafana의 대시보드와 같은 외부 통합 및 SIEM (보안 정보 및 이벤트 관리) 시스템과의 통합을 수동으로 마이그레이션해야 합니다. 이 단계는 설치를 마이그레이션하기 위해 수행 해야 하는 유일한 수동 단계입니다.

## <a name="next-steps"></a>다음 단계

연결 모니터 (미리 보기)에 대 한 자세한 내용은 다음을 참조 하세요.
* [네트워크 성능 모니터에서 연결 모니터로 마이그레이션 (미리 보기)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Azure Portal를 사용 하 여 연결 모니터 만들기 (미리 보기)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
