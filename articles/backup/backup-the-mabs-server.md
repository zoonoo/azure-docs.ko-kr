---
title: MABS 서버 백업
description: MABS (Microsoft Azure Backup 서버)를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: de62f0f57273ad7bd77df917d909627819165adb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946831"
---
# <a name="back-up-the-mabs-server"></a>MABS 서버 백업

MABS (Microsoft Azure Backup Server)가 실패할 경우 데이터를 복구할 수 있도록 하려면 MABS 서버를 백업 하기 위한 전략이 필요 합니다. 백업 되지 않은 경우에는 오류가 발생 한 후 수동으로 다시 빌드해야 하며 디스크 기반 복구 지점은 복구할 수 없습니다. Mabs 데이터베이스를 백업 하 여 MABS 서버를 백업할 수 있습니다.

## <a name="back-up-the-mabs-database"></a>MABS 데이터베이스 백업

MABS 백업 전략의 일환으로 MABS 데이터베이스를 백업 해야 합니다. MABS 데이터베이스의 이름은 DPMDB입니다. 이 데이터베이스에는 mabs의 백업에 대 한 데이터와 함께 MABS 구성이 포함 됩니다. 재해가 발생 한 경우 데이터베이스의 최근 백업을 사용 하 여 MABS 서버의 기능을 대부분 다시 작성할 수 있습니다. 데이터베이스를 복원할 수 있는 경우 테이프 기반 백업에 액세스할 수 있으며 모든 보호 그룹 설정 및 백업 일정이 유지 됩니다. MABS 저장소 풀 디스크가 가동 중단의 영향을 받지 않은 경우 다시 빌드한 후에도 디스크 기반 백업을 사용할 수 있습니다. 다양한 방법을 사용하여 데이터베이스를 백업할 수 있습니다.

