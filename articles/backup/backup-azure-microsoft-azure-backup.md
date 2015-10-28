<properties
  pageTitle="Microsoft Azure에 워크로드 백업 준비 | Microsoft Azure"
  description="이 문서에서는 Microsoft Azure 백업에 응용 프로그램 워크로드 기능의 개요를 제공합니다."
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Microsoft Azure에 워크로드 백업 준비

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

이 문서는 사용자 환경을 준비하여 워크로드를 백업하므로 Azure에 데이터를 백업할 수 있습니다. Microsoft Azure 백업 서버로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, Microsoft Exchange 및 Windows 클라이언트와 같은 응용 프로그램 워크로드를 보호할 수 있습니다.

- 계층1 워크로드에 높은 RTO를 제공하는 **디스크**(D2D)
- 장기 보존용 **Azure**(D2D2C)

그리고 단일 온-프레미스 사용자 인터페이스에서 여러 보호된 엔터티(서버 및 클라이언트)를 보호하도록 관리할 수 있습니다.

>[AZURE.NOTE]Microsoft Azure 백업 서버는 워크로드 백업을 위해 DPM(Data Protection Manager) 기능을 상속합니다. 이러한 기능 중 몇 가지에 대한 DPM 설명서의 포인터가 있습니다. 그러나 Microsoft Azure 백업 서버는 테이프에 대한 보호 기능을 제공하거나 System Center와 통합하지 않습니다.

Microsoft Azure 백업 서버를 다음으로 배포할 수 있습니다.

