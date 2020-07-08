---
title: Azure 유지 관리 이벤트 계획
description: Azure SQL Database 및 Azure SQL Managed Instance에서 계획 된 유지 관리 이벤트를 준비 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987221"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 Azure 유지 관리 이벤트 계획
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 데이터베이스에 대 한 계획 된 유지 관리 이벤트를 준비 하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트란?

각 데이터베이스 Azure SQL Database 및 Azure SQL Managed Instance는 한 복제본이 주 복제본 인 데이터베이스 복제본의 쿼럼을 유지 합니다. 항상 주 복제본이 온라인 서비스 여야 하 고 하나 이상의 보조 복제본이 정상 상태 여야 합니다. 계획된 유지 관리 기간 동안 데이터베이스 쿼럼 멤버는 한 번에 하나씩 오프라인 상태가 되는데, 그 이유는 응답하는 주 복제본이 하나 있고 보조 복제본이 하나 이상 있어서 클라이언트 가동 중단 시간이 없도록 보장하기 위해서 입니다. 주 복제본을 오프라인으로 전환해야 하는 경우, 재구성/장애 조치(failover) 프로세스가 발생하고 하나의 보조 복제본이 새로운 주 복제본이 됩니다.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트 기간 동안 예상되는 상황

재구성/장애 조치 (failover)는 일반적으로 30 초 이내에 완료 됩니다. 평균은 8 초입니다. 이미 연결되어있는 경우, 애플리케이션은 데이터베이스의 새로운 주 복제본을 정상적으로 복제해야 합니다. 새 주 복제본이 온라인 상태가 되기 전에 데이터베이스를 다시 구성 하는 동안 새 연결을 시도 하면 오류 40613 (데이터베이스를 사용할 수 없음): "{servername} ' 서버에서" 데이터베이스 ' {databasename} '을 (를) 현재 사용할 수 없습니다. 나중에 다시 연결해 보십시오.”를 식별하고 해결하는 방법을 설명합니다. 데이터베이스에 장기 실행 쿼리가 있는 경우이 쿼리는 재구성 중에 중단 되므로 다시 시작 해야 합니다.

## <a name="retry-logic"></a>재시도 논리

클라우드 데이터베이스 서비스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 강력한 연결 [재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 합니다. 그러면 이러한 상황을 완화하는 데 도움이 되며 일반적으로 최종 사용자에게 오류를 투명하게 만들 수 있습니다.

## <a name="frequency"></a>빈도

평균적으로 계획된 유지 관리 이벤트는 매달 1.7개가 발생합니다.

## <a name="resource-health"></a>리소스 상태

데이터베이스에 로그온 오류가 발생 한 경우 [Azure Portal](https://portal.azure.com) 에서 현재 상태에 대 한 [Resource Health](../../service-health/resource-health-overview.md#get-started) 창을 확인 합니다. 상태 기록 섹션에는 각 이벤트에 대한 가동 중지 시간 이유가 포함됩니다(가능한 경우).

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 및 Azure SQL Managed Instance에 대 한 [Resource Health](resource-health-to-troubleshoot-connectivity.md) 에 대해 자세히 알아보세요.
- 재시도 논리에 대 한 자세한 내용은 [일시적인 오류에 대 한 다시 시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 참조 하세요.
