<properties
	pageTitle="Windows 서버 또는 클라이언트 컴퓨터를 백업하기 위한 환경 준비 | Microsoft Azure"
	description="백업 자격 증명 모음을 만들고 자격 증명을 다운로드하고 백업 에이전트를 설치하여 Windows 백업을 위한 환경을 준비합니다."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="백업 자격 증명 모음; 백업 에이전트; Windows 백업"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/05/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Windows 컴퓨터를 Azure에 백업하기 위한 환경 준비
이 문서는 Windows 컴퓨터를 Azure에 백업하기 위한 환경을 준비하기 위해 수행해야 하는 항목을 나열합니다.

| 단계 | 이름 | 세부 정보 |
| :------: | ---- | ------- |
| 1 | [자격 증명 모음 만들기](#create-a-backup-vault) | [Azure 백업 관리 포털](http://manage.windowsazure.com)에 자격 증명 모음을 만듭니다. |
| 2 | [저장소 자격 증명 다운로드](#download-the-vault-credential-file) | Windows 컴퓨터를 백업 자격 증명 모음에 등록하는 데 사용될 자격 증명을 다운로드합니다. |
| 3 | [Azure 백업 에이전트 설치](#download-install-and-register-the-azure-backup-agent) | 에이전트를 설치하고 보관 자격 증명을 사용하여 백업 자격 증명 모음에 서버를 등록합니다. |

위의 상위 수준 단계를 이미 모두 수행한 경우 [Windows 컴퓨터 백업](backup-azure-backup-windows-server.md)을 시작할 수 있습니다. 그렇지 않은 경우 아래 세부 단계를 계속 진행하여 사용자 환경이 준비되었는지 확인합니다.

## 시작하기 전에
Windows 컴퓨터 백업 환경을 준비하기 위해서는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 [무료 평가판 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## 백업 자격 증명 모음 만들기
Windows 컴퓨터 또는 Data Protection Manager(DPM)의 파일과 데이터를 Azure에 백업하거나 IaaS VM을 Azure에 백업하려면 데이터를 저장하려는 지역에 백업 자격 증명 모음을 만들어야 합니다.

**백업 자격 증명 모음을 만들려면**

1. [관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. **새로 만들기** > **데이터 서비스** > **복구 서비스** > **백업 자격 증명 모음**을 클릭하고 **빨리 만들기**를 선택합니다.

    ![자격 증명 모음 만들기](./media/backup-configure-vault/createvault1.png)

3. **이름** 매개 변수에 백업 자격 증명 모음을 식별할 이름을 입력합니다. 이는 각 구독에 대해 고유해야 합니다.

    **지역** 매개 변수에서 백업 자격 증명 모음의 지역을 선택합니다. 선택에 따라 백업 데이터가 전송되는 지역이 결정됩니다. 사용자의 위치에 가까운 지역을 선택하면 Azure에 백업할 때 네트워크 대기 시간을 줄일 수 있습니다.

    **자격 증명 모음 만들기**를 클릭하여 워크플로를 완료합니다.

    백업 저장소름 만드는 데 시간이 걸릴 수 있습니다. 상태를 확인하려면 포털 화면의 아래쪽에서 알림을 모니터링할 수 있습니다.

    ![자격 증명 모음을 만드는 중](./media/backup-configure-vault/creatingvault1.png)

    백업 자격 증명 모음을 만든 후 자격 증명 모음이 성공적으로 생성되었다는 메시지가 나타납니다. 자격 증명 모음이 복구 서비스에 대한 리소스에 **활성**으로 표시됩니다.![자격 증명 모음을 만드는 중 상태](./media/backup-configure-vault/backupvaultstatus1.png)

> [AZURE.IMPORTANT] 저장소 중복 옵션을 지정하기에 가장 좋은 시기는 자격 증명 모음을 만든 후 자격 증명 모음에 컴퓨터를 등록하기 바로 직전입니다. 항목이 자격 증명 모음에 등록되고 나면 저장소 중복 옵션 잠기고 수정할 수 없습니다.
>
> **저장소 중복 옵션을 선택하는 방법에 대한 자세한 내용은 [개요](backup-azure-storage-redundancy-options.md)를 참조하세요.**

## 자격 증명 모음 자격 증명 파일 다운로드
온-프레미스 서버(Windows 클라이언트 또는 Windows Server 또는 데이터 보호 관리자 서버)는 백업 자격 증명 모음을 인증해야 Azure에 데이터를 백업할 수 있습니다. 인증은 "자격 증명 모음 자격 증명"을 사용하여 수행됩니다. 자격 증명 모음 파일은 Azure 포털의 보안 채널을 통해 다운로드되며, Azure 백업 서비스는 포털 또는 서비스에 유지되지 않도록 인증서의 개인 키를 인식하지 않습니다.

[Azure 백업 서비스로 인증하기 위한 자격 증명 모음 자격 증명](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file)에 대한 자세한 내용을 알아보려면

**로컬 컴퓨터에 자격 증명 모음 자격 증명 파일을 다운로드하려면**

1. [관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 탐색 창에서 **복구 서비스**를 클릭하고 생성된 백업 자격 증명 모음을 선택합니다.

3.  클라우드 아이콘을 클릭하여 백업 자격 증명 모음의 *빠른 시작* 보기로 이동합니다.

    ![빠른 보기](./media/backup-configure-vault/quickview.png)

4. **빠른 시작** 페이지에서 **자격 증명 모음 자격 증명 다운로드**를 클릭합니다.

    ![다운로드](./media/backup-configure-vault/downloadvc.png)

    포털에서는 저장소 이름과 현재 날짜를 조합하여 저자격 증명 모음 자격 증명을 생성합니다. 자격 증명 모음 자격 증명 파일은 등록 워크플로 중에만 사용되며 48시간 후에 만료됩니다.

    자격 증명 모음 자격 증명 파일은 포털에서 다운로드할 수 있습니다.

5. **저장**을 클릭하여 저장소 자격 증명을 로컬 계정의 다운로드 폴더로 다운로드하거나, **저장** 메뉴에서 *다른 이름으로 저장*을 선택하여 저장소 자격 증명을 저장할 위치를 지정합니다.

    지금은 자격 증명 모음 자격 증명을 열 필요가 없습니다.

    자격 증명 모음 자격 증명이 컴퓨터에서 액세스할 수 있는 위치에 저장되었는지 확인합니다. 파일 공유/SMB에 저장된 경우 액세스 권한을 확인합니다.

## Azure 백업 에이전트 다운로드, 설치 및 등록
Azure 백업 자격 증명 모음을 만든 후에는 각 Windows 컴퓨터(Windows Server, Windows 클라이언트, 시스템 센터, Data Protection Manager 서버 또는 Azure 백업 서버 컴퓨터)에 Azure에 데이터 및 응용 프로그램을 백업할 수 있게 해주는 에이전트가 설치되어야 합니다.

**에이전트를 다운로드, 설치 및 등록하려면**

1. [관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. **복구 서비스**를 클릭한 후 서버에 등록하려는 백업 저장소를 선택합니다.

3. 빠른 시작 페이지에서 **Windows Server 또는 System Center Data Protection Manager 또는 Windows 클라이언트용 에이전트 > 저장**을 클릭합니다.

    ![에이전트 저장](./media/backup-configure-vault/agent.png)

4. *MARSagentinstaller.exe* 다운로드가 완료되면 **실행**을 클릭합니다(또는 저장된 위치에서**MARSAgentInstaller.exe**를 두 번 클릭합니다). *설치 폴더* 및 *에이전트*에 필요한 스크래치 폴더를 선택하고 **다음**을 클릭합니다.

    지정된 캐시 위치에 백업 데이터의 5% 이상에 해당하는 여유 공간이 있어야 합니다.

    ![스크래치 및 캐시](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. 프록시 서버를 사용하여 인터넷에 연결하는 경우 **프록시 구성** 화면에서 프록시 서버 세부 정보를 입력합니다. 인증된 프록시를 사용하는 경우 사용자 이름 및 암호 세부 정보를 입력하고 **다음**을 클릭합니다.

    Azure 백업 에이전트가 .NET Framework 4.5 및 Windows PowerShell(아직 설치되지 않은 경우)을 설치하여 설치를 완료합니다.

6. 에이전트가 설치되면 **등록 진행**을 클릭하여 워크플로를 계속합니다.

    ![등록](./media/backup-configure-vault/register.png)

7. **자격 증명 모음 식별 화면**에서 이전에 다운로드한 *자격 증명 모음 자격 증명 파일*로 이동하여 선택합니다.

    ![자격 증명 모음 자격 증명](./media/backup-configure-vault/vc.png)

    자격 증명 모음 자격 증명 파일은 48시간 동안만 유효합니다(그 이후에는 포털에서 다운로드). 이 화면에서 오류가 발생할 경우(예: "제공한 자격 증명 모음 자격 증명 파일이 만료되었습니다") Azure 포털에 로그인하고 자격 증명 모음 자격 증명 파일을 다시 다운로드합니다.

    설치 응용 프로그램에서 액세스할 수 있는 위치에 있는 자격 증명 모음 자격 증명 파일인지 확인합니다. 액세스 관련 오류가 발생할 경우 이 컴퓨터의 임시 위치에 자격 증명 모음 자격 증명 파일을 복사하고 작업을 다시 시도합니다.

    잘못된 자격 증명 모음 자격 증명 오류(예: "잘못된 자격 증명 모음 자격 증명을 제공했습니다.")가 발생한 경우 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. 포털에서 새 자격 증명 모음 자격 증명 파일을 다운로드한 후에 작업을 다시 시도합니다. 이 오류는 일반적으로 사용자가 *자격 증명 모음 자격 증명 다운로드* 옵션을 빠르게 연속적으로 클릭할 경우에 표시됩니다. 이 경우 마지막 자격 증명 모음 자격 증명 파일만 유효합니다.

8. **암호화 설정** 화면에서 암호를 *생성*하거나 암호를 *제공*합니다.(최소 16자) 암호를 안전한 위치에 저장해야 합니다.

    ![암호화](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] 암호를 분실하거나 잊어버린 경우 Microsoft는 백업 데이터 복구를 도와드릴 수 없습니다. 암호화 암호는 최종 사용자가 소유하며 Microsoft는 최종 사용자가 사용하는 암호를 전혀 볼 수 없습니다. 이 파일은 복구 작업에 필요하므로 안전한 위치에 저장하십시오.

9. **마침**을 클릭합니다.

    이제 컴퓨터가 자격 증명 모음에 성공적으로 등록되고 이제 Microsoft Azure에 백업을 시작할 준비가 완료된 것입니다.

## 다음 단계
- [무료 Azure 평가판 계정](https://azure.microsoft.com/pricing/free-trial/) 등록
- [Windows 서버 또는 클라이언트 컴퓨터를 백업합니다](backup-azure-backup-windows-server.md).
- 여전히 답변이 없는 질문이 있는 경우 [Azure 백업 FAQ](backup-azure-backup-faq.md)를 살펴보세요.
- [Azure 백업 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)을 방문하세요.

<!---HONumber=AcomDC_0211_2016-->