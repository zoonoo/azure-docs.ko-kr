---
title: Azure 리소스 이동 기를 사용 하 여 지역 간에 Azure SQL 리소스 이동을 지원 합니다.
description: Azure 리소스 이동 기를 사용 하 여 지역 간에 Azure SQL 리소스 이동에 대 한 지원을 검토 합니다.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 573d52b836aef36063dd288bf5a5016b98d220ef
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524133"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure 지역 간에 Azure SQL 리소스 이동에 대 한 지원

이 문서에는 azure 리소스를 사용 하 여 azure 지역 간에 azure SQL 리소스를 이동 하기 위한 지원 및 필수 구성 요소가 요약 되어 있습니다.

## <a name="requirements"></a>요구 사항

요구 사항은 다음 표에 요약 되어 있습니다.

**기능** | **지원됨/지원되지 않음** | **세부 정보**
--- | --- | ---
**Azure SQL Database Hyperscale** | 지원되지 않음 | 리소스 이동 기를 사용 하 여 Azure SQL Hyperscale 서비스 계층에서 데이터베이스를 이동할 수 없습니다.
**영역 중복** | 지원됨 |  지원 되는 이동 옵션:<br/><br/> -영역 중복성을 지 원하는 지역 간에<br/><br/> -영역 중복성을 지원 하지 않는 지역 간에<br/><br/> -영역 중복을 지원 하지 않는 지역으로 영역 중복성을 지 원하는 지역 간에.<br/><br/> -영역 중복성을 지원 하지 않는 영역 사이에서 영역 중복성을 지 원하는 영역 사이입니다. 
**데이터 동기화** | 허브/동기화 데이터베이스: 지원 되지 않음<br/><br/> 동기화 멤버: 지원 됨. | 동기화 멤버를 이동 하는 경우 새 대상 데이터베이스에 대 한 데이터 동기화를 설정 해야 합니다.
**기존 지역에서 복제** | 지원됨 | 기존 지역 복제본은 대상 지역의 새 주 복제본으로 다시 매핑됩니다.<br/><br/> 이동 후 시드가 초기화 되어야 합니다. [자세히 알아보기](../azure-sql/database/active-geo-replication-configure-portal.md)
**투명한 데이터 암호화 (TDE) Bring Your Own Key (BYOK)** | 지원됨 | 여러 지역에서 키 자격 증명 모음을 이동 하는 방법에 [대해 자세히 알아보세요](../key-vault/general/move-region.md) .
**서비스 관리 키를 사용 하는 TDE** | 지원됨. |  여러 지역에서 키 자격 증명 모음을 이동 하는 방법에 [대해 자세히 알아보세요](../key-vault/general/move-region.md) .
**동적 데이터 마스킹 규칙** | 지원됨. | 규칙은 이동의 일부로 대상 지역에 자동으로 복사 됩니다. [자세한 정보를 알아보세요](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Advanced Data Security** | 지원 안 됨 | 해결 방법: 대상 지역에서 SQL Server 수준으로 설정 합니다. [자세한 정보를 알아보세요](../azure-sql/database/azure-defender-for-sql.md).
**방화벽 규칙** | 지원 안 됨 | 해결 방법: 대상 지역에서 SQL Server에 대 한 방화벽 규칙을 설정 합니다. 데이터베이스 수준 방화벽 규칙이 원본 서버에서 대상 서버로 복사 됩니다. [자세한 정보를 알아보세요](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**감사 정책** | 지원 안 됨 | 이동 후 정책이 기본값으로 다시 설정 됩니다. 다시 설정 하는 방법을 [알아봅니다](../azure-sql/database/auditing-overview.md) .
**백업 보존** | 지원됨. | 원본 데이터베이스에 대한 백업 보존 정책은 대상 데이터베이스로 전달됩니다. 이동 후 설정을 수정 하는 방법에 [대해 알아봅니다](../azure-sql/database/long-term-backup-retention-configure.md) .
**자동 조정** | 지원 안 됨 | 해결 방법: 이동 후 자동 조정 설정을 설정 합니다. [자세한 정보를 알아보세요](../azure-sql/database/automatic-tuning-enable.md).
**데이터베이스 경고** | 지원 안 됨 | 해결 방법: 이동 후에 경고를 설정 합니다. [자세한 정보를 알아보세요](../azure-sql/database/alerts-insights-configure-portal.md).
**Azure SQL Server stretch database** | 지원되지 않음 | 리소스 이동 기를 사용 하 여 SQL server stretch database를 이동할 수 없습니다.
**Azure Synapse Analytics** | 지원되지 않음 | Synapse Analytics (이전의 SQL Data Warehouse)를 리소스 이동 기와 이동할 수 없습니다.
## <a name="next-steps"></a>다음 단계

리소스 이동 기를 사용 하 여 다른 지역으로 [AZURE SQL 리소스](tutorial-move-region-sql.md) 를 사용해 보세요.