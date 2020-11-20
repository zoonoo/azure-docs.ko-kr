---
title: 네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션
titleSuffix: Azure Network Watcher
description: 네트워크 성능 모니터에서 연결 모니터로 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 38ea682b4f3894f373d16908b942b4fa578741c4
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985810"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션

한 번의 가동 중지 시간 없이 한 번의 클릭으로 네트워크 성능 모니터 (NPM)에서 새롭게 향상 된 연결 모니터로 테스트를 마이그레이션할 수 있습니다. 이점에 대 한 자세한 내용은 [연결 모니터](./connection-monitor-overview.md)를 참조 하세요.

> [!IMPORTANT]
> 연결 모니터는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

>[!NOTE]
> 서비스 연결 모니터의 테스트만 연결 모니터로 마이그레이션할 수 있습니다.
>

## <a name="key-points-to-note"></a>핵심 사항

마이그레이션은 다음과 같은 결과를 생성 하는 데 도움이 됩니다.

* 온-프레미스 에이전트 및 방화벽 설정이 그대로 작동 합니다. 변경할 필요가 없습니다. Azure 가상 컴퓨터에 설치 된 Log Analytics 에이전트를 Network Watcher 확장으로 바꾸어야 합니다.
* 기존 테스트는 테스트 그룹 > 테스트 형식 > 연결 모니터에 매핑됩니다. **편집** 을 선택 하 여 새 연결 모니터의 속성을 보고 수정 하 고, 템플릿을 다운로드 하 여 변경 내용을 적용 하 고, Azure Resource Manager를 통해 템플릿을 제출할 수 있습니다.
* 에이전트는 Log Analytics 작업 영역 및 메트릭 모두에 데이터를 보냅니다.
* 데이터 모니터링:
   * **Log Analytics 데이터**: 마이그레이션 전에는 networkmonitoring 테이블에 NPM가 구성 된 작업 영역에 데이터가 남아 있습니다. 마이그레이션 후에 데이터는 동일한 작업 영역에 있는 NetworkMonitoring 테이블 및 ConnectionMonitor_CL 테이블로 이동 됩니다. NPM에서 테스트를 사용 하지 않도록 설정 하면 데이터는 ConnectionMonitor_CL 테이블에만 저장 됩니다.
   * **로그 기반 경고, 대시보드 및 통합**: 새 ConnectionMonitor_CL 테이블을 기반으로 쿼리를 수동으로 편집 해야 합니다. 메트릭에 대 한 경고를 다시 만들려면 [연결 모니터를 사용 하 여 네트워크 연결 모니터링](./connection-monitor-overview.md#metrics-in-azure-monitor)을 참조 하세요.
    
## <a name="prerequisites"></a>필수 조건

* 구독 및 Log Analytics 작업 영역의 지역에서 Network Watcher를 사용 하도록 설정 했는지 확인 합니다.
* Log Analytics 에이전트가 설치 된 Azure 가상 머신은 Network Watcher 확장을 사용 하 여 사용 하도록 설정 해야 합니다.

## <a name="migrate-the-tests"></a>테스트 마이그레이션

네트워크 성능 모니터에서 연결 모니터로 테스트를 마이그레이션하려면 다음을 수행 합니다.

1. Network Watcher에서 **연결 모니터** 를 선택 하 고 **NPM에서 테스트 마이그레이션** 탭을 선택 합니다. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="네트워크 성능 모니터에서 연결 모니터로 테스트 마이그레이션" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. 드롭다운 목록에서 구독 및 작업 영역을 선택한 다음 마이그레이션할 NPM 기능을 선택 합니다. 현재는 서비스 연결 모니터 에서만 테스트를 마이그레이션할 수 있습니다.  
1. **가져오기** 를 선택 하 여 테스트를 마이그레이션합니다.

마이그레이션이 시작 된 후에는 다음 변경 내용이 적용 됩니다. 
* 새 연결 모니터 리소스가 생성 됩니다.
   * 지역 및 구독 당 하나의 연결 모니터가 생성 됩니다. 온-프레미스 에이전트를 사용 하는 테스트의 경우에는 새 연결 모니터 이름이로 지정 됩니다 `<workspaceName>_"on-premises"` . Azure 에이전트를 사용 하는 테스트의 경우 새 연결 모니터 이름이로 지정 됩니다 `<workspaceName>_<Azure_region_name>` .
   * 이제 NPM가 사용 하도록 설정 된 동일한 Log Analytics 작업 영역에 Connectionmonitor_CL 라는 새 테이블에 모니터링 데이터가 저장 됩니다. 
   * 테스트 이름은 테스트 그룹 이름으로 전달 됩니다. 테스트 설명이 마이그레이션되지 않습니다.
   * 원본 및 대상 끝점은 새 테스트 그룹에서 만들어지고 사용 됩니다. 온-프레미스 에이전트의 경우 끝점의 형식은로 지정 됩니다 `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Azure의 경우 마이그레이션 테스트에 실행 중이 아닌 에이전트가 포함 되어 있는 경우 에이전트를 사용 하도록 설정 하 고 다시 마이그레이션해야 합니다.
   * 대상 포트 및 검색 간격이 *TC_ \<testname>* 이라는 테스트 구성으로 이동 하 고 *\<testname> _AppThresholds TC_*. 프로토콜은 포트 값을 기반으로 설정 됩니다. 성공 임계값 및 기타 선택적 속성은 비워 둡니다.
* NPM는 사용 하지 않도록 설정 되므로 마이그레이션된 테스트는 NetworkMonitoring 및 ConnectionMonitor_CL 테이블로 데이터를 계속 보낼 수 있습니다. 이 방법을 사용 하면 기존 로그 기반 경고 및 통합이 영향을 받지 않습니다.
* 새로 만든 연결 모니터는 연결 모니터에서 볼 수 있습니다.

마이그레이션 후 다음을 수행 해야 합니다.
* NPM에서 테스트를 수동으로 사용 하지 않도록 설정 합니다. 이렇게 할 때까지 요금이 계속 청구 됩니다. 
* NPM를 사용 하지 않도록 설정 하는 동안 ConnectionMonitor_CL 테이블에 대 한 경고를 다시 만들거나 메트릭을 사용 합니다. 
* ConnectionMonitor_CL 테이블로 외부 통합을 마이그레이션합니다. 외부 통합의 예로는 Power BI 및 Grafana의 대시보드와 SIEM (보안 정보 및 이벤트 관리) 시스템과의 통합이 있습니다.


## <a name="next-steps"></a>다음 단계

연결 모니터에 대 한 자세한 내용은 다음을 참조 하세요.
* [연결 모니터에서 연결 모니터로 마이그레이션](/azure/network-watcher/migrate-to-connection-monitor-from-connection-monitor-classic)
* [Azure Portal를 사용 하 여 연결 모니터 만들기](./connection-monitor-create-using-portal.md)