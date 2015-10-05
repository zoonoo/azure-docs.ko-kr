<properties
	pageTitle="Azure의 SharePoint 팜에 대한 DPM 보호 | Microsoft Azure"
	description="이 문서에서는 Azure의 SharePoint 팜에 대한 DPM 보호에 관한 개요를 제공합니다."
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/22/2015" ms.author="sammehta"; "jimpark"/>


# Azure에 SharePoint 팜 백업하기
System Center Data Protection Manager(DPM)을 사용하여 share point 팜 백업은 다른 데이터 소스 백업과 거의 비슷합니다. Azure은 일간, 주간, 월간 혹은 연간 백업 지점을 생성하는 백업 일정에 유연성을 제공하고 다양한 백업 지점에 관한 다양한 보전 정책 옵션을 제공합니다. DPM은 더 빠른 복구 시간 목표(Recovery Time Objectives A.K.A RTO)에 대한 로컬 디스크 복사본을 저장 및 비용 효율적인 장기간 보존 대상을 위해 AZure로 저장하는 기능을 제공합니다.

## SharePoint가 지원하는 버전 및 관련 보호 시나리오
DPM의 Azure 백업은 다음 시나리오들을 지원합니다.

| 워크로드 | 버전 | SharePoint 배포 | DPM 배포 유형 | DPM - System Center 2012 R2 | 보호 및 복구 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint는 물리적 서버 또는 하이퍼-V VmWare 가상 컴퓨터 <br> -------------- <br> SQL 무중단처럼 배포됩니다. | 물리적 서버 또는 온-프레미스 Hyper-v 가상 컴퓨터 | 업데이트 롤업 5에서 Azure로 백업을 지원 | SharePoint 팜 복구 보호: 디스크의 팜, 데이터베이스, 파일 또는 아이템 목록 및 팜 복구 그리고 Azure의 데이터베이스 |

## 시작하기 전에
Azure에 SharePoint 팜을 백업하기 전에 몇가지 확인할 사항이 있습니다.

### 필수 조건
진행 하기 전에 충돌된 워크로드를 보호하기 위해 Microsoft Azure 백업 사용에 관한 모든 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites)를 확인하세요. 필수 구성 요소는 백업 저장소 만들기, 보관 자격 증명 다운로드, Azure 백업 에이전트 설치 및 저장소에 서버 등록 등의 작업들을 다룹니다.

