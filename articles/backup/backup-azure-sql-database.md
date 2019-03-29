---
title: Azure에 SQL Server 데이터베이스 백업 | Microsoft Docs
description: 이 자습서에서는 Azure에 SQL Server를 백업하는 방법을 설명합니다. SQL Server 복구에 대해서도 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 4eaaff859811e4d97cbd4f73231d702285792064
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285448"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM의 SQL Server 백업 정보

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. [Azure Backup](backup-overview.md)을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업할 수 있습니다.

## <a name="backup-process"></a>백업 프로세스

이 솔루션은 SQL 네이티브 API를 활용하여 SQL 데이터베이스를 백업합니다.

* 보호하려는 SQL Server VM을 지정하고 그 안에 있는 데이터베이스를 쿼리하면 Azure Backup 서비스가 `AzureBackupWindowsWorkload` 확장이라는 이름으로 VM에 워크로드 백업 확장을 설치합니다.
* 이 확장은 코디네이터 및 SQL 플러그 인으로 구성됩니다. 코디네이터는 백업 구성, 백업 및 복원 같은 다양한 워크플로를 트리거하는 역할을 담당하고, 플러그 인은 실제 데이터 흐름을 담당합니다.
* 이 VM의 데이터베이스를 검색하기 위해 Azure Backup은  `NT SERVICE\AzureWLBackupPluginSvc`라는 계정을 만듭니다. 이 계정은 백업 및 복원에 사용되며 SQL sysadmin 권한이 필요합니다. Azure Backup은 데이터베이스 검색/조회에  `NT AUTHORITY\SYSTEM`  계정을 활용하므로, 이 계정은 SQL에서 공용 로그인이어야 합니다. Azure Marketplace에서 SQL Server VM을 만들지 않은 경우  **UserErrorSQLNoSysadminMembership** 오류가 발생할 수 있습니다. 이 경우  [다음 지침을 따릅니다](backup-azure-sql-database.md).
* 선택한 데이터베이스에서 보호 구성을 트리거하면 백업 서비스가 백업 일정과 기타 정책 세부 정보를 사용하여 코디네이터를 설정하고, 확장이 VM에 로컬로 캐시합니다. 
* 예약된 시간에 코디네이터가 플러그 인과 통신하고 VDI를 사용하여 SQL 서버의 백업 데이터를 스트리밍하기 시작합니다.  
* 플러그 인은 복구 서비스 자격 증명 모음에 직접 데이터를 보내기 때문에 준비 위치가 필요 없습니다. 데이터는 Azure Backup 서비스를 통해 암호화되어 스토리지 계정에 저장됩니다.
* 데이터 전송이 완료되면 코디네이터가 백업 서비스를 사용하여 커밋을 확인합니다.

  ![SQL 백업 아키텍처](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 확인합니다.

1. Azure에서 실행되는 SQL Server 인스턴스가 있는지 확인합니다. [SQL Server 인스턴스를 마켓플레이스에 빠르게 만들](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) 수 있습니다.
2. [기능 고려 사항](#feature-consideration-and-limitations) 및 [시나리오 지원](#scenario-support)을 검토합니다.
3. 이 시나리오에 대한 [일반적인 질문을 검토](faq-backup-sql-server.md)합니다.

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 배포** | SQL Marketplace Azure VM 및 비 Marketplace VM(SQL Server가 수동 설치됨)이 지원됩니다.
**지원되는 지역** | 오스트레일리아 동남부(ASE), 오스트레일리아 동부(AE) <br> 브라질 남부(BRS)<br> 캐나다 중부(CNC), 캐나다 동부(CE)<br> 동남 아시아(SEA), 동아시아(EA) <br> 미국 동부(EUS), 미국 동부 2(EUS2), 미국 중서부(WCUS), 미국 서부(WUS), 미국 서부 2(WUS 2), 미국 중북부(NCUS), 미국 중부(CUS), 미국 중남부(SCUS) <br> 인도 중부(INC), 인도 남부(INS) <br> 일본 동부(JPE), 일본 서부(JPW) <br> 한국 중부(KRC), 한국 남부(KRS) <br> 북유럽(NE), 서유럽 <br> 영국 남부(UKS), 영국 서부(UKW)
**지원되는 운영 체제** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux는 현재 지원되지 않습니다.
**지원되는 SQL Server 버전** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012<br/><br/> Enterprise, Standard, Web, Developer, Express
**지원되는 .NET 버전** | VM에 설치된 .NET Framework 4.5.2 이상

## <a name="feature-consideration-and-limitations"></a>기능 고려 사항 및 제한 사항

- SQL Server 백업은 Azure Portal 또는 **PowerShell**에서 구성할 수 있습니다. CLI는 지원되지 않습니다.
- SQL Server를 실행하는 VM에서 Azure 공용 IP 주소에 액세스하려면 인터넷 연결이 필요합니다.
- SQL Server Always On **FCI(장애 조치(failover) 클러스터 인스턴스)** 가 지원되지 않습니다.
- 미러 데이터베이스와 데이터베이스 스냅숏에 대한 백업 및 복원 작업은 지원되지 않습니다.
- 백업 솔루션을 2개 이상 사용하여 독립 실행형 SQL Server 인스턴스 또는 SQL Always On 가용성 그룹을 백업하면 오류가 발생할 수 있으므로 그렇게 하지 말아야 합니다.
- 같은 솔루션 또는 다른 솔루션을 사용하여 한 가용성 그룹의 두 노드를 개별적으로 백업해도 오류가 발생할 수 있습니다. Azure Backup은 자격 증명 모음과 동일한 지역에 있는 모든 노드를 검색 및 보호할 수 있습니다. SQL Server Always On 가용성 그룹이 여러 Azure 지역에 걸쳐 있는 경우 주 노드가 있는 Azure 지역에서 백업을 설정합니다. 그러면 Azure Backup이 백업 기본 설정에 따라 가용성 그룹의 모든 데이터베이스를 검색하고 보호할 수 있습니다.  
- Azure Backup은 **읽기 전용** 데이터베이스에 전체 백업 및 복사 전용 전체 백업만 지원합니다.
- 많은 수의 파일이 있는 데이터베이스는 보호할 수 없습니다. 지원되는 최대 파일 수는 **1000**개입니다.  
- 하나의 자격 증명 모음에 최대 **2,000**개의 SQL Server 데이터베이스를 백업할 수 있습니다. 데이터베이스 수가 이보다 더 많은 경우 자격 증명 모음을 여러 개 만들면 됩니다.
- 한 번에 데이터베이스 **50**개까지 백업을 구성할 수 있습니다. 이 제한은 백업 부하 최적화에 도움이 됩니다.
- 지원되는 최대 데이터베이스 크기는 **2TB**이며, 이보다 크면 성능 문제가 발생할 수 있습니다.
- 서버당 보호할 수 있는 데이터베이스 수를 알 수 있도록 대역폭, VM 크기, 백업 빈도, 데이터베이스 크기 등의 요소를 고려해야 합니다. 이러한 수치를 직접 계산할 수 있도록 플래너를 개발하는 중이며, 조만간 게시할 예정입니다.
- 가용성 그룹의 경우 백업은 몇 가지 요소에 따라 다른 노드에서 수행됩니다. 아래는 가용성 그룹에 대한 백업 동작을 요약한 것입니다.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Always On 가용성 그룹의 백업 동작

백업 기본 설정 및 백업 유형(전체/차등/로그/복사 전용 전체)에 따라 특정 노드(주/보조)에서 백업이 수행됩니다.

- **백업 기본 설정: 주**

**백업 유형** | **Node**
    --- | ---
    전체 | 보조
    차등 | 보조
    로그 |  보조
    복사 전용 전체 |  보조

- **백업 기본 설정: 보조만**

**백업 유형** | **Node**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

- **백업 기본 설정: 보조**

**백업 유형** | **Node**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

- **백업 기본 설정 없음**

**백업 유형** | **Node**
--- | ---
전체 | 보조
차등 | 보조
로그 |  주
복사 전용 전체 |  주

## <a name="fix-sql-sysadmin-permissions"></a>SQL sysadmin 권한 수정

  **UserErrorSQLNoSysadminMembership** 오류로 인해 권한을 수정해야 하는 경우 다음을 수행합니다.

  1. SQL Server sysadmin 권한이 있는 계정을 사용하여 SSMS(SQL Server Management Studio)에 로그인합니다. 특별한 사용 권한이 필요하지 않으면 Windows 인증이 작동해야 합니다.
  2. SQL Server에서 **Security/Logins** 폴더를 엽니다.

      ![Security/Logins 폴더를 열어서 계정 보기](./media/backup-azure-sql-database/security-login-list.png)

  3. **Logins** 폴더를 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 선택합니다. **로그인 - 새로 만들기**에서 **검색**을 선택합니다.

      ![로그인 - 신규 대화 상자에서 검색 선택](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 가상 서비스 계정 **NT SERVICE\AzureWLBackupPluginSvc**는 가상 머신 등록 및 SQL 검색 단계 중에 만들어졌습니다. **선택할 개체 이름 입력**에 표시된 대로 계정 이름을 입력합니다. **이름 확인**을 선택하여 이름을 확인합니다. **확인**을 클릭합니다.

      ![이름 확인을 클릭하여 알 수 없는 서비스 이름 확인](./media/backup-azure-sql-database/check-name.png)

  5. **서버 역할**에서 **sysadmin** 역할이 선택되었는지 확인합니다. **확인**을 클릭합니다. 이제 필요한 권한이 있어야 합니다.

      ![sysadmin 서버 역할이 선택되어 있는지 확인](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 이제 데이터베이스를 Recovery Services 자격 증명 모음에 연결합니다. Azure Portal의 **보호된 서버** 목록에서 오류 상태인 서버를 마우스 오른쪽 단추로 클릭하고 **DB 다시 검색**을 클릭합니다.

      ![서버서 적절한 권한이 있는지 확인](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. **알림** 영역에서 진행률을 확인합니다. 선택한 데이터베이스가 발견되면 성공 메시지가 표시됩니다.

      ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스 백업에 대해 [자세히 알아봅니다](backup-sql-server-database-azure-vms.md).
- 백업된 SQL Server 데이터베이스를 복원하는 방법을 [알아봅니다](restore-sql-database-azure-vm.md).
- 백업된 SQL Server 데이터베이스를 관리하는 방법을 [알아봅니다](manage-monitor-sql-database-backup.md).
