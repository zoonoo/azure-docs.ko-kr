---
title: 모범 사례 모니터링-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL 모니터링 하는 최선의 방법을 설명 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96354990"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Azure Database for MySQL 모니터링에 대 한 모범 사례-단일 서버

데이터베이스 작업을 모니터링 하 고 데이터 크기가 증가 함에 따라 성능이 손상 되지 않도록 하는 데 사용할 수 있는 모범 사례에 대해 알아보세요. 플랫폼에 새로운 기능을 추가 하는 경우이 섹션에서 자세히 설명 하는 모범 사례를 계속 구체화 합니다.

## <a name="layout-of-the-current-monitoring-toolkit"></a>현재 모니터링 도구 키트의 레이아웃

Azure Database for MySQL는 사용을 쉽게 모니터링 하 고, 리소스 (예: CPU, 메모리, i/o)를 추가 또는 제거 하 고, 잠재적인 문제를 해결 하 고, 데이터베이스의 성능을 향상 시키는 데 사용할 수 있는 도구와 방법을 제공 합니다. 정기적으로 [성능 메트릭을 모니터링](concepts-monitoring.md#metrics) 하 여 다양 한 시간 범위에 대 한 평균, 최대값 및 최소값을 확인할 수 있습니다.

메트릭 임계값에 대 한 [경고를 설정할](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) 수 있습니다. 그러면 서버가 이러한 제한에 도달 하 여 적절 한 조치를 취할 수 있습니다.  

데이터베이스 서버를 모니터링 하 여 데이터베이스에 할당 된 리소스가 응용 프로그램 작업을 처리할 수 있는지 확인 합니다. 데이터베이스가 리소스 제한에 도달 하는 경우 다음을 고려 하십시오.
    * 리소스를 많이 사용 하는 상위 쿼리를 식별 하 고 최적화 합니다. 
    * 서비스 계층을 업그레이드 하 여 리소스를 더 추가 합니다.

### <a name="cpu-utilization"></a>CPU 사용률
Cpu 사용량을 모니터링 하 고 데이터베이스가 CPU 리소스를 소모 하 고 있는지 여부를 모니터링 합니다. CPU 사용률이 90% 이상이 면 vCores의 수를 늘리거나 다음 가격 책정 계층으로 확장 하 여 계산을 확장 해야 합니다.  CPU를 확장/축소할 때 처리량 또는 동시성이 예상 대로 작동 하는지 확인 합니다. 

### <a name="memory"></a>메모리 
데이터베이스 서버에 사용할 수 있는 메모리 양은 [vcores 수](concepts-pricing-tiers.md)에 비례 합니다. 메모리가 작업에 충분 한지 확인 합니다. 응용 프로그램을 부하 테스트 하 여 읽기 및 쓰기 작업을 위한 메모리가 충분 한지 확인 합니다. 데이터베이스 메모리 소비가 정의 된 임계값을 초과 하는 경우가 자주 증가 하는 경우 vCores 이상 성능 계층을 증가 시켜 인스턴스를 업그레이드 해야 함을 나타냅니다. [쿼리 저장소](concepts-query-store.md), [쿼리 성능 권장 사항을](concepts-performance-recommendations.md) 사용 하 여 가장 긴 기간의 쿼리를 식별 합니다. 최적화할 수 있는 기회를 살펴보세요. 

### <a name="storage"></a>Storage 
MySQL 서버에 대해 프로 비전 된 [저장소의 양에](howto-create-manage-server-portal.md#scale-compute-and-storage) 따라 서버에 대 한 IOPs가 결정 됩니다. 서비스에서 사용 하는 저장소에는 데이터베이스 파일, 트랜잭션 로그, 서버 로그 및 백업 스냅숏이 포함 됩니다. 사용 된 디스크 공간이 총 프로 비전 된 디스크 공간의 85%를 초과 하지 않도록 합니다. 이 경우에는 데이터베이스 서버에서 데이터를 삭제 하거나 보관 하 여 공간을 확보 해야 합니다. 

### <a name="network-traffic"></a>네트워크 트래픽 

**네트워크 수신 처리량, 네트워크 전송 처리량** – 초당 메가바이트 단위에서 들어오고 나가는 네트워크 트래픽 속도입니다. 서버에 대 한 처리량 요구 사항을 평가 하 고 처리량이 예상 보다 낮은 경우 지속적으로 트래픽을 모니터링 해야 합니다. 

### <a name="database-connections"></a>데이터베이스 연결 
**데이터베이스 연결** -Azure Database for MySQL에 연결 된 클라이언트 세션 수는 [선택한 SKU 크기에 대 한 연결 제한과](concepts-server-parameters.md#max_connections) 맞춰야 합니다. 


## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL의 성능에 대 한 모범 사례](concept-performance-best-practices.md)
- [Azure Database for MySQL를 사용 하는 서버 작업에 대 한 모범 사례](concept-operation-excellence-best-practices.md)