### DPM 에이전트
DPM 에이전트를 Sharepoint 서버, SQL 서버, Share point farm을 구성하는 모든 다른서버에 설치해야 합니다. 보호 에이전트 설정에 대한 자세한 내용은 [보호 에이전트 설치](https://technet.microsoft.com/library/hh758039.aspx)를 참고하세요. 유일한 예외는 단일 WFE (웹 프런트엔드) 서버에 에이전트를 설치만 합니다. DPM은 보호에 대한 진입점으로 사용할 단일 WFE 서버에 에이전트만 필요합니다.

### Sharepoint 팜
Farm의 모든 수많은 항목때문에, DPM 폴더의 위치는 최소 2GB의 공간이 필요합니다. 이 공간은 카탈로그를 생성하는 데 필요 합니다. DPM이 특정 항목(사이트 모음, 사이트, 목록, 문서 라이브러리, 폴더, 개별 문서 및 아이템 항목)을 복구할 때 카탈로그 생성이 각 콘텐츠 데이터베이스를 포함한 URL의 목록을 만듭니다. DPM 관리자 콘솔 안의 복구 작업 영역에서 복구 가능한 항목 창에서 Url 목록을 볼 수 있습니다.

### SQL Server
DPM을 로컬 시스템으로 실행한 다음 SQL Server 데이터베이스를 백업 하려면 SQL server 해당 계정에 대한 sysadmin 권한이 필요 합니다. 백업하고 싶은 SQL 서버의*sysadmin*에 NT AUTHORITY\\SYSTEM를 설치합니다.

SharePoint 팜에서, SQL Server 별칭으로 구성 된 SQL Server 데이터베이스가 있는 경우, DPM이 보호할 프런트엔드 웹 서버의 SQL Server 클라이언트 구성 요소 설치 합니다.

### SharePoint Server
성능은 SharePoint 팜의 크기와 같은 여러 요인에 따라 달라지지만, 일반 지침에서는 하나의 DPM 서버당 25TB sharepoint farm을 보호할 수 있습니다.

### DPM 업데이트 롤업 5
Azure에서 SharePoint 팜 보호를 시작하기 전에, DPM 업데이트 롤업 5, 혹은 그 이상을 설치해야 합니다 업데이트 롤업 5는 Azure의 SharePoint 팜(SQL AlwaysOn을 사용하여 구성)를 보호하는 기능을 제공합니다. 자세한 내용은 [DPM 업데이트 롤업 5 설치](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)를 참조하세요.

### 지원 되지않는 사항
1. SharePoint 팜 보호는 검색 인덱스 혹은 응용 프로그램 서비스 데이터베이스를 보호하지 않습니다. 이러한 데이터베이스들은 별도로 보호를 구성해야 합니다.
2. DPM은 규모 확장 파일 서버(SOFS) 공유에 호스팅된 Sharepoint SQL 데이터베이스 백업을 제공하지 않습니다.

## SharePoint 보호 구성
DPM을 사용하여 SharePoint를 보호하기 전에 **ConfigureSharePoint.exe**를 사용하여 SharePoint VSS 기록기 서비스 (WSS 기록기 서비스)를 구성해야 합니다.

**ConfigureSharePoint.exe**를 프런트엔드 웹 서버의 [DPM 설치 경로] \\bin 폴더에서 찾을 수 있습니다. 이 도구는 SharePoint 팜의 자격 증명을 사용하여 보호 에이전트를 제공합니다. 단일 WFE 서버에서 실행합니다. 여러 WFE 서버만 있는 경우, 보호 그룹을 구성할 때 하나만 선택해야 합니다.

### SharePoint VSS 기록기 서비스를 구성하려면
1. WFE 서버의 명령 프롬프트에서 [DPM 설치 위치] \\bin\\로 이동
2. ConfigureSharePoint-EnableSharePointProtection를 실행 합니다.
3. 팜 관리자 자격 증명을 입력 합니다. 이 계정은 WFE 서버에서 로컬 관리자 그룹의 구성원 이어야 합니다. 팜 관리자가 로컬 관리자가 아닌 경우, WFE 서버에 다음 권한을 부여 합니다.
  - DPM 폴더 (% Program Files%\\Microsoft Data Protection Manager\\DPM)에 WSS\_Admin\_WPG 그룹 전체 제어 권한을 부여 합니다.
  - DPM 레지스트리 키 (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager)에 WSS\_Admin\_WPG 그룹 읽기 액세스 권한을 부여 합니다.

>[AZURE.NOTE]SharePoint 팜 관리자 자격 증명의 변경이 있을 때마다 ConfigureSharePoint.exe를 다시 실행 해야 합니다.

## DPM을 사용 하 여 SharePoint 팜 백업
위의 설명에 따라 DPM 및 SharePoint 팜을 구성한 후 SharePoint는 DPM으로 보호할 수 있습니다.

### SharePoint 팜을 보호하려면
1. DPM 관리자 콘솔의 **보호**탭에서 **새로 만들기**를 클릭합니다.![새 보호 탭](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. **새 보호 그룹 만들기**마법사의 **보호 그룹 종류 선택**의 화면에서, **서버**를 선택하고 **다음**을 클릭합니다.

    ![선택하는 보호 그룹 종류](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. **그룹 구성원 선택**화면에서 보호하고 싶은 SharePoint 서버의 체크박스를 선택하고 **다음**을 클릭합니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE]DPM 에이전트를 설치하면, 마법사에서 서버를 볼 수 있습니다. 또한 DPM에서는 해당 구조를 보여줍니다. ConfigureSharePoint.exe가 실행됐기 때문에, DPM은 SharePoint VSS Writer 및 해당하는 SQL 데이터베이스와 통신 및 SharePoint 팜 구조(연결된 된 콘텐츠 데이터베이스 및 모든 해당 항목)를 인식합니다.

4. **데이터 보호 방법 선택** 화면에서, *보호 그룹*의 이름을 입력하고, 선호하는 *보호 방법을* 선택합니다. **다음**을 클릭합니다.

    ![데이터 보호 방법 선택](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE]디스크 보호 선택은 짧은 보호 시간으로 복구 시간 목표를 충족시키는 데 도움이 됩니다. Azure는 테이프에 비해 비용 효율적인 장기 보호 대상입니다. 자세한 내용은 [이 문서](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)(영문)를 읽어보세요.

5. **단기 목표 지정** 화면에서 원하는 *보존 범위*를 선택하면 백업을 수행하고 싶을 때를 식별합니다.

    ![단기 목표 지정](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE]이 예제에서 복구는 대부분 5일보다 적게 있었던 데이터를 요구하기 때문에, 디스크의 보존 범위를 5일로 선택하고 비-프로덕션 시간에 백업이 실행되도록 하게 만듭니다.

6. 프로텍션 그룹에 할당된 저장소 풀 디스크 공간을 검토하고 **다음**을 클릭합니다.

7. 모든 복제 그룹에 대해 DPM은 복사본을 저장하고 관리하기 위해 디스크 공간을 할당합니다. 이 시점에서, DPM은 선택된 데이터의 복사본을 만들어야 합니다. 원하는 복사본을 만들 방법 및 날짜를 선택하고 **다음**을 클릭합니다.

    ![복제본 만들기 방법 선택](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE]네트워트 트래픽에 영향을 주지 않기 위해, 프로덕션 시간 외의 시간을 선택합니다.

8. DPM은 복사본에서 일관성 확인을 수행하여 데이터 무결성을 보장합니다. 사용할 수 있는 두 가지 옵션이 있습니다. 일관성 확인을 실행하는 일정을 정의하거나 복사본이 일치하지 않을 때마다 자동으로 일관성 확인하도록 DPM을 허락할 수 있습니다. 원하는 옵션을 선택하고 **다음**을 클릭합니다.

    ![일관성 확인](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. **온라인 보호 데이터 지정**화면에서 보호하고 싶은 SharePoint 팜을 선택하고 **다음**을 클릭합니다.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. **온라인 백업 일정 지정**화면에서, 선호하는 일정을 선택하고 **다음**을 클릭합니다.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE]DPM에는 하루에 두번씩 다른시간에 Azure로 백업할 수 있습니다.

11. 선택한 백업 일정에 따라 **온라인 보존 정책을 지정**화면에서 일별, 주별, 월별 및 연도별 백업 지점에 대 한 보존 정책을 선택 합니다.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE]DPM은 다른 백업 지점에 대해 다른 보전 정책을 사용하는 조부-아버지-아들 보존 체계를 사용합니다.

12. 디스크와 마찬가지로, Azure에서 초기 참조 지점을 만들어야 합니다. Azure로 복사할 초기 백업을 생성에 대해 원하는 옵션을 선택한 다음 **다음**을 클릭하세요.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. **요약**페이지에서 선택한 설정을 검토하고 **그룹 만들기**를 클릭합니다. 보호 그룹이 생성되면 성공 메시지가 나타납니다.

    ![요약](./media/backup-azure-backup-sharepoint/summary.png)

## DPM을 사용하여 디스크에서 SharePoint 항목 복원
아래 예제에서는 실수로 삭제한*SharePoint 복구 항목*을 복구합니다.![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **DPM 관리자 콘솔**을 엽니다. DPM에서 보호 되는 모든 SharePoint 팜이 보호 탭에 표시 됩니다.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. 항목을 복구하기 전에, **복구**탭을 선택합니다.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. 복구 지점 범위 내 검색 기반의 와일드카드를 사용하여*SharePoint 복구 항목*의 SharePoint를 검색합니다.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. 검색 결과에서 적절한 복구 지점을 선택, 마우스 오른쪽 단추로 클릭하고 **복구**를 선택합니다.

5. 다양한 복구 지점을 통해 이동하고 복구할 데이터베이스 또는 항목을 선택할 수 있습니다. **날짜 > 복구 시간**을 선택한 다음 올바른 **데이터베이스 > SharePoint 팜에 > 복구 지점 > 항목**을 선택합니다.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. 항목을 오른쪽 클릭하고 **복구**를 선택하여 **복구 마법사**를 엽니다. **다음**을 클릭합니다.

    ![복구 선택 사항 확인](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. 수행하고자 하는 복구 유형을 선택한 후 **다음**을 클릭합니다.

    ![복구 유형](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE]위의 예제에서는 원래 SharePoint 사이트의 복구 항목을 보여줍니다.

8. 사용하고자 하는 **복구 프로세스**를 선택합니다.
    - SharePoint farm이 변경되지 않은 경우 **복구 팜을 사용하지 않고 복구**를 선택합니다. 이것은 복원 중인 복구 지점과 같습니다.
    - SharePoint 팜 복구 지점이 생성 된 이후 변경 된 경우 **복구 팜을 사용 하 여 복구**를 선택합니다.

    ![복구 프로세스](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. 일시적인 데이터 복구를 위한 스테이징 SQL 인스턴스 위치를 제공하고, DPM 서버의 스테이징 파일 공유 및 항목을 복구하기 위한 SharePoint 서버를 제공합니다.

    ![스테이징 위치 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM은 SharePoint 항목을 호스팅하는 콘텐츠 데이터베이스를 임시 SQL 인스턴스로 연결합니다. 콘텐츠 데이터베이스에서 DPM 서버 항목을 복구 하 고 DPM 서버에서 준비 파일 위치에 넣습니다. 항목이 스테이징 위치, DPM서버에 복구되었고, SharePoint farm의 스테이징 위치로 내보내야 합니다.

    ![스테이징 Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. **복구 옵션 지정**을 선택하고 SharePoint farm의 보안 설정 혹은 복구 지점의 보안 설정에 적용합니다. **다음**을 클릭합니다.

    ![복구 옵션](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE]네트워크 대역폭 사용량을 제한 하도록 선택할 수 있습니다. 따라서 프로덕션 시간 동안 프로덕션 서버에 미치는 영향이 최소화 됩니다.

11. 요약 정보를 검토하고 **복구**를 클릭하여 파일 복구를 시작합니다.

    ![복구 요약](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. 이제 **DPM 관리자 콘솔**의 **모니터링**탭을 선택하여 복구의 *상태*를 봅니다.

    ![복구 상태](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE]이제 파일이 복원됩니다. 복원된 파일을 확인하려면 SharePoint 사이트를 새로 고침하세요.

## DPM을 사용하여 Azure에서 SharePoint 데이터베이스를 복원합니다.

1. SharePoint 콘텐츠 데이터베이스를 복구하려면 다양한 복구 지점으로 이동하여 (위와 같이) 복구하고자 하는 복구 지점을 선택합니다.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. SharePoint 복구 지점을 두번 클릭하면 사용할 수 있는 SharePoint 카탈로그 정보가 나타납니다.

    > [AZURE.NOTE]SharePoint 팜은 Azure에서 장기 보존으로 보호하고 있기 때문에 DPM 서버에서 사용할 수 있는 카탈로그 정보(메타데이터)가 없습니다. 결과적으로, 지정 시간 SharePoint 콘텐츠 데이터베이스를 복구해야 할 때마다 SharePoint 팜에 카탈로그를 다시 만들어야 합니다.

3. **카탈로그 다시 만들기** 클릭.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **클라우드의 카탈로그 다시 만들기**상태 창이 나타납니다.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    카탈로그 작업이 완료되면 상태가 *성공*으로 변경됩니다. **닫기**를 클릭합니다.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. DPM**복구** 탭 안의 SharePoint 개체 보기를 클릭하여 콘텐츠 데이터베이스 구조를 가져옵니다. 마우스 오른쪽 클릭 > **복구**를 클릭합니다.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. 이 시점에서, 디스크의 SharePoint 콘텐츠 데이터베이스 복구를 위해 [위의 복구 단계](#restore-a-sharepoint-item-from-disk-using-dpm)를 따라하세요.

## FAQ
Q: SQL 2014 와 SQL 2012 (SP2) 두 버전 중 어느 것이 DPM을 지원합니까?<br> A: 업데이트 롤업 4가 포함된 DPM 2012 R2가 지원합니다.

Q: SharePoint가 SQL AlwaysOn을 사용하여 구성된 경우 SharePoint 항목을 원본 위치로 복구할 수 있습니까?<br> A: 네, 항목은 원본 SharePoint 사이트에 복구 시킬 수 있습니다.

Q: SharePoint가 SQL AlwaysOn을 사용하여 구성된 경우 SharePoint 데이터베이스를 원본 위치로 복구할 수 있습니까?<br> A: SharePoint 데이터베이스가 SQL AlwaysOn 에서 구성되었으므로, 가용성 그룹 (AG)을 제거 하지 않으면 수정할 수 없습니다. 따라서 DPM은 원래 위치로 데이터베이스를 복원할 수 없습니다. SQL 데이터베이스를 다른 SQL 인스턴스로 복구시킬 수 있습니다.

## 다음 단계
- SharePoint의 DPM 보호에 대한 자세한 정보는[SharePoint의 DPM 보호를 위한 비디오 시리즈](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)를 참조하세요.
- [System Center 2012-Data Protection Manager의 릴리스](https://technet.microsoft.com/library/jj860415.aspx)를 검토하세요
- [System Center 2012 s p 1의 Data Protection Manager에 대한 릴리스 정보](https://technet.microsoft.com/library/jj860394.aspx)를 검토하세요.

<!---HONumber=Sept15_HO4-->