---
title: "Azure Backup Server를 사용하여 Azure 클래식 포털에 워크로드 백업 | Microsoft Docs"
description: "Azure Backup 서버를 사용하여 워크로드를 백업하도록 환경을 적절히 준비합니다."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: "azure 백업 서버; 백업 자격 증명 모음"
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: db19729f652430c80ad0a7af1b630c1c5a1561d3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Azure Backup 서버를 사용하여 워크로드 백업 준비
> [!div class="op_single_selector"]
> * [Azure Backup 서버](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup 서버(클래식)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM(클래식)](backup-azure-dpm-introduction-classic.md)
>
>

이 문서는 Azure Backup 서버를 사용하여 워크로드를 백업하기 위한 환경을 준비하는 방법에 대해 설명합니다. Azure Backup 서버로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, 단일 콘솔의 Microsoft Exchange 및 Windows 클라이언트와 같은 응용 프로그램 워크로드를 보호할 수 있습니다.

> [!WARNING]
> Azure Backup 서버는 워크로드 백업을 위해 DPM(Data Protection Manager) 기능을 상속합니다. 이러한 기능 중 몇 가지에 대한 DPM 설명서의 포인터가 있습니다. 그러나 Azure Backup 서버는 테이프에 대한 보호 기능을 제공하거나 System Center와 통합하지 않습니다.
>
>

## <a name="1-windows-server-machine"></a>1. Windows Server 컴퓨터
![1단계](./media/backup-azure-microsoft-azure-backup/step1.png)

Azure Backup 서버를 작동하고 실행하는 첫 번째 단계는 Windows Server 컴퓨터를 사용하는 것입니다.

