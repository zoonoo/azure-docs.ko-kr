<properties
   pageTitle="Windows에서 Azure로 파일 및 폴더 백업하는 방법 알아보기 | Microsoft Azure"
   description="자격 증명 모음을 만들고, 백업 에이전트를 설치하고, 파일 및 폴더를 Azure에 백업하여 Windows Server 데이터를 백업하는 방법을 알아봅니다."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="백업 방법, 백업하는 방법"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# 소개: Windows Server 또는 클라이언트에서 Azure로 파일 및 폴더 백업

이 문서는 Azure 백업을 사용하여 Azure에 Windows 서버 (또는 Windows 클라이언트) 파일 및 폴더를 백업하는 방법을 설명합니다. 간단하게 Azure 백업을 사용하려는 경우 바로 여기서 알 수 있습니다.

파일 및 폴더를 백업하려면 다음 단계만 수행합니다.

![1단계](./media/backup-try-azure-backup-in-10-mins/step-1.png) Azure를 구독합니다(필요한 경우).<br> ![2단계](./media/backup-try-azure-backup-in-10-mins/step-2.png) 백업 자격 증명 모음을 만들고 필요한 구성 요소를 다운로드합니다.<br> ![3단계](./media/backup-try-azure-backup-in-10-mins/step-3.png) 해당 구성 요소를 설치하고 등록하여 Windows Server 또는 클라이언트를 준비합니다.<br> ![4단계](./media/backup-try-azure-backup-in-10-mins/step-4.png) 데이터를 백업합니다.


