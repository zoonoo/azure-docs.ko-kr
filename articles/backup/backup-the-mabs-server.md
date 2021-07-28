---
title: MABS 서버 백업
description: MABS(Microsoft Azure Backup Server)를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519122"
---
# <a name="back-up-the-mabs-server"></a>MABS 서버 백업

MABS(Microsoft Azure Backup Server)가 실패한 경우 데이터를 복구할 수 있도록 하려면 MABS 서버 백업 전략이 필요합니다. 백업되지 않은 경우 오류가 발생한 후 수동으로 다시 빌드해야 하며 디스크 기반 복구 지점은 복구할 수 없습니다. MABS 데이터베이스를 백업하여 MABS 서버를 백업할 수 있습니다.

## <a name="back-up-the-mabs-database"></a>MABS 데이터베이스 백업

MABS 백업 전략의 일환으로 MABS 데이터베이스를 백업해야 합니다. MABS 데이터베이스의 이름은 DPMDB입니다. 이 데이터베이스에는 MABS 백업에 대한 데이터와 MABS 구성이 함께 포함되어 있습니다. 재해가 발생한 경우 데이터베이스의 최신 백업을 사용하여 MABS 서버의 기능 대부분을 다시 빌드할 수 있습니다. 데이터베이스를 복원할 수 있는 경우 테이프 기반 백업에 액세스할 수 있으며 모든 보호 그룹 설정 및 백업 일정이 유지됩니다. MABS 스토리지 풀 디스크가 가동 중단의 영향을 받지 않은 경우 다시 빌드한 후 디스크 기반 백업을 사용할 수도 있습니다. 다양한 방법을 사용하여 데이터베이스를 백업할 수 있습니다.