| 위치 | 최소 요구 사항 | 추가 지침 |
| --- | --- | --- |
| Azure |Azure IaaS 가상 컴퓨터<br><br>A2 표준: 2개 코어, 3.5GB RAM |Windows Server 2012 R2 데이터 센터의 간단한 갤러리 이미지로 시작할 수 있습니다. [Azure Backup 서버(DPM)를 사용하여 IaaS 워크로드를 보호하는 데는](https://technet.microsoft.com/library/jj852163.aspx) 미묘한 많은 차이가 있습니다. 컴퓨터를 배포하기 전에 이 문서를 완전히 읽어야 합니다. |
| 온-프레미스 |Hyper-V VM,<br> VMWare VM,<br> 또는 물리적 호스트<br><br>2개 코어 및 4GB RAM |Windows Server 중복 제거를 사용하여 DPM 저장소를 중복 제거할 수 있습니다. [DPM 및 중복 제거](https://technet.microsoft.com/library/dn891438.aspx) 가 Hyper-V VM에 배포될 때 함께 작동하는 방법에 대해 자세히 알아보세요. |

> [!NOTE]
> Windows Server 2012 R2 데이터 센터가 있는 컴퓨터에 Azure Backup 서버를 설치하는 것이 좋습니다. Windows 운영 체제의 최신 버전을 사용하면 많은 필수 조건이 자동으로 해결됩니다.
>
>

Azure Backup Server를 도메인에 조인하려는 경우 Azure Backup Server 소프트웨어를 설치하기 전에 물리적 서버 또는 가상 컴퓨터를 도메인에 조인하는 것이 좋습니다. 배포 후 Azure Backup Server를 새 도메인으로 이동하도록 *지원하지 않습니다*.

## <a name="2-backup-vault"></a>2. Backup 자격 증명 모음
![2단계](./media/backup-azure-microsoft-azure-backup/step2.png)

백업 데이터를 Azure에 전송하거나 로컬로 유지하는 경우 Azure Backup Server를 자격 증명 모음에 등록해야 합니다. 새로운 Azure Backup 사용자이며 Azure Backup Server를 사용하려는 경우 이 문서의 Azure Portal 버전 - [Azure Backup Server를 사용하여 작업 백업 준비](backup-azure-microsoft-azure-backup.md)를 참조하세요.

> [!IMPORTANT]
> 2017년 3월부터는 Backup 자격 증명 모음을 만드는 데 더 이상 클래식 포털을 사용할 수 없습니다.
> 이제 Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드할 수 있습니다. 자세한 내용은 [Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음 업그레이드](backup-azure-upgrade-backup-to-recovery-services.md) 문서를 참조하세요. Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드하는 것이 좋습니다.<br/> 2017년 11월 30일 이후에는 PowerShell을 사용하여 Backup 자격 증명 모음을 만들 수 없습니다. **2017년 11월 30일까지**:
>- 남아 있는 모든 Backup 자격 증명 모음이 Recovery Services 자격 증명 모음으로 자동 업그레이드됩니다.
>- 클래식 포털에서는 백업 데이터에 액세스할 수 없습니다. 대신 Azure Portal을 사용하여 Recovery Services 자격 증명 모음에서 백업 데이터에 액세스할 수 있습니다.
>



## <a name="3-software-package"></a>3. 소프트웨어 패키지
![3단계](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>소프트웨어 패키지 다운로드
자격 증명 모음과 비슷하게 백업 자격 증명의 **빠른 시작 페이지** 에서 응용 프로그램 워크로드에 Microsoft Azure Backup 서버를 다운로드할 수 있습니다.

1. **응용 프로그램 워크로드에 대해서(디스크에서 디스크로 클라우드로)**를 클릭합니다. 그러면 소프트웨어 패키지를 다운로드할 수 있는 다운로드 센터 페이지로 이동하게 됩니다.

    ![Microsoft Azure Backup 시작 화면](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. **다운로드**를 클릭합니다.

    ![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. 모든 파일을 선택하고 **다음**을 클릭합니다. Microsoft Azure Backup 다운로드 페이지에서 들어오는 모든 파일을 다운로드하고 모든 파일을 동일한 폴더에 배치합니다.
   ![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    모든 파일의 다운로드 크기가 >3G이기 때문에 10Mbps 다운로드 링크에서 다운로드를 완료하는 데 최대 60분이 걸릴 수 있습니다.

### <a name="extracting-the-software-package"></a>소프트웨어 패키지 추출
모든 파일을 다운로드한 후에 **MicrosoftAzureBackupInstaller.exe**를 클릭합니다. 사용자가 지정한 위치에 설치 파일을 추출하는 **Microsoft Azure Backup 설정 마법사** 가 시작됩니다. 마법사를 계속 진행하고 **추출** 단추를 클릭하여 추출 프로세스를 시작합니다.

> [!WARNING]
> 설치 파일을 추출하려면 4GB 이상의 사용 가능한 공간이 필요합니다.
>
>

![Microsoft Azure Backup 설정 마법사](./media/backup-azure-microsoft-azure-backup/extract/03.png)

추출 프로세스가 완료되면 새로 추출한 *setup.exe* 를 시작할 상자를 선택하여 Microsoft Azure Backup 서버 설치를 시작하고 **마침** 단추를 클릭합니다.

### <a name="installing-the-software-package"></a>소프트웨어 패키지 설치
1. **Microsoft Azure Backup** 을 클릭하여 설치 마법사를 시작합니다.

    ![Microsoft Azure Backup 설정 마법사](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. 시작 화면에서 **다음** 단추를 클릭합니다. 그러면 *필수 구성 요소 확인* 섹션으로 이동합니다. 이 화면에서 **확인** 단추를 클릭하여 Azure Backup 서버에 대한 하드웨어 및 소프트웨어 필수 구성 요소가 충족되었는지 확인합니다. 모든 필수 조건이 성공적으로 충족되면 컴퓨터가 요구 사항을 만족한다는 것을 나타내는 메시지가 표시됩니다. **다음** 단추를 클릭합니다.

    ![Azure Backup 서버 - 시작 및 필수 조건 확인](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup 서버에는 SQL Server Standard가 필요하며 Azure Backup 서버 설치 패키지는 필요한 적절한 SQL Server 이진 파일과 함께 제공됩니다. 새 Azure Backup Server 설치를 시작할 때 **이 설치 프로그램으로 새 SQL Server 인스턴스 설치** 옵션을 선택하고 **확인 후 설치** 단추를 클릭해야 합니다. 필수 구성 요소가 성공적으로 설치되면 **다음**을 클릭합니다.

    ![Azure Backup 서버 - SQL 확인](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    컴퓨터를 다시 시작하라는 권장 사항과 함께 실패가 발생한 경우 작업을 수행하고 **다시 확인**을 클릭합니다.

   > [!NOTE]
   > Azure Backup 서버는 원격 SQL Server 인스턴스에서 작동하지 않습니다. Azure Backup 서버에서 사용되는 인스턴스는 로컬이어야 합니다.
   >
   >

4. Microsoft Azure Backup 서버 파일을 설치할 위치를 입력하고 **다음**을 클릭합니다.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    스크래치 위치는 Azure에 백업에 대한 요구 사항입니다. 스크래치 위치가 클라우드로 백업할 계획된 데이터의 5%인지 확인하세요. 디스크 보호를 위해 별도 디스크가 설치를 완료하면 구성되어야 합니다. 저장소 풀에 관련된 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.
5. 제한된 로컬 사용자 계정에 강력한 암호를 제공하고 **다음**을 클릭합니다.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. *Microsoft 업데이트* 를 사용할지 선택하여 업데이트를 확인하고 **다음**을 클릭합니다.

   > [!NOTE]
   > Windows 업데이트에 Microsoft 업데이트를 리디렉션하는 것이 좋으며 이는 Windows 및 Microsoft Azure Backup 서버와 같은 다른 제품에 대한 보안 및 중요 업데이트를 제공합니다.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *설정 요약* 을 검토하고 **설치**를 클릭합니다.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. 설치는 단계적으로 발생합니다. 첫 번째 단계에서 Microsoft Azure Recovery Services 에이전트가 서버에 설치됩니다. 또한 마법사가 인터넷 연결을 확인합니다. 인터넷 연결을 사용할 수 있으면 설치를 계속 진행할 수 있고 그렇지 않으면 프록시 세부 정보를 제공하여 인터넷에 연결해야 합니다.

    다음 단계는 Microsoft Azure Recovery Services 에이전트를 구성하는 것입니다. 구성의 일부로 백업 자격 증명 모음에 컴퓨터를 등록하려면 자격 증명 모음을 제공해야 합니다. 또한 Azure 및 프레미스 간에 전송된 데이터를 암호화/암호 해독할 암호를 제공하게 됩니다. 자동으로 암호를 생성하거나 최소 16자인 고유의 암호를 제공할 수 있습니다. 에이전트가 구성될 때까지 마법사를 계속 진행합니다.

    ![Azure Backup 서버 PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup 서버 등록이 완료되면 전체 설치 마법사는 SQL Server 및 Azure Backup 서버 구성 요소의 설치 및 구성으로 진행됩니다. SQL Server 구성 요소 설치가 완료되면 Azure Backup 서버 구성 요소가 설치됩니다.

    ![Azure Backup 서버](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

설치 단계가 완료되면 제품의 바탕 화면 아이콘도 만들어집니다. 제품을 시작하려면 아이콘을 두 번 클릭합니다.

### <a name="add-backup-storage"></a>백업 저장소 추가
첫 번째 백업 복사본은 Azure Backup 서버 컴퓨터에 연결된 저장소에 보관됩니다. 디스크 추가에 대한 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](https://technet.microsoft.com/library/hh758075.aspx)을 참조하세요.

> [!NOTE]
> 데이터를 Azure에 전송하려는 경우에도 백업 저장소를 추가해야 합니다. Azure Backup 서버의 현재 아키텍처에서 Azure Backup 자격 증명 모음은 데이터의 *두 번째* 복사본을 보유하고 로컬 저장소에서는 첫 번째(및 필수) 백업 복사본을 보유합니다.  
>
>

## <a name="4-network-connectivity"></a>4. 네트워크 연결
![4단계](./media/backup-azure-microsoft-azure-backup/step4.png)

Azure Backup 서버가 Azure Backup 서비스에 연결되어야 제품이 제대로 작동합니다. 컴퓨터가 Azure에 연결되어 있는지 여부를 확인하려면 Azure Backup 서버 PowerShell 콘솔의 ```Get-DPMCloudConnection``` commandlet을 사용합니다. commandlet의 출력이 TRUE인 경우 연결되어 있고 그렇지 않으면 연결되지 않은 것입니다.

이와 동시에 Azure 구독은 정상 상태여야 합니다. 구독 상태를 확인하고 관리하려면 [구독 포털](https://account.windowsazure.com/Subscriptions)에 로그인합니다.

Azure 연결 및 Azure 구독 상태를 알고 있다면 아래 표를 사용하여 제공된 백업/복원 기능에 미치는 영향을 알아볼 수 있습니다.

| 연결 상태 | Azure 구독 | Azure에 Backup | 디스크에 Backup | Azure에서 복구 | 디스크에서 복구 |
| --- | --- | --- | --- | --- | --- |
| 연결됨 |Active |허용됨 |허용됨 |허용됨 |허용됨 |
| 연결됨 |만료됨 |중지됨 |중지됨 |허용됨 |허용됨 |
| 연결됨 |프로비전 해제됨 |중지됨 |중지됨 |중지되고 Azure 복구 지점 삭제됨 |중지됨 |
| 손실된 연결 > 15일 |Active |중지됨 |중지됨 |허용됨 |허용됨 |
| 손실된 연결 > 15일 |만료됨 |중지됨 |중지됨 |허용됨 |허용됨 |
| 손실된 연결 > 15일 |프로비전 해제됨 |중지됨 |중지됨 |중지되고 Azure 복구 지점 삭제됨 |중지됨 |

### <a name="recovering-from-loss-of-connectivity"></a>연결 끊김 복구
Azure에 대한 액세스를 차단하고 있는 방화벽 또는 프록시가 있는 경우 방화벽/프록시 프로필에서 다음 도메인 주소를 허용 목록에 추가해야 합니다.

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Azure Backup 서버 컴퓨터에 Azure에 대한 연결이 복원되면 수행할 수 있는 작업은 Azure 구독 상태에 따라 결정됩니다. 컴퓨터가 "연결된" 경우 허용되는 작업에 대한 세부 정보가 위의 표에 나와 있습니다.

### <a name="handling-subscription-states"></a>구독 상태 처리
Azure 구독을 *만료됨* 또는 *프로비전 해제됨* 상태에서 *활성* 상태로 전환할 수 있습니다. 그러나 이 작업은 *활성*상태가 아닌 동안 제품 동작에 일부 영향을 미칩니다.

* *프로비전 해제됨* 구독은 프로비전 해제 기간 동안 기능이 손실됩니다. *활성*으로 전환될 때 제품의 백업/복원 기능을 다시 사용할 수 있습니다. 또한 로컬 디스크의 백업 데이터가 충분한 보존 기간으로 유지되었다면 검색이 가능합니다. 그러나 Azure의 백업 데이터는 구독이 *프로비전 해제됨* 상태로 전환되면 회복할 수 없는 상태로 손실됩니다.
* *만료됨* 구독은 다시 *활성* 상태로 되기 전까지 기능을 상실합니다. 구독이 *만료됨* 상태인 기간에 예약된 모든 백업은 실행되지 않습니다.

## <a name="troubleshooting"></a>문제 해결
설치(또는 백업 또는 복원) 단계에서 Microsoft Azure Backup Server가 오류로 실패하는 경우에 대한 자세한 내용은 [오류 코드 문서](https://support.microsoft.com/kb/3041338)를 참조합니다.
[Azure Backup 관련 FAQ](backup-azure-backup-faq.md)

## <a name="next-steps"></a>다음 단계
[DPM을 위한 환경 준비](https://technet.microsoft.com/library/hh758176.aspx) 에 대한 자세한 내용은 Microsoft TechNet 사이트에서 확인할 수 있습니다. 또한 여기에는 Azure Backup 서버를 배포 및 사용하는 데 지원되는 구성에 대한 정보도 포함되어 있습니다.

Microsoft Azure Backup 서버를 사용하여 워크로드 보호를 더 깊이 이해하려면 다음 문서를 사용할 수 있습니다.

* [SQL Server 백업](backup-azure-backup-sql.md)
* [SharePoint 서버 백업](backup-azure-backup-sharepoint.md)
* [대체 서버 백업](backup-azure-alternate-dpm-server.md)
