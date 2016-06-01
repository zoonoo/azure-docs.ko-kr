<properties
  pageTitle="Azure 백업 서버를 사용하여 워크로드를 백업하도록 환경 준비 | Microsoft Azure"
  description="Azure 백업 서버를 사용하여 워크로드를 백업하도록 환경을 적절히 준비합니다."
  services="backup"
  documentationCenter=""
  authors="trinadhk"
  manager="shreeshd"
  editor=""
  keywords="azure 백업 서버; 백업 자격 증명 모음"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/10/2016"
  ms.author="jimpark;trinadhk;pullabhk"/>

# Azure 백업 서버를 사용하여 워크로드 백업 준비

> [AZURE.SELECTOR]
- [Azure 백업 서버](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure 백업 서버(클래식)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM(클래식)](backup-azure-dpm-introduction-classic.md)

이 문서는 Azure 백업 서버를 사용하여 워크로드를 백업하기 위한 환경을 준비하는 방법에 대해 설명합니다.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../resource-manager-deployment-model.md) 모델이 있습니다. 이 문서에서는 리소스 관리자 모델을 사용하여 배포된 VM을 복원하기 위한 정보 및 절차를 제공합니다.

Azure 백업 서버로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, 단일 콘솔의 Microsoft Exchange 및 Windows 클라이언트와 같은 응용 프로그램 워크로드를 보호할 수 있습니다.

>[AZURE.WARNING] Azure 백업 서버는 워크로드 백업을 위해 DPM(Data Protection Manager) 기능을 상속합니다. 이러한 기능 중 몇 가지에 대한 DPM 설명서의 포인터가 있습니다. 그러나 Azure 백업 서버는 테이프에 대한 보호 기능을 제공하거나 System Center와 통합하지 않습니다.

## 1\. Windows Server 컴퓨터

Azure 백업 서버를 작동하고 실행하는 첫 번째 단계는 Windows Server 컴퓨터를 사용하는 것입니다.

