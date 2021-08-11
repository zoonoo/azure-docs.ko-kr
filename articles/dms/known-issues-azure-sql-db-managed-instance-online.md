---
title: Azure SQL Managed Instance로의 온라인 마이그레이션에 대해 알려진 문제와 제한 사항
description: Azure SQL Managed Instance로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695539"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항

SQL Server에서 Azure SQL Managed Instance로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 아래에 설명되어 있습니다.

> [!IMPORTANT]
> SQL Server에서 Azure SQL Database로의 온라인 마이그레이션에서는 SQL_variant 데이터 형식의 마이그레이션이 지원되지 않습니다.

## <a name="backup-requirements"></a>백업 요구 사항

- **체크섬이 있는 백업**

    Azure Database Migration Service는 백업 및 복원 메서드를 사용하여 온-프레미스 데이터베이스를 SQL Managed Instance에 마이그레이션합니다. Azure Database Migration Service는 체크섬을 사용하여 만든 백업만 지원합니다.

    [백업 또는 복원 중 백업 체크섬 기능 사용 또는 사용 안 함(SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > 압축을 통해 데이터베이스 백업을 수행하는 경우 명시적으로 사용하지 않도록 설정하지 않는 한 체크섬은 기본적으로 작동합니다.

    오프라인 마이그레이션을 사용하면서 **Azure Database Migration Service가...** 를 선택하면 Azure Database Migration Service는 체크섬 옵션을 사용하도록 설정된 데이터베이스 백업을 수행합니다.

- **백업 미디어**

    별도의 백업 미디어(백업 파일)에서 모든 백업을 만들어야 합니다. Azure Database Migration Service는 단일 백업 파일에 추가되는 백업을 지원하지 않습니다. 전체 백업 및 로그 백업을 사용하여 백업 파일을 분리합니다.

## <a name="data-and-log-file-layout"></a>데이터 및 로그 파일 레이아웃

- **로그 파일 수**

    Azure Database Migration Service는 여러 로그 파일이 있는 데이터베이스를 지원하지 않습니다. 여러 로그 파일이 있는 경우 단일 트랜잭션 로그 파일로 축소하고 다시 조직합니다. 비어 있지 않은 로그 파일에 대해 원격으로 작업할 수 없으므로 먼저 로그 파일을 백업해야 합니다.

## <a name="sql-server-features"></a>SQL Server 기능

- **FileStream/FileTables**

    SQL Managed Instance는 현재 FileStream 및 FileTable을 지원하지 않습니다. 이러한 기능에 종속된 워크로드의 경우 Azure VM에서 실행되는 SQL Server를 Azure 대상으로 선택하는 것이 좋습니다.

- **메모리 내 테이블**

    메모리 내 OLTP는 SQL Managed Instance 프리미엄 및 중요 비즈니스용 계층에서 사용할 수 있습니다. 범용 계층은 메모리 내 OLTP를 지원하지 않습니다.

## <a name="migration-resets"></a>마이그레이션 다시 설정

- **배포**

    SQL Managed Instance는 자동 패치 및 버전 업데이트를 사용하는 PaaS 서비스입니다. SQL Managed Instance를 마이그레이션하는 동안 중요하지 않은 업데이트는 최대 36시간 동안 연기됩니다. 나중에(그리고 중요 업데이트의 경우) 마이그레이션이 중단되면 프로세스가 전체 복원 상태로 다시 설정됩니다.

    마이그레이션 컷오버는 전체 백업이 복원되고 모든 로그 백업을 따라잡은 후에만 호출할 수 있습니다. 프로덕션 마이그레이션 컷오버에 영향을 주는 경우 [Azure DMS 피드백 별칭](mailto:dmsfeedback@microsoft.com)에 문의하세요.

## <a name="smb-file-share-connectivity"></a>SMB 파일 공유 연결

SMB 파일 공유에 연결하는 문제는 권한 문제로 인해 발생할 수 있습니다. 

SMB 파일 공유 연결을 테스트하려면 다음 단계를 수행합니다. 

1. 백업을 SMB 파일 공유에 저장합니다. 
1. Azure Database Migration Service 서브넷과 원본 SQL Server 간의 네트워크 연결을 확인합니다. 이 작업을 수행하는 가장 쉬운 방법은 SQL Server 가상 머신을 DMS 서브넷에 배포하고 SQL Server Management Studio를 사용하여 원본 SQL Server에 연결하는 것입니다. 
1. 파일 공유의 백업에서 원본 SQL Server의 헤더를 복원합니다. 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

파일 공유에 연결할 수 없는 경우 다음 단계를 사용하여 사용 권한을 구성합니다. 

1. 파일 탐색기를 사용하여 파일 공유로 이동합니다. 
1. 해당 파일 공유를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다. 
1. **공유** 탭을 선택하고 **고급 공유** 를 선택합니다. 
1. 마이그레이션에 사용되는 Windows 계정을 추가하고 모든 권한을 할당합니다. 
1. SQL Server 서비스 계정을 추가하고 모든 권한을 할당합니다. 사용 중인 계정을 잘 모르는 경우 SQL Server 서비스 계정에 대한 **SQL Server 구성 관리자** 를 확인합니다. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="마이그레이션 및 SQL Server 서비스 계정에 사용되는 Windows 계정에 모든 권한을 부여합니다. ":::

