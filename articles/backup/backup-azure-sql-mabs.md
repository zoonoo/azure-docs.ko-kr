---
title: Azure 백업 서버를 사용하여 SQL 서버 백업
description: 이 문서에서는 Microsoft Azure 백업 서버(MABS)를 사용하여 SQL Server 데이터베이스를 백업하는 구성을 알아봅니다.
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505953"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Azure 백업 서버를 사용하여 SQL 서버를 Azure에 백업

이 문서에서는 Microsoft Azure 백업 서버(MABS)를 사용하여 SQL Server 데이터베이스의 백업을 설정하는 데 도움이 됩니다.

SQL Server 데이터베이스를 백업하고 Azure에서 복구하려면 다음을 수행하십시오.

1. Azure에서 SQL Server 데이터베이스를 보호하기 위한 백업 정책을 만듭니다.
1. Azure에서 주문형 백업 복사본을 만듭니다.
1. Azure에서 데이터베이스를 복구합니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 Azure 백업 [서버를 설치하고 준비했어야](backup-azure-microsoft-azure-backup.md)합니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기 

Azure에서 SQL Server 데이터베이스를 보호하려면 먼저 백업 정책을 만듭니다.

1. Azure 백업 서버에서 **보호** 작업 영역을 선택합니다.
1. 보호 그룹을 만들려면 **새로 만들기를** 선택합니다.

    ![Azure 백업 서버에서 보호 그룹 만들기](./media/backup-azure-backup-sql/protection-group.png)
1. 시작 페이지에서 보호 그룹을 만드는 방법에 대한 지침을 검토합니다. 그런 다음 을 **선택합니다.**
1. 보호 그룹 유형의 경우 **서버를 선택합니다.**

    ![서버 보호 그룹 유형 선택](./media/backup-azure-backup-sql/pg-servers.png)
1. 백업하려는 데이터베이스가 있는 SQL Server 컴퓨터를 확장합니다. 해당 서버에서 백업할 수 있는 데이터 원본이 표시됩니다. **모든 SQL 공유를** 확장한 다음 백업할 데이터베이스를 선택합니다. 이 예제에서는 ReportServer$MSDPM2012 및 ReportServer$MSDPM2012TempDB를 선택합니다. **다음**을 선택합니다.

    ![SQL Server 데이터베이스 선택](./media/backup-azure-backup-sql/pg-databases.png)
1. 보호 그룹의 이름을 지정한 다음 **온라인 보호를 선택합니다.**

    ![단기 디스크 보호 또는 온라인 Azure 보호 - 데이터 보호 방법 선택](./media/backup-azure-backup-sql/pg-name.png)