| 위치 | 최소 요구 사항 | 추가 지침 |
| -------- | -------------------- | ----------------------- |
| Azure | Azure IaaS 가상 컴퓨터<br><br>A2 표준: 2개 코어, 3.5GB RAM | Windows Server 2012 R2 데이터 센터의 간단한 갤러리 이미지로 시작할 수 있습니다. [Azure 백업 서버(DPM)를 사용하여 IaaS 워크로드를 보호하는 데는](https://technet.microsoft.com/library/jj852163.aspx) 미묘한 많은 차이가 있습니다. 컴퓨터를 배포하기 전에 이 문서를 완전히 읽어야 합니다. |
| 온-프레미스 | Hyper-V VM,<br> VMWare VM,<br> 또는 실제 호스트<br><br>2개 코어 및 4GB RAM | Windows Server 중복 제거를 사용하여 DPM 저장소를 중복 제거할 수 있습니다. [DPM 및 중복 제거](https://technet.microsoft.com/library/dn891438.aspx)가 Hyper-V VM에 배포될 때 함께 작동하는 방법에 대해 자세히 알아보세요. |

> [AZURE.NOTE] Windows Server 2012 R2 데이터 센터가 있는 컴퓨터에 Azure 백업 서버를 설치하는 것이 좋습니다. Windows 운영 체제의 최신 버전을 사용하면 많은 필수 조건이 자동으로 해결됩니다.

특정 시점에 이 서버를 도메인에 가입시키려는 경우 Azure 백업 서버를 설치하기 전에 도메인 가입 작업을 수행하는 것이 좋습니다. 배포 후 기존 Azure 백업 서버 컴퓨터를 새 도메인으로 이동하는 것은 *지원되지 않습니다*.

## 2\. 복구 서비스 자격 증명 모음

Azure에 백업 데이터를 전송하거나 로컬로 유지하거나 관계없이 소프트웨어는 Azure에 연결되어야 합니다. 더 구체적으로 설명하자면 Azure 백업 서버 컴퓨터를 복구 서비스 자격 증명 모음에 등록해야 합니다.

복구 서비스 자격 증명 모음을 만들려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. 허브 메뉴에서 **찾아보기**를 클릭하고 리소스 목록에서 **복구 서비스**를 입력합니다. 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **복구 서비스 자격 증명 모음**을 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다.

3. **복구 서비스 자격 증명 모음** 메뉴에서 **추가**를 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 2단계](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    복구 서비스 자격 증명 모음 블레이드가 열리고 **이름**, **구독**, **리소스 그룹** 및 **위치**를 입력하라는 메시지가 표시됩니다.

    ![복구 서비스 자격 증명 모음 만들기 5단계](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.

5. **구독**을 클릭하여 사용 가능한 구독 목록을 볼 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 조직 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.

6. **리소스 그룹**을 클릭하여 사용 가능한 리소스 그룹 목록을 표시하거나 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 배포 및 관리](../azure-portal/resource-group-portal.md)를 참조하세요.

7. **위치**를 클릭하여 자격 증명 모음에 대한 지리적 지역을 선택합니다.

8. **만들기**를 클릭합니다. 복구 서비스 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 사용자 자격 증명 모음을 만들면 포털에서 열립니다.

### 저장소 복제 설정

저장소 복제 옵션을 사용하면 지역 중복 저장소와 로컬 중복 저장소 중에서 선택할 수 있습니다. 기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. 기본 백업인 경우 지역 중복 저장소 옵션이 설정된 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우에는 로컬 중복 저장소를 선택합니다. [지역 중복](../storage/storage-redundancy.md#geo-redundant-storage) 및 [로컬 중복](../storage/storage-redundancy.md#locally-redundant-storage) 저장소 옵션에 대한 자세한 내용은 [Azure 저장소 복제 개요](../storage/storage-redundancy.md)를 참조하세요.

저장소 복제 설정을 편집하려면

1. 자격 증명 모음 대시보드 및 설정 블레이드를 열 자격 증명 모음을 선택합니다. **설정** 블레이드가 열리지 않을 경우 자격 증명 모음 대시보드에서 **모든 설정**을 클릭합니다.

2. **설정** 블레이드에서 **백업 인프라** > **백업 구성**을 클릭하여 **백업 구성** 블레이드를 엽니다. **백업 구성** 블레이드에서 자격 증명 모음에 대한 저장소 복제 옵션을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    자격 증명 모음에 대한 저장소 옵션을 선택하면 자격 증명 모음이 있는 VM에 연결할 준비가 됩니다. 연결을 시작하려면 Azure 가상 컴퓨터를 검색하고 등록해야 합니다.

## 3\. 소프트웨어 패키지

### 소프트웨어 패키지 다운로드
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. 복구 서비스 자격 증명 모음이 이미 열려 있으면 3단계를 진행합니다. 복구 서비스 자격 증명 모음이 열려 있지 않지만 Azure 포털에 있는 경우 허브 메뉴에서 **찾아보기**를 클릭합니다.

    - 리소스 목록에서 **복구 서비스**를 입력합니다.
    - 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **복구 서비스 자격 증명 모음**이 표시되면 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

    복구 서비스 자격 증명 모음의 목록이 표시됩니다.
    
    - 복구 서비스 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

    선택한 자격 증명 모음 대시보드가 열립니다.

    ![자격 증명 모음 블레이드 열기](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

3. 설정 블레이드가 기본적으로 열립니다. 닫혀있으면, **설정**을 클릭하고 설정 블레이드를 엽니다.

    ![자격 증명 모음 블레이드 열기](./media/backup-azure-microsoft-azure-backup/vault-setting.png)

4. **시작**에서 **백업**을 클릭하여 시작 마법사를 엽니다.

    ![백업 시작](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

5. Getting Started(시작)가 열리면, Backup Goals(백업 목표) 화면이 자동으로 선택됩니다. ![백업-목표-기본-열기](./media/backup-azure-microsoft-azure-backup/getting-started.png)

    **Backup goals**(백업 목표) 섹션에서 *where is your workload running*(워크로드 실행 위치)에 *온-프레미스*를 선택합니다.

    ![온-프레미스 및 목표 워크로드](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

6. *what workloads you want to protect*(보호할 워크로드)에서 Azure 백업 서버를 사용하여 보호할 워크로드를 선택하고 **확인**을 클릭합니다.

    > [AZURE.NOTE] 파일과 폴더만 보호하려면 Azure 백업 에이전트를 사용하는 것이 좋습니다. 파일과 폴더 이상으로 더 많은 워크로드를 보호할 예정이거나, 나중에 보호 요구를 확장할 계획이면, 해당 워크로드를 모두 살펴봅니다.

    이렇게 하면 시작 마법사가 온-프레미스의 워크로드를 Azure로 보호하기 위해 인프라를 준비하도록 변경됩니다.

    ![시작 마법사 변경](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)
  
7. **인프라 준비** 블레이드가 열리면, **Download Azure Backup Server**(Azure 백업 서버 다운로드)와 Azure 백업 서버를 복구 서비스 자격 증명 모음에 등록하는 동안 사용한 보관 자격 증명을 클릭합니다. 그러면 소프트웨어 패키지를 다운로드할 수 있는 다운로드 센터 페이지로 이동하게 됩니다.

    ![Azure 백업 서버에 대한 인프라 준비](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

8. 모든 파일을 선택하고 **다음**을 클릭합니다. Microsoft Azure 백업 다운로드 페이지에서 들어오는 모든 파일을 다운로드하고 모든 파일을 동일한 폴더에 배치합니다.

    ![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    모든 파일의 다운로드 크기가 >3G이기 때문에 10Mbps 다운로드 링크에서 다운로드를 완료하는 데 최대 60분이 걸릴 수 있습니다.


### 소프트웨어 패키지 추출

모든 파일을 다운로드한 후에 **MicrosoftAzureBackupInstaller.exe**를 클릭합니다. 사용자가 지정한 위치에 설치 파일을 추출하는 **Microsoft Azure 백업 설정 마법사**가 시작됩니다. 마법사를 계속 진행하고 **추출** 단추를 클릭하여 추출 프로세스를 시작합니다.

> [AZURE.WARNING] 설치 파일을 추출하려면 4GB 이상의 사용 가능한 공간이 필요합니다.


![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/extract/03.png)

추출 프로세스가 완료되면 새로 추출한 *setup.exe*를 시작할 상자를 선택하여 Microsoft Azure 백업 서버 설치를 시작하고 **마침** 단추를 클릭합니다.

### 소프트웨어 패키지 설치

1. **Microsoft Azure 백업**을 클릭하여 설치 마법사를 시작합니다.

    ![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. 시작 화면에서 **다음** 단추를 클릭합니다. 그러면 *필수 구성 요소 확인* 섹션으로 이동합니다. 이 화면에서 **확인** 단추를 클릭하여 Azure 백업 서버에 대한 하드웨어 및 소프트웨어 필수 구성 요소가 충족되었는지 확인합니다. 모든 필수 조건이 성공적으로 충족되면 컴퓨터가 요구 사항을 만족한다는 것을 나타내는 메시지가 표시됩니다. **다음** 단추를 클릭합니다.

    ![Azure 백업 서버 - 시작 및 필수 조건 확인](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. Microsoft Azure 백업 서버에는 SQL Server Standard가 필요하며 Azure 백업 서버 설치 패키지는 필요한 적절한 SQL Server 이진 파일과 함께 제공됩니다. 새 Azure 백업 서버 설치를 시작할 때 **Install new Instance of SQL Server with this Setup**(이 설치 프로그램으로 새 SQL Server 인스턴스 설치) 옵션을 선택하고 **확인 후 설치** 단추를 클릭해야 합니다. 필수 구성 요소가 성공적으로 설치되면 **다음**을 클릭합니다.

    ![Azure 백업 서버 - SQL 확인](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    컴퓨터를 다시 시작하라는 권장 사항과 함께 실패가 발생한 경우 작업을 수행하고 **다시 확인**을 클릭합니다.

    > [AZURE.NOTE] Azure 백업 서버는 원격 SQL Server 인스턴스에서 작동하지 않습니다. Azure 백업 서버에서 사용되는 인스턴스는 로컬이어야 합니다.

4. Microsoft Azure 백업 서버 파일을 설치할 위치를 입력하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    스크래치 위치는 Azure에 백업에 대한 요구 사항입니다. 스크래치 위치가 클라우드로 백업할 계획된 데이터의 5%인지 확인하세요. 디스크 보호를 위해 별도 디스크가 설치를 완료하면 구성되어야 합니다. 저장소 풀에 관련된 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.

5. 제한된 로컬 사용자 계정에 강력한 암호를 제공하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. *Microsoft 업데이트*를 사용할지 선택하여 업데이트를 확인하고 **다음**을 클릭합니다.

    >[AZURE.NOTE] Windows 업데이트에 Microsoft 업데이트를 리디렉션하는 것이 좋으며 이는 Windows 및 Microsoft Azure 백업 서버와 같은 다른 제품에 대한 보안 및 중요 업데이트를 제공합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. *설정 요약*으 검토하고 **설치**를 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. 설치는 단계적으로 발생합니다. 첫 번째 단계에서 Microsoft Azure 복구 서비스 에이전트가 서버에 설치됩니다. 또한 마법사가 인터넷 연결을 확인합니다. 인터넷 연결을 사용할 수 있으면 설치를 계속 진행할 수 있고 그렇지 않으면 프록시 세부 정보를 제공하여 인터넷에 연결해야 합니다.

    다음 단계는 Microsoft Azure 복구 서비스 에이전트를 구성하는 것입니다. 구성의 일부로 복구 서비스 자격 증명 모음에 컴퓨터를 등록하려면 보관 자격 증명을 제공해야 합니다. 또한 Azure 및 프레미스 간에 전송된 데이터를 암호화/암호 해독할 암호를 제공하게 됩니다. 자동으로 암호를 생성하거나 최소 16자인 고유의 암호를 제공할 수 있습니다. 에이전트가 구성될 때까지 마법사를 계속 진행합니다.

    ![Azure 백업 서버 PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Microsoft Azure 백업 서버 등록이 완료되면 전체 설치 마법사는 SQL Server 및 Azure 백업 서버 구성 요소의 설치 및 구성으로 진행됩니다. SQL Server 구성 요소 설치가 완료되면 Azure 백업 서버 구성 요소가 설치됩니다.

    ![Azure 백업 서버](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


설치 단계가 완료되면 제품의 바탕 화면 아이콘도 만들어집니다. 제품을 시작하려면 아이콘을 두 번 클릭합니다.

### 백업 저장소 추가

첫 번째 백업 복사본은 Azure 백업 서버 컴퓨터에 연결된 저장소에 보관됩니다. 디스크 추가에 대한 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.

> [AZURE.NOTE] 데이터를 Azure에 전송하려는 경우에도 백업 저장소를 추가해야 합니다. Azure 백업 서버의 현재 아키텍처에서 Azure 백업 자격 증명 모음은 데이터의 *두 번째* 복사본을 보유하고 로컬 저장소에서는 첫 번째(및 필수) 백업 복사본을 보유합니다.

## 4\. 네트워크 연결

Azure 백업 서버가 Azure 백업 서비스에 연결되어야 제품이 제대로 작동합니다. 컴퓨터가 Azure에 연결되어 있는지 여부를 확인하려면 Azure 백업 서버 PowerShell 콘솔에서 ```Get-DPMCloudConnection``` cmdlet을 사용합니다. cmdlet의 출력이 TRUE인 경우 연결되어 있고 그렇지 않으면 연결되지 않은 것입니다.

이와 동시에 Azure 구독은 정상 상태여야 합니다. 구독 상태를 확인하고 관리하려면 [구독 포털](https://account.windowsazure.com/Subscriptions)에 로그인합니다.

Azure 연결 및 Azure 구독 상태를 알고 있다면 아래 표를 사용하여 제공된 백업/복원 기능에 미치는 영향을 알아볼 수 있습니다.

| 연결 상태 | Azure 구독 | Azure에 백업| 디스크에 백업 | Azure에서 복구 | 디스크에서 복구 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 연결됨 | Active | 허용됨 | 허용됨 | 허용됨 | 허용됨 |
| 연결됨 | 만료됨 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 연결됨 | 프로비전 해제됨 | 중지됨 | 중지됨 | 중지되고 Azure 복구 지점 삭제됨 | 중지됨 |
| 손실된 연결 > 15일 | 활성 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 손실된 연결 > 15일 | 만료됨 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 손실된 연결 > 15일 | 프로비전 해제됨 | 중지됨 | 중지됨 | 중지되고 Azure 복구 지점 삭제됨 | 중지됨 |

### 연결 끊김 복구
Azure에 대한 액세스를 차단하고 있는 방화벽 또는 프록시가 있는 경우 방화벽/프록시 프로필에서 다음 도메인 주소를 허용 목록에 추가해야 합니다.

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

Azure 백업 서버 컴퓨터에 Azure에 대한 연결이 복원되면 수행할 수 있는 작업은 Azure 구독 상태에 따라 결정됩니다. 컴퓨터가 "연결된" 경우 허용되는 작업에 대한 세부 정보가 위의 표에 나와 있습니다.

### 구독 상태 처리

Azure 구독을 *만료됨* 또는 *프로비전 해제됨* 상태에서 *활성* 상태로 전환할 수 있습니다. 그러나 이 작업은 *활성* 상태가 아닌 동안 제품 동작에 일부 영향을 미칩니다.

- *프로비전 해제됨* 구독은 프로비전 해제 기간 동안 기능이 손실됩니다. *활성*으로 전환될 때 제품의 백업/복원 기능을 다시 사용할 수 있습니다. 또한 로컬 디스크의 백업 데이터가 충분한 보존 기간으로 유지되었다면 검색이 가능합니다. 그러나 Azure의 백업 데이터는 구독이 *프로비전 해제됨* 상태로 전환되면 회복할 수 없는 상태로 손실됩니다.
- *만료됨* 구독은 다시 *활성* 상태로 될 때까지만 기능이 손실됩니다. 구독이 *만료됨* 상태인 기간에 예약된 모든 백업은 실행되지 않습니다.


## 문제 해결

설치(또는 백업 또는 복원) 단계 동안 Microsoft Azure 백업 서버가 오류로 실패하면 자세한 내용은 [오류 코드 문서](https://support.microsoft.com/kb/3041338)를 참조합니다. [Azure 백업 관련 FAQ](backup-azure-backup-faq.md)를 참조할 수 있습니다.


## 다음 단계

[DPM을 위한 환경 준비](https://technet.microsoft.com/library/hh758176.aspx)에 대한 자세한 내용은 Microsoft TechNet 사이트에서 확인할 수 있습니다. 또한 여기에는 Azure 백업 서버를 배포 및 사용하는 데 지원되는 구성에 대한 정보도 포함되어 있습니다.

Microsoft Azure 백업 서버를 사용하여 워크로드 보호를 더 깊이 이해하려면 다음 문서를 사용할 수 있습니다.

- [SQL Server 백업](backup-azure-backup-sql.md)
- [SharePoint 서버 백업](backup-azure-backup-sharepoint.md)
- [대체 서버 백업](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0518_2016-->