- 실제 독립 실행형 서버입니다.
- Hyper-V 가상 컴퓨터 - 온-프레미스 데이터를 백업하는 온-프레미스 Hyper-V 호스트 서버에서 호스팅되는 가상 컴퓨터인 Microsoft Azure 백업 서버를 실행할 수 있습니다. 이 환경에 대한 고려 사항 목록은 [온-프레미스 Hyper-V 서버의 가상 컴퓨터로 DPM 설치](https://technet.microsoft.com/library/dn440941.aspx)를 참조하세요.
- VMWare의 Windows 가상 컴퓨터 - Microsoft Azure 백업 서버를 배포하여 VMWare의 Windows 가상 컴퓨터에서 실행되는 Microsoft 워크로드에 보호를 제공할 수 있습니다. 이 시나리오에서 Microsoft Azure 백업 서버는 VMWare의 물리적 독립 실행형 서버, Hyper-V 가상 컴퓨터 또는 Windows 가상 컴퓨터로 배포될 수 있습니다.
- Azure 가상 컴퓨터 - Microsoft Azure 백업 서버를 Azure에서 가상 컴퓨터로 실행하여 Azure 가상 컴퓨터를 실행하는 클라우드 워크로드를 백업할 수 있습니다. 이 배포에 대한 정보는 [Azure 가상 컴퓨터인 DPM 설치](https://technet.microsoft.com/library/hh758176.aspx)를 참조하세요.

다음도 참조하세요.

- Microsoft Azure 백업은 Windows Server 2008 R2 SP1, 2012 및 2012 R2에 설치할 수 있습니다.
- Microsoft Azure 백업 서버는 SCDPM 또는 SCDPM RA 에이전트가 설치된 컴퓨터에 설치할 수 없습니다.
- Microsoft Azure 백업 서버는 Microsoft Azure 백업 에이전트가 설치된 컴퓨터에 설치할 수 없고 Azure 백업 자격 증명 모음으로 등록할 수 없습니다.



Microsoft Azure 백업을 설치하려는 서버를 선택하면 다음을 수행해야 합니다.

- Microsoft Azure 백업 서버 필수 구성 요소가 충족되는지 확인
- 백업 자격 증명 모음 만들기
- 저장소 자격 증명 다운로드 및
- Microsoft Azure 백업 서버 설치 파일을 다운로드 합니다.

각각은 아래에 자세히 설명되어 있습니다.

## 시작하기 전에

오늘날 Microsoft Azure 백업 서버는 영어 로컬에만 사용할 수 있습니다. 현재 Microsoft Azure 복구 서비스는 Microsoft Azure 정부 데이터 센터 및 중국의 Microsoft Azure가 아닌 Azure를 사용 가능한 모든 지역에서 사용할 수 있습니다.

원활한 설치를 위해 Microsoft Azure 백업 서버를 설치하기 전에 다음 필수 조건이 충족되는지 확인합니다.

- 서버는 인터넷에 연결되어 있어야 합니다. Microsoft Azure는 서버에서 액세스할 수 있어야 합니다.
- Microsoft Azure 백업을 설치하기 위한 서버 요구 사항은 DPM으로 동일합니다. 추가 정보는 해당 [하드웨어 요구 사항](https://technet.microsoft.com/library/dn726764.aspx)을 참조하세요.
- Microsoft Azure 백업 서버는 도메인에 연결되어야 합니다.
- Microsoft Azure 백업 서버에는 .Net 3.5,.Net 4.0,.Net 3.5 SP1 기능이 설치되어야 합니다. 추가 정보는 [.NET Framework를 사용하는 방법에 대한 자세한 내용](https://technet.microsoft.com/library/dn482071.aspx)을 참조하세요.
- Microsoft Azure 백업 서버에는 Windows Management Framework 4.0이 설치되어야 합니다. [여기](http://www.microsoft.com/download/details.aspx?id=40855)에서 다운로드할 수 있습니다.
- Microsoft Azure 백업 서버 데이터 저장소에 대한 전용 공간으로 사용되는 디스크의 경우 권장되는 저장소 풀 크기는 보호된 데이터의 1.5배 크기입니다. 자세한 내용은 [이 항목](https://technet.microsoft.com/library/hh758176.aspx#DPM 서버)의 디스크 및 저장소 섹션을 참조하십시오.

Microsoft Azure 백업 서버를 준비하여 다음으로 데이터를 백업합니다.

1. **백업 자격 증명 모음 만들기** — Azure 백업 콘솔에 자격 증명 모음을 만듭니다.
2. **저장소 자격 증명 다운로드** — Azure 백업에서 자격 증명 모음에 대해 만든 관리 인증서를 업로드합니다.
3. **Microsoft Azure 백업 서버 다운로드** — 백업 자격 증명의 빠른 시작 페이지에서 응용 프로그램 워크로드에 Microsoft Azure 백업 서버를 다운로드할 수 있습니다.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Microsoft Azure 백업 서버 다운로드
자격 증명 모음과 비슷하게 백업 자격 증명의 **빠른 시작 페이지**에서 응용 프로그램 워크로드에 Microsoft Azure 백업 서버를 다운로드할 수 있습니다.

1. **응용 프로그램 워크로드에 대해서(디스크에서 디스크로 클라우드로)**를 클릭합니다.

    ![Microsoft Azure 백업 시작 화면](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. **다운로드**를 클릭합니다.

    ![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. 모든 파일을 선택하고 **다음**을 클릭합니다.
![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]모든 파일의 다운로드 크기가 >3G이기 때문에 10Mbps 다운로드 링크에서 다운로드를 완료하는 데 최대 60분이 걸릴 수 있습니다.

4. 동일한 폴더에 모든 파일을 배치합니다.

5. Microsoft Azure 백업 다운로드 페이지에서 들어오는 모든 파일을 다운로드합니다.

## Microsoft Azure 백업 서버 설치
설치를 시작하기 전에 이전 섹션에서 언급한 필수 구성 요소가 충족되는지 확인합니다.

1. 모든 파일을 다운로드한 후에 **MicrosoftAzureBackupInstaller.exe**를 클릭합니다. **Microsoft Azure 백업 설치 마법사**가 표시됩니다.

    ![Microsoft Azure 백업 설치 관리자](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. 파일을 추출할 해당 서버에 위치를 제공하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. **추출**을 클릭하여 설치 파일을 추출하기 시작합니다.

    ![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. setup.exe를 시작하는 확인란을 선택하여 Microsoft Azure 백업 서버를 설치하기 시작하고 **마침**을 클릭합니다.

    ![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. **Microsoft Azure 백업**을 클릭하여 설치 마법사를 시작합니다.

    ![Microsoft Azure 백업 설치 마법사](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. 시작 화면에서 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 시작 화면](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. **확인**을 클릭하여 Microsoft Azure 백업 서버에 대한 하드웨어 및 소프트웨어 필수 구성 요소가 충족되는지 결정합니다.

    ![Microsoft Azure 백업 PreReq1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. 모든 필수 구성 요소가 성공적으로 충족되면 컴퓨터가 요구 사항을 만족한다는 것을 나타내는 메시지가 표시됩니다. **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Microsoft Azure 백업 서버에는 SQL Server Standard가 필요합니다. 기존 2014 SQL Server Standard 로컬 인스턴스를 선택하거나 설치 마법사를 사용하여 SQL Server Standard를 설치합니다. **확인 및 설치**를 클릭하여 서버에 SQL 필수 구성 요소를 설치가 필요합니다.

    ![Microsoft Azure 백업 SQL1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    컴퓨터를 다시 시작하라는 권장 사항과 함께 실패가 발생한 경우 작업을 수행하고 **다시 확인**을 클릭합니다.

    ![SiS 필터 실패](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. 필수 구성 요소가 성공적으로 설치되면 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Microsoft Azure 백업 서버 파일을 설치할 위치를 입력하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE] 스크래치 위치는 Azure에 백업에 대한 요구 사항입니다. 스크래치 위치가 클라우드로 백업할 계획된 데이터의 5%인지 확인하세요. 디스크 보호를 위해 별도 디스크가 설치를 완료하면 구성되어야 합니다. 저장소 풀에 관련된 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.

12. 제한된 로컬 사용자 계정에 강력한 암호를 제공하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE] 강력한 암호를 설정하는 것이 시스템의 보안에 중요합니다. 강력한 암호는 적어도 6자 길이이며 사용자의 계정 이름의 전체 또는 일부를 포함하지 않고 다음 네 가지 문자 중 세 가지 이상을 포함하는 암호입니다. 대문자, 소문자, 10진수 및 기호(예:!, @, #).

13. *Microsoft 업데이트*를 사용할지 선택하여 업데이트를 확인하고 **다음**을 클릭합니다.

    >[AZURE.NOTE] Windows 업데이트에 Microsoft Update를 리디렉션하는 것이 좋으며 이는 Windows 및 Microsoft Azure 백업 서버와 같은 다른 제품에 대한 보안 및 중요 업데이트를 제공합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. *설정 요약*으 검토하고 **설치**를 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    설치는 단계적으로 발생합니다. 서버에 에이전트가 설치된 첫 번째 단계에서 해당 제품과 함께 다운로드한 유효한 Azure 백업 자격 증명 모음으로 서버를 등록해야 합니다. 그런 다음 마법사가 직접 인터넷 연결을 확인합니다. 인터넷 연결을 사용할 수 있으면 설치를 계속 진행할 수 있고 그렇지 않으면 프록시 세부 정보를 제공하여 인터넷에 연결해야 합니다.

15. **다음**을 클릭하여 Microsoft Azure 복구 서비스 에이전트의 설치 단계를 시작합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    마법사는 필수 항목을 확인하고 누락이 발견되면 설치합니다.

16. **Install**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. 에이전트 설치가 완료되면 **다음**을 클릭하여 Azure 백업 자격 증명 모음으로이 서버를 등록합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Azure 백업 자격 증명 모음을 제공하여 서버를 등록하고 **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Azure에 전송되는 데이터를 암호화/암호 해독하는 데 사용되는 암호를 제공하고 해당 암호를 저장할 위치를 제공합니다. 자동으로 암호를 생성하거나 최소 16자인 고유의 암호를 제공할 수 있습니다. **다음**을 클릭합니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE] 기밀성을 유지하려면 Microsoft Azure 백업 서버는 이 암호를 데이터와 함께 Azure에 보내지 않습니다. 안전한 위치에 Azure에서 데이터를 복원할 때 필요한 암호를 저장해야 합니다.

    Microsoft Azure 백업 서버의 등록을 성공적으로 완료하면 설치는 SQL Server Standard 2014의 설치 및 구성을 진행합니다.(선택 시)

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    SQL 설치가 완료되면 Microsoft Azure 백업 서버 구성 요소가 설치됩니다.

    ![Microsoft Azure 백업 PreReq2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. 제품 설치되고 바탕 화면 아이콘이 만들어지면 아이콘을 두 번 클릭하여 제품을 시작합니다.

    ![아이콘](./media/backup-azure-microsoft-azure-backup/icons.png)

## 저장소 풀에 디스크 추가

워크로드를 디스크 및 Azure에 백업하려면 먼저 저장소 풀에 디스크를 추가해야 합니다. 디스크 추가에 대한 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.

저장소 풀이 구성되면 디스크 및 Azure에 응용 프로그램 워크로드를 백업할 수 있습니다.

## 문제 해결

설치(또는 백업 또는 복원) 단계 동안 Microsoft Azure 백업 서버가 오류로 실패하면 자세한 내용은 [오류 코드 문서](https://support.microsoft.com/kb/3041338)를 참조합니다.
[Azure 백업 관련 FAQ](backup-azure-backup-faq.md)를 참조할 수 있습니다.

## FAQ

### 설치 및 배포

Q: NTFS 압축은 디스크 백업에 사용된 Microsoft Azure 백업 서버 디스크/볼륨에서 지원됩니까?
<br>A: NTFS는 Microsoft Azure 백업 서버에 연결된 디스크/볼륨에서 지원되지**않습니다**.

Q: Microsoft Azure 백업 서버를 새 도메인 게시 배포로 이동할 수 있습니까?
<br>A: 아니요, Microsoft Azure 백업 서버를 새 도메인 게시 배포로 이동하도록 지원하지 **않습니다**.

Q: 도메인 컨트롤러에 설치된 경우 어떤 리소스가 Microsoft Azure 백업 서버를 보호할 수 있습니까?
<br>A: 도메인 컨트롤러와 동일한 서버에 함께 설치된 경우 Microsoft Azure 백업 서버는 로컬 데이터 원본만 보호할 수 있습니다. 다른 서버의 워크로드를 보호하기 위해 Microsoft Azure 백업 서버는 도메인 컨트롤러와 동일한 컴퓨터에 설치하지 마십시오.

Q: Microsoft Azure 백업 서버는 원격 SQL Server 인스턴스를 사용할 수 있습니까?
<br>A: 아니요, Microsoft Azure 백업 서버는 로컬 SQL Server 인스턴스만을 사용할 수 있습니다.

### Microsoft Azure 백업 서버 저장소 풀

Q: Microsoft Azure 백업 서버 저장소 풀에서 가상 디스크(VHD/HDx)를 제공할 수 있습니까?
<br>A: 예.

Q: Microsoft Azure 백업 서버 저장소 풀에 중복 제거를 가져올 수 있습니까?
<br>A: 예, Microsoft Azure 백업 서버 저장소 풀에 중복 제거를 사용 가능합니다. Microsoft Azure 백업 서버에 대한 사용자 환경은 해당 [DPM 블로그 게시물](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)에서 자세히 설명된 바와 같습니다.

Q: USB 또는 제거 드라이브는 Microsoft Azure 백업 서버 저장소 풀에 사용할 수 있습니까?
<br>A: 아니요.

### Azure 서비스에 Microsoft Azure 백업 서버 연결

Q: Azure 서비스 및 Azure 구독에 Microsoft Azure 백업 서버 연결 상태가 백업 및 복원에 어떤 영향을 줍니까?
<br>A: Microsoft Azure 백업 서버가 Azure 서비스에 연결되어야 제품이 제대로 작동합니다. 이와 동시에 Azure 구독은 정상 상태여야 합니다.

다음 테이블에서 다른 연결 및 Azure 구독 상태와 관련하여 Microsoft Azure 백업 서버의 기능을 설명합니다.

| 연결 상태 | Azure 구독 | Azure에 백업| 디스크에 백업 | Azure에서 복구 | 디스크에서 복구 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 연결됨 | Active | 허용됨 | 허용됨 | 허용됨 | 허용됨 |
| 연결됨 | 만료됨 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 정상 | 프로비전 해제됨 | 중지됨 | 중지됨 | 중지되고 Azure 복구 지점 삭제됨 | 중지됨 |
| 손실된 연결 > 15일 | 활성 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 손실된 연결 > 15일 | 만료됨 | 중지됨 | 중지됨 | 허용됨 | 허용됨 |
| 손실된 연결 > 15일 | 프로비전 해제됨 | 중지됨 | 중지됨 | 중지되고 Azure 복구 지점 삭제됨 | 중지됨 |

Q: 구독 상태가 계속 *활성*이라고 가정하면 Microsoft Azure 백업 서버 연결이 복원된 경우 어떻게 될까요?
<br>A: Microsoft Azure 서비스에 Azure 백업 서버 연결이 정상적으로 다시 시작되고 구독 상태가 *활성*이면 Microsoft Azure 백업 서버의 모든 작업이 허용됩니다. 두 디스크에 뿐만 아니라 Azure에도 백업이 정상적으로 수행됩니다.

Q: *만료됨* 상태에서 *활성* 상태로 구독 상태를 복원하는 경우 어떻게 됩니까?
<br>A: Microsoft Azure 서비스에 Azure 백업 서버 연결이 정상적으로 다시 시작된다고 가정하면 Microsoft Azure 백업 서버 구독 상태가 *활성*으로 복원되면 Microsoft Azure 백업 서버의 모든 작업이 허용됩니다. 이제 두 디스크에 뿐만 아니라 Azure에도 백업이 정상적으로 수행됩니다.

Q: *프로비전 해제됨* 상태에서 *활성* 상태로 구독 상태를 복원하는 경우 어떻게 됩니까?
<br>A: Microsoft Azure 서비스에 Azure 백업 서버 연결이 정상적으로 다시 시작된다고 가정하면 Microsoft Azure 백업 서버 구독 상태가 *활성*으로 복원되면 Microsoft Azure 백업 서버의 모든 작업이 허용됩니다. 그러나 Azure 복구 지점이 삭제되고 복원될 수 없습니다. 디스크 백업이 유효한 보존 범위에 있는 경우 디스크 복구 지점은 복원할 수 있습니다.

Q: 공용 Azure 서비스에 Microsoft Azure 백업 서버 연결을 허용하기 위해 어떤 예외를 만들어야 합니까?
<br>A: 방화벽 프로필에서 다음 도메인 주소를 허용해야 합니다. www.msftncsi.com, *. Microsoft.com, *. WindowsAzure.com, *. microsoftonline.com, *. windows.net.

Q: Microsoft Azure 백업 서버가 Azure 서비스에 연결되었는지 여부를 어떻게 확인할 수 있습니까?
<br>A: Microsoft Azure 백업 서버 콘솔에서 다음 PowerShell cmdlet을 실행합니다.

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

Q: 내 구독 상태가 *활성*이 아닌 이유가 무엇입니까? *활성*으로 변경하려면 어떻게 합니까?
<br>A: [구독 포털](https://account.windowsazure.com/Subscriptions)에 로그온하고 구독을 관리합니다.

### 결제

Q: Microsoft Azure 백업 서버에 대한 요금 청구 모델은 무엇입니까?
<br>A: 보호된 인스턴스 모델을 통해 사용자에게 요금이 청구됩니다. 자세한 내용은 [가격 책정](http://azure.microsoft.com/pricing/details/backup/) 페이지에서 FAQ를 참조합니다.

Q: 디스크에 데이터를 보호한 경우 요금 청구 모델은 무엇입니까?
<br>A: 는 요금 청구 모델은 보호된 인스턴스 모델과 같습니다. 해당 데이터가 온-프레미스 저장소에서 보호되므로 디스크 기반 백업의 경우 Azure 저장소 요금이 없습니다. 이 경우 보호된 인스턴스 요금만 고객에게 청구됩니다. 인스턴스의 정의가 무엇이고 인스턴스당 얼마가 청구되는지에 대한 자세한 내용은 [가격 책정](http://azure.microsoft.com/pricing/details/backup/) 페이지의 FAQ를 참조합니다.

Q: 보호된 인스턴스당 요금이란 무엇입니까?
<br>A: [가격 책정 페이지](http://azure.microsoft.com/pricing/details/backup/)를 참조합니다.

Q: 다른 응용 프로그램 워크로드 및 보호된 인스턴스 요금을 강조 표시하는 예제는 어디서 찾을 수 있습니까?
<br>A: [가격 책정](http://azure.microsoft.com/pricing/details/backup/) 페이지의 "예제 섹션"을 참조합니다.

Q: Microsoft Azure 백업 서버는 클라우드 뿐만 아니라 디스크에 보호된 데이터 원본을 어떻게 청구합니까?
<br>A: 데이터를 디스크 또는 클라우드 중 어디에 백업했는지와 관계 없이 Microsoft Azure 백업 서버는 보호된 인스턴스에 따라 요금을 청구합니다. 보호된 인스턴스 크기는 데이터 원본의 프런트 엔드 크기에 따라 계산됩니다. 또한 Azure 저장소에서 백업 데이터의 경우 Azure 저장소 비용도 적용합니다.

## 다음 단계

Microsoft Azure 백업 서버를 사용하여 워크로드 보호를 더 깊이 이해하려면 다음 문서를 사용할 수 있습니다.

- [SQL Server 백업](backup-azure-backup-sql.md)
- [SharePoint 서버 백업](backup-azure-backup-sharepoint.md)
- [대체 서버 백업](backup-azure-alternate-dpm-server.md)

<!----HONumber=Oct15_HO3-->