1. 단기 **목표 지정** 페이지에 디스크에 백업 지점을 만드는 데 필요한 입력을 포함합니다.

    이 예제에서는 **보존 범위가** *5일로*설정됩니다. 백업 **동기화 빈도는** *15분마다*한 번으로 설정됩니다. **익스프레스 전체 백업은** *8:00 PM으로*설정됩니다.

    ![백업 보호를 위한 단기 목표 설정](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 이 예제에서는 매일 오후 8:00에 백업 지점이 만들어집니다. 전날 8:00 PM 백업 지점 이후 수정된 데이터가 전송됩니다. 이 프로세스를 **빠른 전체 Backup**이라고 합니다. 트랜잭션 로그는 15분마다 동기화되지만 9:00 PM에 데이터베이스를 복구해야 하는 경우 이 예제에서는 8:00 PM인 마지막 express 전체 백업 지점에서 로그를 재생하여 지점이 만들어집니다.
   >
   >

1. **다음**을 선택합니다. MABS는 사용 가능한 전체 저장 공간을 보여 주며 또한 잠재적인 디스크 공간 사용률을 보여 주기도 합니다.

    ![MABS에서 디스크 할당 설정](./media/backup-azure-backup-sql/pg-storage.png)

    기본적으로 MABS는 데이터 원본(SQL Server 데이터베이스)당 하나의 볼륨을 만듭니다. 볼륨은 초기 백업 복사본에 사용됩니다. 이 구성에서 LDM(논리 디스크 관리자)은 MABS 보호를 300개의 데이터 원본(SQL Server 데이터베이스)으로 제한합니다. 이 제한을 해결하려면 **DPM 스토리지 풀에 데이터 공동 배치**를 선택합니다. 이 옵션을 사용하는 경우 MABS는 여러 데이터 원본에 대해 단일 볼륨을 사용합니다. 이 설정을 통해 MABS는 최대 2,000개의 SQL Server 데이터베이스를 보호할 수 있습니다.

    **볼륨을 자동으로 늘리는**것을 선택하면 MABS는 프로덕션 데이터가 증가함에 따라 증가된 백업 볼륨을 설명할 수 있습니다. **볼륨을 자동으로 늘리는**것을 선택하지 않으면 MABS는 백업 저장소를 보호 그룹의 데이터 원본으로 제한합니다.
1. 관리자인 경우 이 초기 백업을 **네트워크를 통해 자동으로** 전송하도록 선택하고 전송 시간을 선택할 수 있습니다. 또는 **수동으로** 백업을 전송하도록 선택합니다. 그런 다음 을 **선택합니다.**

    ![MABS에서 복제 생성 방법 선택](./media/backup-azure-backup-sql/pg-manual.png)

    초기 백업 복사본에는 전체 데이터 원본(SQL Server 데이터베이스)을 전송해야 합니다. 백업 데이터는 프로덕션 서버(SQL Server 컴퓨터)에서 MABS로 이동합니다. 이 백업이 큰 경우 네트워크를 통해 데이터를 전송하면 대역폭이 정체될 수 있습니다. 이러한 이유로 관리자는 이동식 미디어를 사용하여 초기 백업을 수동으로 전송하도록 선택할 수 **있습니다.** 또는 지정된 시간에 **네트워크를 통해 자동으로** 데이터를 전송할 수 있습니다.

    초기 백업이 완료되면 초기 백업 복사본에서 백업이 점진적으로 계속됩니다. 증분 백업은 크기가 작으며 네트워크를 통해 간편하게 전송될 수 있습니다.
1. 일관성 검사를 실행할 시기를 선택합니다. 그런 다음 을 **선택합니다.**

    ![일관성 검사를 실행할 시기 선택](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS는 백업 지점의 무결성에 대한 일관성 검사를 실행할 수 있습니다. 프로덕션 서버(이 예제의 SQL Server 컴퓨터)에서 백업 된 파일의 체크섬과 MABS의 해당 파일에 대한 백업 된 데이터를 계산합니다. 검사에서 충돌이 발견되면 MABS의 백업된 파일이 손상된 것으로 가정합니다. MABS는 체크섬 불일치에 해당하는 블록을 전송하여 백업된 데이터를 수정합니다. 일관성 검사는 성능이 많이 다 사용하기 위한 작업이므로 관리자는 일관성 검사를 예약하거나 자동으로 실행할 수 있습니다.
1. Azure에서 보호할 데이터 원본을 선택합니다. 그런 다음 을 **선택합니다.**

    ![Azure에서 보호할 데이터 원본 선택](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 관리자인 경우 조직의 정책에 맞는 백업 일정 및 보존 정책을 선택할 수 있습니다.

    ![일정 및 보존 정책 선택](./media/backup-azure-backup-sql/pg-schedule.png)

    이 예제에서는 백업이 매일 12:00 PM 및 8:00 PM에 수행됩니다.

    > [!TIP]
    > 빠른 복구를 위해 디스크에 몇 가지 단기 복구 지점을 유지하십시오. 이러한 복구 지점은 운영 복구에 사용됩니다. Azure는 더 높은 SLA와 보장된 가용성을 제공하는 좋은 오프사이트 위치역할을 합니다.
    >
    > DPM(데이터 보호 관리자)을 사용하여 로컬 디스크 백업이 완료된 후 Azure 백업을 예약합니다. 이 연습을 수행하면 최신 디스크 백업이 Azure에 복사됩니다.
    >


1. 보존 정책 일정을 선택합니다. 보존 정책의 작동 방식에 대한 자세한 내용은 [Azure Backup 사용을 참조하여 테이프 인프라를 대체합니다.](backup-azure-backup-cloud-as-tape.md)

    ![MABS에서 보존 정책 선택](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    이 예제에 대한 설명:

    * 백업은 매일 12:00 PM 및 8:00 PM에 수행됩니다. 그들은 180 일 동안 유지됩니다.
    * 토요일 오후 12:00에 백업은 104주 동안 유지됩니다.
    * 매월 마지막 토요일 오후 12:00의 백업은 60개월 동안 유지됩니다.
    * 3월 마지막 토요일 오후 12:00의 백업은 10년 동안 유지됩니다.

    보존 정책을 선택한 후 **다음**을 선택합니다.
1. 초기 백업 복사본을 Azure로 전송하는 방법을 선택합니다.

    * **네트워크 를 통해 자동으로** 데이터를 Azure로 전송 하는 백업 일정을 따릅니다.
    * **오프라인 백업에**대한 자세한 내용은 [오프라인 백업 개요를](offline-backup-overview.md)참조하십시오.

    전송 메커니즘을 선택한 후 **다음**을 선택합니다.
1. **요약** 페이지에서 정책 세부 정보를 검토합니다. 그런 다음 **그룹 만들기를**선택합니다. **닫기를** 선택하고 **모니터링** 작업 영역에서 작업 진행 률을 볼 수 있습니다.

    ![보호 그룹 생성 진행 상황](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>SQL Server 데이터베이스의 주문형 백업 복사본 만들기

복구 지점은 첫 번째 백업이 발생할 때 만들어집니다. 일정이 실행될 때까지 기다리지 않고 복구 지점 만들기를 수동으로 트리거할 수 있습니다.

1. 보호 그룹에서 데이터베이스 상태가 **정상인지**확인합니다.

    ![데이터베이스 상태를 표시하는 보호 그룹](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **복구 지점 만들기를**선택합니다.

    ![온라인 복구 지점 만들기 선택](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 드롭다운 메뉴에서 온라인 **보호**를 선택합니다. 그런 다음 **확인을** 선택하여 Azure에서 복구 지점 만들기를 시작합니다.

    ![Azure에서 복구 지점 만들기 시작](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. **모니터링** 작업 영역에서 작업 진행 률을 볼 수 있습니다. 

    ![모니터링 콘솔에서 작업 진행 상황 보기](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure에서 SQL Server 데이터베이스 복구

Azure에서 SQL Server 데이터베이스와 같은 보호된 엔터티를 복구하려면 다음을 수행합니다.

1. DPM 서버 관리 콘솔을 엽니다. **복구** 작업 영역으로 이동하여 DPM이 백업하는 서버를 확인합니다. 데이터베이스를 선택합니다(이 예에서는 ReportServer$MSDPM2012). **온라인**로 끝나는 **복구 시간을** 선택합니다.

    ![복구 지점 선택](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 데이터베이스 이름을 마우스 오른쪽 단추로 클릭하고 **복구를**선택합니다.

    ![Azure에서 데이터베이스 복구](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM에서는 복구 지점에 대한 세부 정보를 보여줍니다. **다음**을 선택합니다. 데이터베이스를 덮어쓰려면 복구 형식 **SQL Server의 원본 인스턴스에 복구**를 선택합니다. 그런 다음 을 **선택합니다.**

    ![데이터베이스를 원래 위치로 복구](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    이 예제에서 DPM을 사용하면 데이터베이스를 다른 SQL Server 인스턴스 또는 독립 실행형 네트워크 폴더로 복구할 수 있습니다.
1. 복구 **옵션 지정** 페이지에서 복구 옵션을 선택할 수 있습니다. 예를 들어 네트워크 **대역폭 사용량 제한을** 선택하여 복구에서 사용하는 대역폭을 제한할 수 있습니다. 그런 다음 을 **선택합니다.**
1. **요약** 페이지에 현재 복구 구성이 표시됩니다. **복구를 선택합니다.**

    복구 상태는 복구 중인 데이터베이스를 보여 주며 **닫기를** 선택하여 마법사를 닫고 **모니터링** 작업 영역에서 진행 률을 볼 수 있습니다.

    ![복구 프로세스 시작](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    복구가 완료되면 복원된 데이터베이스가 응용 프로그램과 일치합니다.

### <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 백업 FAQ를](backup-azure-backup-faq.md)참조하십시오.