|데이터베이스 백업 방법|장점|단점|
|--------------------------|--------------|-----------------|
|[Azure에 백업](#back-up-to-azure)|<li>MABS에서 쉽게 구성 하 고 모니터링 합니다.<li>백업 데이터베이스 파일의 여러 위치.<li>클라우드 스토리지가 재해 복구를 위한 강력한 솔루션을 제공합니다.<li>데이터베이스를 위한 매우 안전한 스토리지입니다.<li>120개의 온라인 복구 지점을 지원합니다.|<li>Azure 계정 및 추가 MABS 구성이 필요 합니다. Azure 스토리지에 대한 일부 비용이 발생합니다.<li> Azure backup 자격 증명 모음에 저장 된 MABS 백업에 대 한 액세스 권한을 얻으려면 Azure 에이전트와 함께 지원 되는 버전의 Windows Server 기반 시스템을 사용 해야 합니다. 다른 MABS 서버는 될 수 없습니다.<li>데이터베이스가 로컬로 호스팅되고 보조 보호를 사용하도록 설정하려는 경우 옵션이 아닙니다. <li>추가 준비 및 복구 시간이 발생합니다.|
|[MABS 저장소 풀을 백업 하 여 데이터베이스 백업](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>간단하게 구성하고 모니터링할 수 있습니다.<li>백업은 MABS 저장소 풀 디스크에 보관 되며 로컬에서 쉽게 액세스할 수 있습니다.<li>MABS 예약 된 백업은 512 빠른 전체 백업을 지원 합니다. 매시간 백업 하는 경우 21 일의 전체 보호가 제공 됩니다.|<li>재해 복구에는 적합한 옵션이 아닙니다. 온라인 상태 이며 MABS 서버 또는 저장소 풀 디스크에 오류가 발생 하는 경우 복구가 예상 대로 작동 하지 않을 수 있습니다.<li>데이터베이스가 로컬로 호스팅되고 보조 보호를 사용하도록 설정하려는 경우 옵션이 아닙니다. <li>MABS 서비스 또는 콘솔이 실행 되거나 작동 되지 않는 경우 복구 시점에 대 한 액세스 권한을 얻으려면 일부 준비 및 특별 단계가 필요 합니다.|
|[네이티브 SQL Server 백업을 사용하여 로컬 디스크에 백업](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>SQL Server에서 기본 제공됩니다.<li>백업은 쉽게 액세스할 수 있는 로컬 디스크에 보관 됩니다.<li>원하는 만큼 실행되도록 예약할 수 있습니다.<li>MABS와 완전히 독립적입니다.<li>백업 파일 정리를 예약할 수 있습니다.|<li>백업을 원격 위치에 복사하지 않는 한 좋은 재해 복구 옵션은 아닙니다.<li>백업에 대 한 로컬 저장소가 필요 하며이는 보존 및 빈도를 제한할 수 있습니다.|
|[네이티브 SQL 백업 및 MABS 보호를 사용 하 여 MABS로 보호 되는 공유에 백업](#back-up-to-a-share-protected-by-mabs)|<li>MABS에서 쉽게 모니터링 됩니다.<li>백업 데이터베이스 파일의 여러 위치.<li>네트워크의 모든 Windows 컴퓨터에서 쉽게 액세스할 수 있습니다.<li>잠재적으로 가장 빠른 복구 방법입니다.|<li>64개의 복구 지점만 지원합니다.<li>사이트 재해 복구에는 적합한 옵션이 아닙니다. MABS 서버 또는 MABS 저장소 풀 디스크 오류로 인해 복구 작업이 방해할 수 있습니다.<li>MABS DB가 로컬로 호스팅되고 보조 보호를 사용 하도록 설정 하려는 경우 옵션이 아닙니다. <li>구성 및 테스트에는 일부 추가적인 준비가 필요합니다.<li>MABS 서버 자체가 중단 되었지만 MABS 저장소 풀 디스크가 충분 한 경우 추가 준비 및 복구 시간이 필요 합니다.|

- MABS 보호 그룹을 사용 하 여 백업 하는 경우 데이터베이스에 대해 고유한 보호 그룹을 사용 하는 것이 좋습니다.

    > [!NOTE]
    > 복원 목적으로 mabs 데이터베이스를 사용 하 여 복원 하려는 MABS 설치는 mabs 데이터베이스 자체의 버전과 일치 해야 합니다.  예를 들어 복구 하려는 데이터베이스가 업데이트 롤업 1을 설치한 MABS V3에서 가져온 경우 MABS 서버는 업데이트 롤업 1과 동일한 버전을 실행 해야 합니다. 즉, 데이터베이스를 복원 하기 전에 MABS를 제거 하 고 호환 되는 버전으로 다시 설치 해야 할 수 있습니다.  데이터베이스 버전을 확인하려면 임시 데이터베이스 이름에 수동으로 탑재한 다음 해당 데이터베이스에 대해 SQL 쿼리를 실행하여 주 및 부 버전을 기준으로 마지막으로 설치된 롤업을 확인해야 할 수 있습니다.

- MABS 데이터베이스 버전을 확인 하려면 다음 단계를 수행 합니다.

    1. 쿼리를 실행 하려면 SQL Management Studio를 연 다음 MABS 데이터베이스를 실행 하는 SQL 인스턴스에 연결 합니다.

    2. MABS 데이터베이스를 선택한 다음 새 쿼리를 시작 합니다.

    3. 다음 SQL 쿼리를 쿼리 창에 붙여넣고 실행합니다.

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    쿼리 결과에 아무 것도 반환 되지 않은 경우 또는 MABS 서버가 이전 버전에서 업그레이드 되었지만 이후 새 업데이트 롤업이 설치 되지 않은 경우 MABS의 기본 설치에는 주 버전 (부 버전)에 대 한 항목이 없습니다. 업데이트 롤업에 연결 된 MABS 버전을 확인 하려면 [MABS의 빌드 번호 목록](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)을 참조 하세요.

### <a name="back-up-to-azure"></a>Azure에 백업

1. 시작 하기 전에 MABS 백업이 포함 된 복구 지점을 알 수 있도록 MABS 복제본 볼륨 탑재 지점 경로를 검색 하는 스크립트를 실행 해야 합니다. Azure Backup을 사용하여 초기 복제 후 이 작업을 수행합니다. 스크립트에서을 `dplsqlservername%` MABS 데이터베이스를 호스트 하는 SQL Server 인스턴스의 이름으로 바꿉니다.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Azure Recovery Services 에이전트가 설치 되어 있고 MABS 서버가 Azure Backup 자격 증명 모음에 등록 되었을 때 지정 된 암호가 있는지 확인 합니다. 백업을 복원하려면 이 암호가 있어야 합니다.

2. Azure Backup 자격 증명 모음을 만들고 Azure Backup 에이전트 설치 파일 및 자격 증명 모음을 다운로드합니다. 설치 파일을 실행 하 여 MABS 서버에 에이전트를 설치 하 고 자격 증명 모음 자격 증명을 사용 하 여 자격 증명 모음에 MABS 서버를 등록 합니다. [자세한 정보를 알아보세요](backup-azure-microsoft-azure-backup.md).

3. 자격 증명 모음이 구성 된 후 mabs 데이터베이스를 포함 하는 MABS 보호 그룹을 설정 합니다. 디스크 및 Azure에 백업 하려면 선택 합니다.

#### <a name="recover-the-mabs-database-from-azure"></a>Azure에서 MABS 데이터베이스 복구

다음과 같이 Azure Backup 자격 증명 모음에 등록 된 MABS 서버를 사용 하 여 Azure에서 데이터베이스를 복구할 수 있습니다.

1. Mabs 콘솔에서 **복구**  >  **외부 mabs 추가**를 선택 합니다.

2. 자격 증명 모음 자격 증명을 제공 합니다 (Azure Backup 자격 증명 모음에서 다운로드). 자격 증명은 2일간만 유효합니다.

3. **복구를 위한 외부 Mabs 선택**에서 데이터베이스를 복구 하려는 mabs 서버를 선택 하 고 암호화 암호를 입력 한 다음 확인을 선택 **합니다.**

4. 사용 가능한 지점 목록에서 사용할 복구 지점을 선택합니다. 로컬 MABS 뷰로 돌아가려면 **EXTERNAL Mabs 지우기** 를 선택 합니다.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Mabs 저장소 풀에 MABS 데이터베이스 백업

> [!NOTE]  
> 이 옵션은 Modern Backup Storage에서 MABS에 적용 됩니다.

1. Mabs 콘솔에서 **보호**  >  **보호 그룹 만들기**를 선택 합니다.
2. **보호 그룹 종류 선택** 페이지에서 **서버**를 선택합니다.
3. **그룹 구성원 선택** 페이지에서 **DPM 데이터베이스**를 선택 합니다. MABS 서버를 확장 하 고 DPMDB를 선택 합니다.
4. **데이터 보호 방법 선택** 페이지에서 **디스크를 사용한 단기 보호 사용**을 선택합니다. 단기 보호 정책 옵션을 지정합니다.
5. MABS 데이터베이스의 초기 복제 후 다음 SQL 스크립트를 실행 합니다.

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>MABS 데이터베이스 복구

동일한 DB를 사용 하 여 MABS를 다시 생성 하려면 먼저 MABS 데이터베이스를 복구 하 고 새로 설치 된 MABS와 동기화 해야 합니다.

#### <a name="use-the-following-steps"></a>다음 단계를 사용 합니다.

1. 관리 명령 프롬프트를 열고를 실행 `psexec.exe -s powershell.exe` 하 여 시스템 컨텍스트에서 PowerShell 창을 시작 합니다.
2. 데이터베이스를 복구할 위치를 결정합니다.

#### <a name="to-copy-the-database-from-the-last-backup"></a>마지막 백업에서 데이터베이스를 복사하려면

1. 복제본 VHD 경로 `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>` 로 이동
2. 명령을 사용 하 여 disk0를 탑재 **합니다** . `mount-vhd disk0.vhdx`
3. 복제본 VHD가 탑재 되 면를 사용 `mountvol.exe` 하 여 SQL 스크립트 출력의 실제 복제본 ID를 사용 하 여 복제본 볼륨에 드라이브 문자를 할당 합니다. 예: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>이전 복구 지점에서 데이터베이스를 복사하려면

1. DPMDB container 디렉터리로 이동  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` 합니다. MABS DB에 대해 수행 되는 해당 복구 지점이 있는 일부 고유 GUID 식별자가 포함 된 여러 디렉터리가 표시 됩니다. 다른 디렉터리는 PIT/recovery 지점을 나타냅니다.
2. 모든 PIT vhd 경로 (예:)로 이동 하 `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` 고 명령을 사용 하 여 **disk0** 를 탑재 합니다 `mount-vhd disk0.vhdx` .
3. 복제본 VHD가 탑재 되 면를 사용 `mountvol.exe` 하 여 SQL 스크립트 출력의 실제 복제본 ID를 사용 하 여 복제본 볼륨에 드라이브 문자를 할당 합니다. 예: `mountvol X: \?\Volume{}\`

   위의 단계에서 각도 중괄호와 함께 표시 되는 모든 용어는 자리 표시자입니다. 다음과 같이 적절 한 값으로 바꿉니다.
   - SQL 스크립트 출력의 **Refsvolume** -액세스 경로
   - **MABSSERVER FQDN** -mabs 서버의 정규화 된 이름
   - 실제 **replicaid** -SQL 스크립트의 실제 복제본 ID입니다.
   - **PITId** -컨테이너 디렉터리의 실제 복제본 ID 이외의 GUID 식별자입니다.
4. 다른 관리 명령 프롬프트를 열고를 실행 `psexec.exe -s cmd.exe` 하 여 시스템 컨텍스트에서 명령 프롬프트를 시작 합니다.
5. 디렉터리를 X: 드라이브로 변경 하 고 MABS 데이터베이스 파일의 위치로 이동 합니다.
6. 쉽게 복원할 수 있는 위치에 복사합니다. 복사한 후 psexec cmd 창을 종료합니다.
7. 1 단계에서 연 psexec PowerShell 창으로 이동 하 여 VHDX 경로로 이동 하 고 명령을 사용 하 여 VHDX를 분리 합니다 `dismount-vhd disk0.vhdx` .
8. MABS 서버를 다시 설치한 후 명령을 실행 하 여 복원 된 DPMDB를 사용 하 여 MABS 서버에 연결할 수 있습니다 `DPMSYNC-RESTOREDB` .
9. `DPMSYNC-SYNC`한 번 실행 `DPMSYNC-RESTOREDB` 이 완료 되었습니다.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>MABS 저장소 풀을 백업 하 여 데이터베이스 백업

> [!NOTE]
> 이 옵션은 레거시 저장소를 사용 하는 MABS에 적용 됩니다.

시작 하기 전에 MABS 백업이 포함 된 복구 지점을 알 수 있도록 MABS 복제본 볼륨 탑재 지점 경로를 검색 하는 스크립트를 실행 해야 합니다. Azure Backup을 사용하여 초기 복제 후 이 작업을 수행합니다. 스크립트에서을 `dplsqlservername%` MABS 데이터베이스를 호스트 하는 SQL Server 인스턴스의 이름으로 바꿉니다.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Mabs 콘솔에서 **보호**  >  **보호 그룹 만들기**를 선택 합니다.

2. **보호 그룹 종류 선택** 페이지에서 **서버**를 선택합니다.

3. **그룹 구성원 선택** 페이지에서 mabs 데이터베이스를 선택 합니다. MABS 서버 항목을 확장 하 고 **DPMDB**를 선택 합니다.

4. **데이터 보호 방법 선택** 페이지에서 **디스크를 사용 하는 단기 보호 사용**을 선택 합니다. 단기 보호 정책 옵션을 지정합니다. MABS 데이터베이스에 대해 2 주의 보존 범위를 권장 합니다.

#### <a name="recover-the-database"></a>데이터베이스 복구

MABS 서버가 여전히 작동 하 고 저장소 풀이 손상 된 경우 (예: MABS 서비스 또는 콘솔의 문제) 다음과 같이 복제본 볼륨 또는 섀도 복사본에서 데이터베이스를 복사 합니다.

1. 데이터베이스를 복구하려는 시간을 결정합니다.

    - MABS 복제본 볼륨에서 직접 가져온 마지막 백업에서 데이터베이스를 복사 하려는 경우 **mountvol.exe** 를 사용 하 여 SQL 스크립트 출력의 GUID를 사용 하 여 복제본 볼륨에 드라이브 문자를 할당 합니다. 예: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - 이전 복구 지점 (섀도 복사)에서 데이터베이스를 복사 하려는 경우 SQL 스크립트 출력의 볼륨 GUID를 사용 하 여 복제본에 대 한 모든 섀도 복사본을 나열 해야 합니다. 이 명령은 해당 볼륨에 대 한 섀도 복사본을 나열 `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 합니다. 복구 하려는 만든 시간 및 섀도 복사본 ID를 확인 합니다.

2. 그런 다음 **diskshadow.exe** 를 사용 하 여 섀도 복사본을 사용 하지 않는 드라이브 문자 X:에 탑재 하 고 섀도 복사본 ID를 사용 하 여 데이터베이스 파일을 복사할 수 있습니다.

3. 관리 명령 프롬프트를 열고를 실행 `psexec.exe -s cmd.exe` 하 여 시스템 컨텍스트에서 명령 프롬프트를 시작 합니다. 그러면 복제본 볼륨 (X:)으로 이동할 수 있는 권한이 부여 됩니다. 파일을 복사 합니다.

4. CD를 X: 드라이브로 이동 하 고 MABS 데이터베이스 파일의 위치로 이동 합니다. 쉽게 복원할 수 있는 위치에 복사합니다. 복사가 완료 되 면 psexec cmd 창이 실행 되 고 **diskshadow.exe** 를 실행 하 고 X: 볼륨을 숨깁니다 합니다.

5. 이제 SQL Management Studio를 사용 하거나 **DPMSYNC \- RESTOREDB**를 실행 하 여 데이터베이스 파일을 복원할 수 있습니다.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>네이티브 SQL Server 백업을 사용하여 로컬 디스크에 백업

MABS와는 독립적으로 네이티브 SQL Server 백업을 사용 하 여 MABS 데이터베이스를 로컬 디스크에 백업할 수 있습니다.

- SQL Server 백업의 [개요](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)를 가져옵니다.

- 클라우드로 SQL Server를 백업하는 방법에 대해 [알아보세요](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

## <a name="back-up-to-a-share-protected-by-mabs"></a>MABS로 보호 되는 공유에 백업

이 백업 옵션은 native SQL을 사용 하 여 MABS 데이터베이스를 공유에 백업 하 고, MABS를 사용 하 여 공유를 보호 하 고, Windows VSS 이전 버전을 사용 하 여 복원을 용이 하 게 합니다.

### <a name="before-you-start"></a>시작하기 전에

1. SQL Server에서 백업의 단일 복사본을 저장할 공간이 충분 한 드라이브에 폴더를 만듭니다. 예: `C:\MABSBACKUP`

1. 폴더를 공유합니다. 예를 들어 `C:\MABSBACKUP` 폴더를 *DPMBACKUP*으로 공유 합니다.

1. 아래의 OSQL 명령을 복사 하 여 메모장에 붙여넣고 이라는 파일에 저장 `C:\MABSACKUP\bkupdb.cmd` 합니다. .Txt 확장명이 없는지 확인 합니다. MABS 서버에서 사용 하는 인스턴스와 DPMDB 이름에 맞게 SQL_Instance_name 및 DPMDB_NAME를 수정 합니다.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. 메모장을 사용 하 여 **ScriptingConfig.xml** `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` mabs 서버의 폴더 아래에 있는ScriptingConfig.xml파일을 엽니다.

1. **ScriptingConfig.xml** 를 수정 하 고 **DATASOURCENAME =** 를 dpmdbbackup 폴더/공유가 포함 된 드라이브 문자로 변경 합니다. PreBackupScript 항목을 3 단계에서 저장 한 **bkupdb** 의 전체 경로 및 이름으로 변경 합니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. **ScriptingConfig.xml**에 대 한 변경 내용을 저장 합니다.

1. MABS를 사용 하 여 C:\MABSBACKUP 폴더 또는 공유를 보호 하 `\sqlservername\MABSBACKUP` 고 초기 복제본이 생성 될 때까지 기다립니다. 백업 전 스크립트를 실행 한 결과로 C:\MABSBACKUP 폴더에 **dpmdb** 가 있어야 하며,이는 mabs 복제본에 복사 됩니다.

1. 셀프 서비스 복구를 사용 하지 않는 경우 복제본에서 MABSBACKUP 폴더를 공유 하려면 몇 가지 추가 단계가 필요 합니다.

    1. MABS 콘솔 > **보호**에서 MABSBACKUP 데이터 원본을 찾아 선택 합니다. 세부 정보 섹션에서 복제본 경로에 대 한 링크에서 **세부 정보를 보려면 클릭** 하십시오 .를 선택 하 고 메모장에 해당 경로를 복사 합니다. 원본 경로를 제거하고 대상 경로를 유지합니다. 경로는 다음과 유사 하 게 표시 됩니다 `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. 공유 이름 **MABSSERVERNAME-DPMDB**을 사용 하 여 공유를 해당 경로로 만듭니다. 관리 명령 프롬프트에서 아래의 Net Share 명령을 사용할 수 있습니다.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>백업 구성

SQL Server 기본 백업을 사용 하 여 다른 SQL Server 데이터베이스와 마찬가지로 MABS 데이터베이스를 백업할 수 있습니다.

- SQL Server 백업의 [개요](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)를 가져옵니다.

- 클라우드로 SQL Server를 백업하는 방법에 대해 [알아보세요](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

### <a name="recover-the-mabs-database"></a>MABS 데이터베이스 복구

1. `\\MABSServer\MABSSERVERNAME-dpmdb`모든 Windows 컴퓨터에서 탐색기를 사용 하 여 공유에 연결 합니다.

2. **Dpmdb** 파일을 마우스 오른쪽 단추로 클릭 하 여 속성을 봅니다. **이전 버전** 탭에 선택 및 복사할 수 있는 모든 백업이 있습니다. 또한 쉽게 액세스할 수 있는 C:\MABSBACKUP 폴더에 아직 마지막 백업이 있습니다.

3. SAN 연결 된 MABS 저장소 풀 디스크를 복제 볼륨에서 읽을 수 있도록 다른 서버로 이동 하거나 로컬로 연결 된 디스크를 읽을 수 있도록 Windows를 다시 설치 해야 하는 경우, 데이터베이스 백업을 보유 하 고 있는 볼륨을 알 수 있도록 MABS 복제본 볼륨 탑재 지점 경로 또는 볼륨 GUID를 미리 알고 있어야 합니다. 초기 보호 후 복원할 필요성이 생기기 전에 언제든지 아래 SQL 스크립트를 사용하여 해당 정보를 추출할 수 있습니다. 를 `%dpmsqlservername%` 데이터베이스를 호스팅하는 SQL Server 이름으로 바꿉니다.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. MABS 저장소 풀 디스크를 이동 하거나 MABS 서버를 다시 작성 한 후 복구 해야 하는 경우:

    1. 볼륨 GUID가 있으므로 다른 Windows 서버에 볼륨을 탑재 하거나 MABS 서버를 다시 작성 한 후에는 **mountvol.exe** 를 사용 하 여 SQL 스크립트 출력의 볼륨 GUID를 사용 하 여 드라이브 문자를 할당 해야 `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` 합니다.

    2. 드라이브 문자 및 폴더 구조를 나타내는 복제본 경로의 일부를 사용 하 여 복제본 볼륨의 MABSBACKUP 폴더를 다시 공유 합니다.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. `\\SERVERNAME\MABSSERVERNAME-dpmdb`모든 Windows 컴퓨터에서 탐색기를 사용 하 여 공유에 연결 합니다.

    4. **Dpmdb** 파일을 마우스 오른쪽 단추로 클릭 하 여 속성을 확인 합니다. **이전 버전** 탭에 선택 및 복사할 수 있는 모든 백업이 있습니다.

## <a name="using-dpmsync"></a>DPMSync 사용

**DpmSync** 는 저장소 풀의 디스크 상태와 설치 된 보호 에이전트로 mabs 데이터베이스를 동기화 하는 데 사용할 수 있는 명령줄 도구입니다. DpmSync는 MABS 데이터베이스를 복원 하 고, MABS 데이터베이스를 저장소 풀의 복제본과 동기화 하 고, 보고서 데이터베이스를 복원 하 고, 누락 된 복제본을 다시 할당 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수      | 설명    |
|----------------|-----------------------------|
| **-RestoreDb**                       | 지정 된 위치에서 MABS 데이터베이스를 복원 합니다.|
| **-Sync**                            | 복원된 데이터베이스를 동기화합니다. 데이터베이스를 복원한 후에는 **DpmSync-Sync** 를 실행 해야 합니다. **DpmSync – Sync**를 실행 한 후에는 일부 복제본이 아직 누락 된 것으로 표시 될 수 있습니다. |
| **-DbLoc** *위치*                | MABS 데이터베이스의 백업 위치를 식별 합니다.|
| **-InstanceName** <br/>*서버 \ 인스턴스*     | DPMDB를 복원해야 하는 인스턴스입니다.|
| **-ReallocateReplica**         | 누락된 복제본 볼륨을 동기화하지 않고 모두 재할당합니다. |
| **-DataCopied**                      | 새로 할당된 복제본 볼륨에 데이터 로드를 완료했음을 나타냅니다. 이는 클라이언트 컴퓨터에만 해당됩니다. |

**예 1:** Mabs 서버의 로컬 백업 미디어에서 MABS 데이터베이스를 복원 하려면 다음 명령을 실행 합니다.

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

MABS 데이터베이스를 복원한 후 데이터베이스를 동기화 하려면 다음 명령을 실행 합니다.

```cmd
DpmSync -Sync
```

MABS 데이터베이스를 복원 및 동기화 한 후 복제본을 복원 하기 전에 다음 명령을 실행 하 여 복제본에 대 한 디스크 공간을 다시 할당 합니다.

```cmd
DpmSync -ReallocateReplica
```

**예 2:** 원격 데이터베이스에서 MABS 데이터베이스를 복원 하려면 원격 컴퓨터에서 다음 명령을 실행 합니다.

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

MABS 데이터베이스를 복원한 후 데이터베이스를 동기화 하려면 MABS 서버에서 다음 명령을 실행 합니다.

```cmd
DpmSync -Sync
```

MABS 데이터베이스를 복원 및 동기화 한 후 복제본을 복원 하기 전에 MABS 서버에서 다음 명령을 실행 하 여 복제본에 대 한 디스크 공간을 다시 할당 합니다.

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>다음 단계

- [MABS 지원 매트릭스](backup-support-matrix-mabs-dpm.md)
- [MABS FAQ](backup-azure-dpm-azure-server-faq.md)