![Azure 백업을 사용하여 Windows 컴퓨터를 백업하는 방법](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## 1단계: Azure 구독하기

Azure 구독이 없는 경우 모든 Azure 서비스에 액세스할 수 있게 해주는 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

>[AZURE.NOTE] Azure 구독이 있는 경우 이 단계를 건너뛸 수 있습니다.

## 2단계: 백업 자격 증명 모음을 만들고 필요한 구성 요소를 다운로드합니다.

파일과 폴더를 백업하려면 데이터를 저장하려는 지역에 백업 자격 증명 모음을 만들어야 합니다.

1. 아직 로그인하지 않은 경우 Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **새로 만들기 > 하이브리드 통합 > 백업**을 클릭합니다.

    ![파일 및 폴더를 백업하기 위해 준비를 시작합니다.](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. 결과 화면의 **이름**에 백업 자격 증명 모음을 식별할 친숙한 이름을 입력합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.

    b. **지역**에서 백업 자격 증명 모음의 지역을 선택합니다. 사용자의 위치에 가까운 지역을 선택하면 Azure에 백업할 때 네트워크 대기 시간을 줄일 수 있습니다.

    c. **자격 증명 모음 만들기**를 클릭합니다.

    ![자격 증명 모음 만들기](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    상태는 포털 화면의 아래쪽에 있는 알림을 통해 모니터링할 수 있습니다.

    ![자격 증명 모음 프로세스](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    백업 자격 증명 모음을 만들면 복구 서비스의 리소스에 **활성**으로 나열됩니다.

    ![자격 증명 모음 상태는 활성](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. **저장소 중복** 옵션을 선택합니다.

    저장소 중복 옵션을 선택하기에 가장 좋은 시기는 자격 증명 모음을 만든 후 자격 증명 모음에 컴퓨터를 등록하기 바로 직전입니다. 항목이 자격 증명 모음에 등록되고 나면 저장소 중복 옵션 잠기고 수정할 수 없습니다.

    Azure를 기본 백업 저장소 끝점으로 사용하는 경우 (기본) [지역 중복 저장소](../storage/storage-redundancy.md#geo-redundant-storage) 옵션을 선택하는 것이 좋습니다.

    Azure를 기본 백업 저장소 끝점으로 사용하는 경우 [로컬 중복 저장소](../storage/storage-redundancy.md#locally-redundant-storage)를 선택하는 것이 좋습니다. 이 옵션을 사용하면 Azure에 데이터를 저장하는 비용을 크게 줄일 수 있지만 3차 복사본에 허용될 수 있는 영속성 수준이 낮아집니다.

    a. 방금 만든 자격 증명 모음을 클릭합니다.

    b. 빠른 시작 페이지에서 **구성**을 선택합니다.

    ![자격 증명 모음 상태 구성](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. 적절한 저장소 중복 옵션을 선택합니다.

    ![저장소 중복 옵션 선택](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    **지역 중복**이 기본 옵션이기 때문에 **로컬 중복**을 선택한 경우 **저장**을 클릭합니다.

    d. 왼쪽 탐색 창에서 **복구 서비스**에 대한 리소스 목록으로 돌아가려면 **복구 서비스**를 클릭합니다.

    ![복구 서비스](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    이제 방금 만든 백업 자격 증명 모음으로 Windows 컴퓨터를 인증해야 합니다. 인증은 자격 증명 모음 자격 증명을 사용하여 수행됩니다.

4.  방금 만든 자격 증명 모음을 클릭합니다.

    ![새 자격 증명 선택](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. **빠른 시작** 페이지에서 **자격 증명 모음 자격 증명 다운로드**를 클릭합니다.

    ![저장소 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    포털에서는 자격 증명 모음 이름과 현재 날짜를 조합하여 자격 증명 모음 자격 증명을 생성합니다.

    >[AZURE.NOTE] 자격 증명 모음 자격 증명 파일은 등록 워크플로 중에만 사용되며 48시간 후에 만료됩니다.

6. **저장**을 클릭하여 자격 증명 모음 자격 증명을 로컬 **다운로드** 폴더에 다운로드합니다. **저장** 메뉴에서 **다른 이름으로 저장**을 선택하여 자격 증명 모음 자격 증명에 대한 위치를 지정할 수 있습니다.

    자격 증명 모음 자격 증명이 컴퓨터에서 액세스할 수 있는 위치에 저장되었는지 확인합니다. 파일이 파일 공유/SMB에 저장된 경우 액세스 권한을 확인합니다.

    >[AZURE.NOTE] 프로시저에서 지금은 자격 증명 모음 자격 증명을 열 필요가 없습니다.

    그 다음 백업 에이전트를 다운로드해야 합니다.

7. 왼쪽 탐색 창에서 **복구 서비스**를 클릭한 다음 서버에 등록하려는 백업 자격 증명 모음을 클릭합니다.

    ![백업 자격 증명 모음 선택](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. 빠른 시작 페이지에서 **Windows Server 또는 System Center Data Protection Manager 또는 Windows 클라이언트용 에이전트 > 저장**을 클릭합니다.

    ![백업 에이전트 저장](./media/backup-try-azure-backup-in-10-mins/agent.png)

이것으로 백업 자격 증명 모음을 만들고 필요한 구성 요소 다운로드를 완료했습니다. 이제 백업 에이전트를 설치합니다.

## 3단계: 백업 구성 요소를 설치하고 등록하여 Windows Server 또는 클라이언트 준비

1. 다운로드가 완료되면 저장된 위치에서 **MARSagentinstaller.exe**를 두 번 클릭합니다(또는 이전 단계에서 파일을 저장하는 대신 **실행**을 클릭할 수 있습니다).

2. 에이전트에 필요한 **설치 폴더** 및 **캐시 폴더**를 선택합니다.

    지정된 캐시 위치에 백업 데이터의 5% 이상에 해당하는 여유 공간이 있어야 합니다.

    **다음**을 클릭합니다.

    ![설치 및 캐시 폴더](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (선택 사항) 프록시 서버를 사용하여 인터넷에 연결하는 경우 **프록시 구성** 화면에서 **사용자 지정 프록시 설정 사용** 상자를 확인하고 프록시 서버 세부 정보를 입력합니다.

    인증된 프록시를 사용하는 경우 사용자 이름 및 암호 세부 정보를 입력합니다.

    **다음**을 클릭합니다.

    ![프록시 구성](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. **Install**을 클릭합니다.

    Azure 백업 에이전트가 .NET Framework 4.5 및 Windows PowerShell을 설치하여(아직 설치되지 않은 경우) 설치를 완료합니다.

5. 에이전트가 설치되면 **등록 진행**을 클릭하여 워크플로를 계속합니다.

    ![등록](./media/backup-try-azure-backup-in-10-mins/register.png)

6. **자격 증명 모음 식별 화면**에서 이전에 다운로드한 **자격 증명 모음 자격 증명 파일**로 이동하여 선택합니다. 설치 응용 프로그램에서 액세스할 수 있는 위치에 있는 자격 증명 모음 자격 증명 파일인지 확인합니다.

    **다음**을 클릭합니다.

    ![자격 증명 모음 식별](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. **암호화 설정** 화면에서 암호를 생성하거나 암호를 제공합니다.(최소 16자) 암호를 안전한 위치에 저장해야 합니다.

    > [AZURE.WARNING] 암호를 분실하거나 잊어버린 경우 Microsoft에서 백업 데이터의 복구를 도와드릴 수 없습니다. 사용자는 암호화 암호를 소유하며 Microsoft는 최종 사용자가 사용하는 암호를 전혀 볼 수 없습니다. 이 파일은 복구 작업에 필요하므로 안전한 위치에 저장하십시오.

    **마침**을 클릭합니다.

    ![암호화 설정](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    **서버 등록 마법사**는 Microsoft Azure 백업에 서버를 등록합니다.

8. **암호화 키**가 설정되면, **Microsoft Azure 복구 서비스 에이전트 시작** 확인란은 선택된 상태로 두고 **닫기**를 클릭합니다.

    이제 컴퓨터가 자격 증명 모음에 성공적으로 등록되고 백업 옵션을 구성하고 예약할 준비되었습니다.

## 4단계: 데이터를 백업합니다.

1. **백업 에이전트**(**Microsoft Azure 복구 서비스 에이전트 시작** 확인란을 선택된 상태로 둔 경우 자동으로 열림)에서 **백업 일정**을 클릭합니다.

    ![Windows Server 백업 예약](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. **시작** 화면에서 **다음**을 클릭합니다.

3. **백업할 항목 선택** 화면에서 **항목 추가**를 클릭합니다. Windows 서버 또는 Windows 클라이언트에서 Azure 백업을 사용하면 파일과 폴더를 보호할 수 있습니다.

    ![Windows Server 백업 예약](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    백업하려는 항목을 선택하고 **확인**을 클릭합니다.

    **다음**을 클릭합니다.

4. **백업 일정**을 지정하고 **다음**을 클릭합니다.

    매일(하루에 최대 3회) 또는 매주 백업을 예약할 수 있습니다.

    ![백업 일정 변경](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. 백업 복사본에 대한 **보존 정책**을 선택합니다. 매일, 매주, 매월 및 매년 보존 정책을 요구 사항에 맞게 수정할 수 있습니다.

    >[AZURE.NOTE] 백업 일정은 [문서](backup-azure-backup-cloud-as-tape.md)에 자세히 설명되어 있습니다.

     **다음**를 클릭합니다.

    ![보존 정책 선택](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. 초기 백업 형식을 선택합니다.

    네트워크를 통해 자동으로 백업하거나 오프라인으로 백업할 수 있습니다. 이 문서의 나머지 부분은 자동 백업 프로세스를 따릅니다. 오프라인 백업 작업을 수행하려는 경우, 이 문서의 [Azure 백업에서 워크플로를 오프라인 백업](backup-azure-backup-import-export.md)에 대한 추가 설명을 참조하세요.

    **다음**를 클릭합니다.

    ![초기 백업 유형](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. **확인** 화면에서 정보를 검토하고 **마침**을 클릭합니다.

8. 마법사가 **백업 일정** 생성을 완료하면 **닫기**를 클릭합니다.

9. **백업 에이전트**에서 **지금 백업**을 클릭하여 네트워크를 통한 초기 시드 작업을 완료합니다.

    ![지금 Windows Server 백업](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. **확인** 화면에서 컴퓨터를 백업하는 데 마법사가 사용할 설정을 검토하고 **백업**을 클릭합니다.

11. **닫기**를 클릭하여 마법사를 닫습니다. 백업 프로세스가 완료되고 백그라운드에서 실행이 계속되기 전에 실행할 수 있습니다.

12. 초기 백업 작업이 완료된 후에 Azure 백업 콘솔의 **작업** 보기에 "작업 완료" 상태가 표시됩니다.

    ![초기 백업 완료](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

축하합니다. 파일 및 폴더를 Azure 백업에 성공적으로 백업했습니다.

## 다음 단계
- Azure 백업에 대한 자세한 내용은 [Azure 백업 개요](backup-introduction-to-azure-backup.md)를 참조하세요.
- [환경 준비 및 Windows 컴퓨터 백업](backup-configure-vault.md)에 대한 자세한 내용을 가져옵니다.
- 이 [문서 먼저 보기](backup-azure-vms-first-look.md)로 Azure VM을 백업하는 방법을 알아봅니다.
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)에서 Azure 백업에 대한 최신 토론을 읽어봅니다.

<!---HONumber=AcomDC_0330_2016-->