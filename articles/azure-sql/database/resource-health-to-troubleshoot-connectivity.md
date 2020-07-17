---
title: Azure Resource Health를 사용 하 여 데이터베이스 상태 모니터링
description: Azure Resource Health를 사용 하 여 Azure SQL Database 및 Azure SQL Managed Instance 상태를 모니터링 하면 Azure 문제가 SQL 리소스에 영향을 주는 경우 진단 하 고 지원을 받을 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 27865afd356be9eac64083c1ebdeb6ced43dbd18
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986945"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Resource Health를 사용 하 여 Azure SQL Database 및 Azure SQL에 대 한 연결 문제를 해결 Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에 대 한 [Resource Health](../../service-health/resource-health-overview.md#get-started) 를 통해 AZURE 문제가 SQL 리소스에 영향을 주는 경우 진단 하 고 지원을 받을 수 있습니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제에 대 한 도움이 필요한 경우 기술 지원을 제공 합니다.

![개요](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>상태 확인

Resource Health는 리소스에 대한 로그인의 성공 및 실패를 검사하여 SQL 리소스의 상태를 결정합니다. 현재 SQL Database 리소스에 대 한 Resource Health는 시스템 오류로 인 한 로그인 오류만 검사 하 고 사용자 오류는 검사 하지 않습니다. Resource Health 상태는 1 ~ 2 분 마다 업데이트 됩니다.

## <a name="health-states"></a>성능 상태

### <a name="available"></a>사용 가능

**사용 가능한** 상태는 SQL 리소스에서 시스템 오류로 인 한 로그인 실패를 검색 하지 못했음을 Resource Health 합니다.

![사용 가능](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>성능 저하됨

**저하** 됨 상태는 Resource Health에서 대부분의 성공한 로그인을 검색 했지만 일부 오류가 발생 했음을 의미 합니다. 이러한 오류는 일시적인 로그인 오류일 가능성이 높습니다. 일시적인 로그인 오류로 인해 발생 하는 연결 문제로 인 한 영향을 줄이려면 코드에서 [재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) 를 구현 합니다.

![성능 저하됨](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>사용할 수 없음

**사용할 수 없음** 상태는 Resource Health가 SQL 리소스에 대한 일관된 로그인 실패를 감지했음을 의미 합니다. 리소스가 오랜 시간 동안이 상태를 유지 하는 경우 지원 담당자에 게 문의 하세요.

![사용할 수 없음](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>알 수 없음

**알 수 없음** 상태는 Resource Health에서 이 리소스에 대한 정보를 10분 넘게 수신하지 못했음을 나타냅니다. 이 상태는 리소스 상태에 대한 결정적인 표시는 아니지만 문제 해결 프로세스의 중요한 데이터 요소입니다. 리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 사용 가능으로 변경됩니다. 리소스에 문제가 발생하는 경우 상태가 알 수 없음이면, 플랫폼의 이벤트가 리소스에 영향을 미치고 있다는 것을 의미할 수 있습니다.

![알 수 없음](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>기록 정보

Resource Health의 상태 기록 섹션에서 최대 14 일 동안의 상태 기록에 액세스할 수 있습니다. 섹션에는 Resource Health에서 보고하는 가동 중지 시간에 대한 가동 중지 시간 이유(가능한 경우)도 포함됩니다. 현재 Azure는 2 분 단위로 데이터베이스 리소스의 가동 중지 시간을 보여 줍니다. 실제 가동 중지 시간은 1분 미만일 가능성이 높습니다. 평균은 8 초입니다.

### <a name="downtime-reasons"></a>가동 중지 시간 이유

데이터베이스에 가동 중지 시간이 발생 하는 경우 이유를 확인 하기 위해 분석이 수행 됩니다. 사용 가능한 경우 가동 중지 시간 이유가 Resource Health의 상태 기록 섹션에 보고됩니다. 가동 중지 시간 이유는 일반적으로 이벤트 발생 후 30분에 게시됩니다.

#### <a name="planned-maintenance"></a>계획된 유지 보수

Azure 인프라는 데이터 센터에서 하드웨어 또는 소프트웨어 구성 요소를 업그레이드 하는 계획 된 유지 관리를 정기적으로 수행 합니다. 데이터베이스에서 유지 관리를 수행 하는 동안 Azure SQL은 일부 기존 연결을 종료 하 고 새 연결을 거부할 수 있습니다. 계획 된 유지 관리 중에 발생 하는 로그인 실패는 일반적으로 일시적 이며 [다시 시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) 를 통해 영향을 줄일 수 있습니다. 로그인 오류가 계속 발생 하면 지원 담당자에 게 문의 하세요.

#### <a name="reconfiguration"></a>Reconfiguration

재구성는 일시적인 조건으로 간주 되며, 시간에서 예상 됩니다. 이러한 이벤트는 부하 분산 또는 소프트웨어/하드웨어 실패에 의해 트리거될 수 있습니다. 클라우드 데이터베이스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 이러한 상황을 완화하므로 강력한 연결 [재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 하며 일반적으로 오류를 최종 사용자에게 투명하게 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [일시적인 오류에 대 한 재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)에 대해 자세히 알아보세요.
- [SQL 연결 오류를 해결, 진단 및 방지](troubleshoot-common-connectivity-issues.md)합니다.
- [Resource Health 경고 구성](../../service-health/resource-health-alert-arm-template-guide.md)에 대해 자세히 알아보세요.
- [Resource Health](../../application-gateway/resource-health-overview.md)에 대 한 개요를 가져옵니다.
- [RESOURCE HEALTH FAQ](../../service-health/resource-health-faq.md)를 검토 합니다.