|데이터베이스 백업 방법|장점|단점|
|--------------------------|--------------|-----------------|
|[Azure에 백업](#back-up-to-azure)|<li>MABS에서 간편하게 구성 및 모니터링합니다.<li>백업 데이터베이스 파일의 여러 위치.<li>클라우드 스토리지가 재해 복구를 위한 강력한 솔루션을 제공합니다.<li>데이터베이스를 위한 매우 안전한 스토리지입니다.<li>120개의 온라인 복구 지점을 지원합니다.|<li>Azure 계정 및 추가 MABS 구성이 필요합니다. Azure 스토리지에 대한 일부 비용이 발생합니다.<li> Azure 백업 자격 증명 모음에 저장된 MABS 백업에 대한 액세스 권한을 얻으려면 Azure 에이전트가 있는 Windows Server 기반 시스템의 지원되는 버전이 필요합니다. 다른 MABS 서버를 사용할 수는 없습니다.<li>데이터베이스가 로컬로 호스팅되고 보조 보호를 사용하도록 설정하려는 경우 옵션이 아닙니다. <li>추가 준비 및 복구 시간이 발생합니다.|
|[MABS 스토리지 풀을 백업하여 데이터베이스 백업](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>간단하게 구성하고 모니터링할 수 있습니다.<li>백업은 MABS 스토리지 풀 디스크에 보관되며 로컬로 쉽게 액세스할 수 있습니다.<li>MABS 예약 백업은 512개의 고속 전체 백업을 지원합니다. 매 시간 백업하는 경우 21일의 전체 보호가 가능하게 됩니다.|<li>재해 복구에는 적합한 옵션이 아닙니다. 온라인 상태일 때 복구는 MABS 서버 또는 스토리지 풀 디스크에 오류가 발생하는 경우 예상대로 작동하지 않을 수 있습니다.<li>데이터베이스가 로컬로 호스팅되고 보조 보호를 사용하도록 설정하려는 경우 옵션이 아닙니다. <li>MABS 서비스 또는 콘솔이 실행되거나 작동되지 않는 경우 복구 지점에 대한 액세스를 얻기 위해서는 일부 준비 및 특별 단계가 필요합니다.|
|[네이티브 SQL Server 백업을 사용하여 로컬 디스크에 백업](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>SQL Server에서 기본 제공됩니다.<li>백업은 쉽게 액세스할 수 있는 로컬 디스크에 보관됩니다.<li>원하는 만큼 실행되도록 예약할 수 있습니다.<li>MABS에 대해 완전히 독립적입니다.<li>백업 파일 정리를 예약할 수 있습니다.|<li>백업을 원격 위치에 복사하지 않는 한 좋은 재해 복구 옵션은 아닙니다.<li>백업의 보존 및 빈도를 제한할 수 있도록 로컬 스토리지가 필요합니다.|
|[원시 SQL 백업 및 MABS 보호를 사용하여 MABS가 보호하는 공유에 백업](#back-up-to-a-share-protected-by-mabs)|<li>MABS에서 쉽게 모니터링됩니다.<li>백업 데이터베이스 파일의 여러 위치.<li>네트워크의 모든 Windows 컴퓨터에서 쉽게 액세스할 수 있습니다.<li>잠재적으로 가장 빠른 복구 방법입니다.|<li>64개의 복구 지점만 지원합니다.<li>사이트 재해 복구에는 적합한 옵션이 아닙니다. MABS 서버 또는 MABS 스토리지 풀 디스크 오류로 복구 성능이 저하될 수 있습니다.<li>MABS DB가 로컬로 호스팅되고 보조 보호를 사용하도록 설정하려는 경우 옵션이 아닙니다. <li>구성 및 테스트에는 일부 추가적인 준비가 필요합니다.<li>MABS 서버 자체를 중단해야 하지만 MABS 스토리지 풀 디스크는 괜찮은 경우 일부 추가 준비 및 복구 시간이 필요합니다.|

- MABS 보호 그룹을 사용하여 백업하는 경우 데이터베이스에 대한 고유 보호 그룹을 사용하는 것이 좋습니다.

    > [!NOTE]
    > 복원을 위해서는 MABS 데이터베이스로 복원하려는 MABS 설치가 MABS 데이터베이스 버전 자체와 일치해야 합니다.  예를 들어 복구하려는 데이터베이스가 업데이트 롤업 1이 설치된 MABS V3에서 가져온 것이면 MABS 서버는 업데이트 롤업 1과 동일한 버전을 실행해야 합니다. 즉, 데이터베이스를 복원하기 전에 MABS를 제거했다가 호환되는 버전으로 다시 설치해야 할 수 있습니다.  데이터베이스 버전을 확인하려면 임시 데이터베이스 이름에 수동으로 탑재한 다음 해당 데이터베이스에 대해 SQL 쿼리를 실행하여 주 및 부 버전을 기준으로 마지막으로 설치된 롤업을 확인해야 할 수 있습니다.

- MABS 데이터베이스 버전을 확인하려면 다음 단계를 따르세요.

    1. 쿼리를 실행하려면 SQL Management Studio를 연 다음, MABS 데이터베이스를 실행하는 SQL 인스턴스에 연결합니다.

    2. MABS 데이터베이스를 선택한 다음, 새 쿼리를 시작합니다.

    3. 다음 SQL 쿼리를 쿼리 창에 붙여넣고 실행합니다.

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    쿼리 결과에 아무 것도 반환되지 않거나 MABS 서버가 이전 버전에서 업그레이드되었지만 그 이후로 새 업데이트 롤업이 설치되지 않은 경우 MABS의 기본 설치에 대한 주 버전, 부 버전 항목이 없습니다. 업데이트 롤업과 관련된 MABS 버전을 확인하려면 [MABS 빌드 번호 목록](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)을 참조하세요.

### <a name="back-up-to-azure"></a>Azure에 백업

1. 시작하기 전에 MABS 백업이 포함된 복구 지점을 알 수 있도록 MABS 복제 볼륨 탑재 지점 경로를 검색하는 스크립트를 실행해야 합니다. Azure Backup을 사용하여 초기 복제 후 이 작업을 수행합니다. 스크립트에서 MABS 데이터베이스를 호스팅하는 SQL Server 인스턴스의 이름으로 `dplsqlservername%`를 바꿉니다.

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

    Azure 복구 서비스 에이전트가 설치되고 MABS 서버가 Azure 백업 자격 증명 모음에 등록되었을 때 지정된 암호가 있는지 확인합니다. 백업을 복원하려면 이 암호가 있어야 합니다.

2. Azure Backup 자격 증명 모음을 만들고 Azure Backup 에이전트 설치 파일 및 자격 증명 모음을 다운로드합니다. 설치 파일을 실행하여 MABS 서버에 에이전트를 설치하고 자격 증명 모음을 사용하여 자격 증명 모음에 MABS 서버를 등록합니다. [자세한 정보를 알아보세요](backup-azure-microsoft-azure-backup.md).

3. 자격 증명 모음이 구성된 후 MABS 데이터베이스를 포함하는 MABS 보호 그룹을 설정합니다. 디스크 및 Azure에 백업하려면 선택합니다.

#### <a name="recover-the-mabs-database-from-azure"></a>Azure에서 MABS 데이터베이스를 복구합니다.

다음과 같이 Azure Backup 자격 증명 모음에 등록된 MABS 서버를 사용하여 Azure에서 데이터베이스를 복구할 수 있습니다.

1. MABS 콘솔에서 **복구** > **외부 MABS 추가** 를 선택합니다.

2. 자격 증명 모음을 제공합니다(Azure Backup 자격 증명 모음에서 다운로드). 자격 증명은 2일간만 유효합니다.

3. **복구용 외부 MABS 선택** 에서 데이터베이스를 복구할 MABS 서버를 선택하고 암호화 암호를 입력한 다음, **확인** 을 선택합니다.

4. 사용 가능한 지점 목록에서 사용할 복구 지점을 선택합니다. 로컬 MABS 보기로 돌아가려면 **외부 MABS 지우기** 를 선택합니다.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>MABS 데이터베이스를 MABS 스토리지 풀에 백업

> [!NOTE]  
> 이 옵션은 Modern Backup Storage가 있는 MABS에 적용됩니다.

1. MABS 콘솔에서 **보호** > **보호 그룹** 을 선택합니다.
2. **보호 그룹 종류 선택** 페이지에서 **서버** 를 선택합니다.
3. **그룹 구성원 선택** 페이지에서 **DPM 데이터베이스** 를 선택합니다. MABS 서버를 확장하고 DPMDB를 선택합니다.
4. **데이터 보호 방법 선택** 페이지에서 **디스크를 사용한 단기 보호 사용** 을 선택합니다. 단기 보호 정책 옵션을 지정합니다.
5. MABS 데이터베이스의 초기 복제가 완료되면 다음 SQL 스크립트를 실행합니다.

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

동일한 DB로 MABS를 다시 구성하려면 먼저 MABS 데이터베이스를 복구하고 새로 설치된 MABS와 동기화해야 합니다.

#### <a name="use-the-following-steps"></a>다음 단계 사용

1. 관리 명령 프롬프트를 열고 `psexec.exe -s powershell.exe`를 실행하여 시스템 컨텍스트에서 PowerShell 창을 시작합니다.
2. 데이터베이스를 복구할 위치를 결정합니다.

#### <a name="to-copy-the-database-from-the-last-backup"></a>마지막 백업에서 데이터베이스를 복사하려면

1. 복제본 VHD 경로 `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>` 로 이동
2. `mount-vhd disk0.vhdx` 명령을 사용하여 해당 디스크에 있는 **disk0.vhdx** 를 탑재합니다.
3. 복제본 VHD가 탑재되면 `mountvol.exe`를 사용하여 SQL 스크립트 출력에서 실제 복제본 ID를 통해 복제 볼륨에 드라이브 문자를 할당합니다. `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>이전 복구 지점에서 데이터베이스를 복사하려면

1. DPMDB 컨테이너 디렉터리 `\<MABSServer FQDN\>\<PhysicalReplicaId\>`로 이동합니다. MABS DB에 대해 사용된 해당 복구 지점 아래에 몇 가지 고유한 GUID 식별자가 있는 여러 디렉터리가 표시됩니다. 다른 디렉터리는 PIT/복구 지점을 나타냅니다.
2. PIT vhd 경로(예: `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>`)로 이동하고 `mount-vhd disk0.vhdx` 명령을 사용하여 그 안에 있는 **disk0.vhdx** 를 탑재합니다.
3. 복제본 VHD가 탑재되면 `mountvol.exe`를 사용하여 SQL 스크립트 출력에서 실제 복제본 ID를 통해 복제 볼륨에 드라이브 문자를 할당합니다. `mountvol X: \?\Volume{}\`

   위 단계에서 꺾쇠 괄호로 표시되는 모든 용어는 자리 표시자입니다. 다음과 같이 적절한 값으로 바꿉니다.
   - **ReFSVolume** - SQL 스크립트 출력의 액세스 경로
   - **MABSServer FQDN** - MABS 서버의 정규화된 이름
   - **PhysicalReplicaId** - SQL 스크립트의 실제 복제본 ID
   - **PITId** - 컨테이너 디렉터리의 실제 복제본 ID가 아닌 GUID 식별자입니다.
4. 다른 관리 명령 프롬프트를 열고 `psexec.exe -s cmd.exe`를 실행하여 시스템 컨텍스트에서 명령 프롬프트를 시작합니다.
5. 디렉터리를 X: 드라이브로 변경하고 MABS 데이터베이스 파일 위치로 이동합니다.
6. 쉽게 복원할 수 있는 위치에 복사합니다. 복사한 후 psexec cmd 창을 종료합니다.
7. 1단계에서 연 psexec PowerShell 창으로 이동하여 VHDX 경로를 탐색하고 `dismount-vhd disk0.vhdx` 명령을 사용하여 VHDX를 분리합니다.
8. MABS 서버를 다시 설치한 후 `DPMSYNC-RESTOREDB` 명령을 실행하여 복원된 DPMDB를 통해 MABS 서버에 연결할 수 있습니다.
9. `DPMSYNC-RESTOREDB`가 완료되면 `DPMSYNC-SYNC`를 실행합니다.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>MABS 스토리지 풀을 백업하여 데이터베이스 백업

> [!NOTE]
> 이 옵션은 레거시 스토리지가 있는 MABS에 적용됩니다.

시작하기 전에 MABS 백업이 포함된 복구 지점을 알 수 있도록 MABS 복제 볼륨 탑재 지점 경로를 검색하는 스크립트를 실행해야 합니다. Azure Backup을 사용하여 초기 복제 후 이 작업을 수행합니다. 스크립트에서 MABS 데이터베이스를 호스팅하는 SQL Server 인스턴스의 이름으로 `dplsqlservername%`를 바꿉니다.

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

1. MABS 콘솔에서 **보호** > **보호 그룹** 을 선택합니다.

2. **보호 그룹 종류 선택** 페이지에서 **서버** 를 선택합니다.

3. **그룹 구성원 선택** 페이지에서 MABS 데이터베이스를 선택합니다. MABS 서버 항목을 확장하고 **DPMDB** 를 선택합니다.

4. **데이터 보호 방법 선택** 페이지에서 **디스크를 사용한 단기 보호 사용** 을 선택합니다. 단기 보호 정책 옵션을 지정합니다. MABS 데이터베이스에 대해 2주의 보존 범위를 지정하는 것이 좋습니다.

#### <a name="recover-the-database"></a>데이터베이스 복구

MABS 서버가 계속 작동하고 스토리지 풀이 손상되지 않은 경우(예: MABS 서비스 또는 콘솔 문제) 다음과 같이 복제 볼륨 또는 섀도 복사본에서 데이터베이스를 복사합니다.

1. 데이터베이스를 복구하려는 시간을 결정합니다.

    - MABS 복제본 볼륨에서 직접 가져온 마지막 백업에서 데이터베이스를 복사하려는 경우 SQL 스크립트 출력의 GUID를 사용하여 복제본 볼륨에 드라이브 문자를 할당하려면 **mountvol.exe** 를 사용합니다. `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - 이전 복구 지점(섀도 복사)에서 데이터베이스를 복사하려는 경우 SQL 스크립트 출력의 볼륨 GUID를 사용하여 복제본에 대한 모든 섀도 복사본을 나열해야 합니다. 이 명령은 해당 볼륨에 대한 섀도 복사본을 나열합니다(`C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\`). 만든 시간 및 복구하려는 섀도 복사본 ID를 확인합니다.

2. 그런 다음, **diskshadow.exe** 를 사용하여 섀도 복사본을 사용하지 않는 드라이브 문자 X:에 탑재하고 섀도 복사본 ID를 사용하여 데이터베이스 파일을 복사할 수 있습니다.

3. 복제본 볼륨(X:)을 탐색하고 파일을 복사할 수 있는 권한이 있으므로 관리 명령 프롬프트를 열고 `psexec.exe -s cmd.exe`를 실행하여 시스템 컨텍스트에서 명령 프롬프트를 시작합니다.

4. CD에서 X: 드라이브로 그리고 MABS 데이터베이스 파일 위치로 이동합니다. 쉽게 복원할 수 있는 위치에 복사합니다. 복사가 완료된 후 psexec cmd 창을 종료하고 **diskshadow.exe** 를 실행하여 x: 볼륨을 숨깁니다.

5. 이제 **DPMSYNC\-RESTOREDB** 를 실행하거나 SQL Management Studio를 사용하여 데이터베이스 파일을 복원할 수 있습니다.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>네이티브 SQL Server 백업을 사용하여 로컬 디스크에 백업

MABS에 관계 없이 원시 SQL Server 백업을 사용하여 로컬 디스크에 MABS 데이터베이스를 백업할 수 있습니다.

- SQL Server 백업의 [개요](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)를 가져옵니다.

- 클라우드로 SQL Server를 백업하는 방법에 대해 [알아보세요](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

## <a name="back-up-to-a-share-protected-by-mabs"></a>MABS로 보호되는 공유에 백업

이 백업 옵션은 원시 SQL을 사용하여 공유에 MABS 데이터베이스를 백업하고 MABS를 사용하여 공유를 보호하고 이전 버전 Windows VSS를 사용하여 복원을 용이하게 합니다.

### <a name="before-you-start"></a>시작하기 전에

1. SQL Server에서 백업의 단일 복사본을 저장할 공간이 충분한 드라이브에 폴더를 만듭니다. 예를 들면 `C:\MABSBACKUP`와 같습니다.

1. 폴더를 공유합니다. 예를 들어 `C:\MABSBACKUP` 폴더를 *DPMBACKUP* 으로 공유합니다.

1. 아래 OSQL 명령을 복사하여 메모장에 붙여넣고 `C:\MABSACKUP\bkupdb.cmd` 파일에 저장합니다. .txt 확장명이 없는지 확인합니다. SQL_Instance_name 및 DPMDB_NAME을 MABS 서버에서 사용하는 인스턴스 및 DPMDB 이름과 일치하도록 수정합니다.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. 메모장을 사용하여 MABS 서버의 `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` 폴더 아래에 있는 **ScriptingConfig.xml** 파일을 엽니다.

1. **ScriptingConfig.xml** 을 수정하고 DPMDBBACKUP 폴더/공유가 포함된 드라이브 문자가 되도록 **DataSourceName=** 을 변경합니다. 3단계에서 저장한 **bkupdb.cmd** 의 이름과 전체 경로로 PreBackupScript 항목을 변경합니다.

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

1. **ScriptingConfig.xml** 에 변경 내용을 저장합니다.

1. MABS를 사용하여 C:\MABSBACKUP 폴더 또는 `\sqlservername\MABSBACKUP` 공유를 보호하고 초기 복제본이 생성될 때까지 기다립니다. MABS 복제본에 복사된 사전 백업 스크립트 실행 결과로 C:\MABSBACKUP에 **dpmdb.bak** 가 있어야 합니다.

1. 셀프 서비스 복구를 사용하지 않는 경우 복제본에서 MABSBACKUP 폴더를 공유하려면 몇 가지 추가 단계가 필요합니다.

    1. MABS 콘솔 > **보호** 에서 MABSBACKUP 데이터 원본을 찾아 선택합니다. 세부 정보 섹션에서 복제본 경로에 대한 링크에서 **세부 정보를 보려면 클릭하세요** 를 선택하고 메모장에 해당 경로를 복사합니다. 원본 경로를 제거하고 대상 경로를 유지합니다. 경로는 다음과 유사해야 합니다. `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP`

    2. 공유 이름 **MABSSERVERNAME-DPMDB** 를 사용하여 공유를 해당 경로로 만듭니다. 관리 명령 프롬프트에서 아래의 Net Share 명령을 사용할 수 있습니다.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>백업 구성

SQL Server 원시 백업을 사용하는 다른 SQL Server 데이터베이스와 마찬가지로 MABS 데이터베이스를 백업할 수 있습니다.

- SQL Server 백업의 [개요](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)를 가져옵니다.

- 클라우드로 SQL Server를 백업하는 방법에 대해 [알아보세요](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

### <a name="recover-the-mabs-database"></a>MABS 데이터베이스 복구

1. 모든 Windows 컴퓨터에서 탐색기를 사용하여 `\\MABSServer\MABSSERVERNAME-dpmdb` 공유에 연결합니다.

2. 속성을 보려면 **dpmdb.bak** 파일을 마우스 오른쪽 단추로 클릭합니다. **이전 버전** 탭에 선택 및 복사할 수 있는 모든 백업이 있습니다. 또한 C:\MABSBACKUP 폴더에 최근 백업이 있으므로 쉽게 액세스할 수 있습니다.

3. SAN에 연결된 MABS 스토리지 풀 디스크를 복제본 볼륨에서 읽을 수 있도록 다른 서버로 이동하거나 로컬로 연결된 디스크를 읽으려면 Windows를 다시 설치해야 하는 경우, 데이터베이스 백업이 보관된 볼륨을 파악할 수 있도록 MABS 복제본 볼륨 탑재 지점 경로 또는 볼륨 GUID를 미리 알고 있어야 합니다. 초기 보호 후 복원할 필요성이 생기기 전에 언제든지 아래 SQL 스크립트를 사용하여 해당 정보를 추출할 수 있습니다. 데이터베이스를 호스팅하는 SQL Server의 이름으로 `%dpmsqlservername%`을 바꿉니다.

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

4. MABS 스토리지 풀 디스크를 이동하거나 MABS 서버를 다시 빌드한 후 복구해야 하는 경우 다음을 수행합니다.

    1. 볼륨 GUID가 있으므로 다른 Windows 서버에 또는 MABS 서버를 다시 빌드한 후 해당 볼륨을 탑재해야 하는 경우 SQL 스크립트 출력(`C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\`)의 볼륨 GUID를 사용하여 드라이브 문자를 할당하기 위해 **mountvol.exe** 를 사용합니다.

    2. 드라이브 문자 및 폴더 구조를 나타내는 복제본 경로의 일부분을 사용하여 복제 볼륨에서 MABSBACKUP 폴더를 다시 공유합니다.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. 모든 Windows 컴퓨터에서 탐색기를 사용하여 `\\SERVERNAME\MABSSERVERNAME-dpmdb` 공유에 연결합니다.

    4. 속성을 보려면 **dpmdb.bak** 파일을 마우스 오른쪽 단추로 클릭합니다. **이전 버전** 탭에 선택 및 복사할 수 있는 모든 백업이 있습니다.

## <a name="using-dpmsync"></a>DPMSync 사용

**DpmSync** 는 MABS 데이터베이스를 스토리지 풀의 디스크 상태 및 설치된 보호 에이전트와 동기화할 수 있는 명령줄 도구입니다. DpmSync는 MABS 데이터베이스를 복원하고, MABS 데이터베이스를 스토리지 풀의 복제본과 동기화하고, 보고서 데이터베이스를 복원하며 누락된 복제본을 재할당합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수      | 설명    |
|----------------|-----------------------------|
| **-RestoreDb**                       | MABS 데이터베이스를 지정된 위치에서 복원합니다.|
| **-Sync**                            | 복원된 데이터베이스를 동기화합니다. 데이터베이스를 복원한 후에는 **DpmSync –Sync** 를 실행해야 합니다. **DpmSync –Sync** 를 실행한 후에는 일부 복제본이 아직 누락된 것으로 표시될 수 있습니다. |
| **-DbLoc** *location*                | MABS 데이터베이스의 백업 위치를 식별합니다.|
| **-InstanceName** <br/>*server\instance*     | DPMDB를 복원해야 하는 인스턴스입니다.|
| **-ReallocateReplica**         | 누락된 복제본 볼륨을 동기화하지 않고 모두 재할당합니다. |
| **-DataCopied**                      | 새로 할당된 복제본 볼륨에 데이터 로드를 완료했음을 나타냅니다. 이는 클라이언트 컴퓨터에만 해당됩니다. |

**예 1:** MABS 서버의 로컬 백업 미디어에서 MABS 데이터베이스를 복원하려면 다음 명령을 실행합니다.

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

MABS 데이터베이스를 복원한 후 데이터베이스를 동기화하려면 다음 명령을 실행합니다.

```cmd
DpmSync -Sync
```

MABS 데이터베이스를 복원 및 동기화한 후 복제본을 복원하기 전에 다음 명령을 실행하여 복제본에 대한 디스크 공간을 재할당합니다.

```cmd
DpmSync -ReallocateReplica
```

**예 2:** 원격 데이터베이스에서 MABS 데이터베이스를 복원하려면 원격 컴퓨터에서 다음 명령을 실행합니다.

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

MABS 데이터베이스를 복원한 후 데이터베이스를 동기화하려면 MABS 서버에서 다음 명령을 실행합니다.

```cmd
DpmSync -Sync
```

MABS 데이터베이스를 복원 및 동기화한 후 복제본을 복원하기 전에 MABS 서버에서 다음 명령을 실행하여 복제본에 대한 디스크 공간을 재할당합니다.

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>다음 단계

- [MABS 지원 매트릭스](backup-support-matrix-mabs-dpm.md)
- [MABS FAQ](backup-azure-dpm-azure-server-faq.yml)