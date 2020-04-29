---
title: MARS (Microsoft Azure Recovery Services) 에이전트 설치
description: Windows 컴퓨터를 백업 하는 MARS (Microsoft Azure Recovery Services) 에이전트를 설치 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d3932b66dbc41ff2631e2cccbe716c0877a509d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422925"
---
# <a name="install-the-azure-backup-mars-agent"></a>MARS 에이전트 Azure Backup를 설치 합니다.

이 문서에서는 MARS (Microsoft Azure Recovery Services) 에이전트를 설치 하는 방법을 설명 합니다. MARS는 Azure Backup 에이전트 라고도 합니다.

## <a name="about-the-mars-agent"></a>MARS 에이전트 정보

Azure Backup MARS 에이전트를 사용 하 여 온-프레미스 컴퓨터 및 Azure Vm에서 파일, 폴더 및 시스템 상태를 백업 합니다. 이러한 백업은 Azure의 Recovery Services 자격 증명 모음에 저장 됩니다. 에이전트를 실행할 수 있습니다.

* 온-프레미스 Windows 컴퓨터에서 직접 이러한 컴퓨터는 Azure에서 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
* Azure VM 백업 확장과 함께 Windows를 실행 하는 Azure Vm 에이전트는 VM의 특정 파일 및 폴더를 백업 합니다.
* Microsoft Azure Backup 서버 (MABS) 인스턴스 또는 System Center Data Protection Manager (DPM) 서버에 있습니다. 이 시나리오에서 컴퓨터 및 워크 로드는 MABS 또는 Data Protection Manager에 백업 합니다. 그런 다음, MABS 또는 Data Protection Manager MARS 에이전트를 사용 하 여 Azure의 자격 증명 모음에 백업 합니다.

백업에 사용할 수 있는 데이터는 에이전트가 설치 된 위치에 따라 달라 집니다.

> [!NOTE]
> 일반적으로 VM에서 Azure Backup 확장을 사용 하 여 Azure VM을 백업 합니다. 이 메서드는 전체 VM을 백업 합니다. VM의 특정 파일 및 폴더를 백업 하려는 경우에는 확장과 함께 MARS 에이전트를 설치 하 고 사용 합니다. 자세한 내용은 [기본 제공 AZURE VM 백업의 아키텍처](backup-architecture.md#architecture-built-in-azure-vm-backup)를 참조 하세요.

![Backup 프로세스 단계](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>시작하기 전에

* [AZURE BACKUP MARS 에이전트를 사용 하 여 Windows 컴퓨터를 백업](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)하는 방법에 대해 알아봅니다.
* 보조 MABS 또는 Data Protection Manager 서버에서 MARS 에이전트를 실행 하는 [백업 아키텍처](backup-architecture.md#architecture-back-up-to-dpmmabs) 에 대해 알아봅니다.
* 지원 되는 기능과 MARS 에이전트에서 [백업할 수 있는 항목을](backup-support-matrix-mars-agent.md) 검토 합니다.
* 서버 또는 클라이언트를 Azure에 백업 해야 하는 경우 Azure 계정이 있는지 확인 합니다. 계정이 없는 경우 몇 분만에 [무료](https://azure.microsoft.com/free/) 계정을 만들 수 있습니다.
* 백업 하려는 컴퓨터에서 인터넷 액세스를 확인 합니다.
* MARS 에이전트를 설치 하 고 구성 하는 사용자에 게 보호할 서버에 대 한 로컬 관리자 권한이 있는지 확인 하십시오.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 자격 증명 모음은 [GRS (지역 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)를 사용 합니다.

* 자격 증명 모음이 기본 백업 메커니즘인 경우 GRS를 사용 하는 것이 좋습니다.
* [LRS (로컬 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 를 사용 하 여 Azure storage 비용을 줄일 수 있습니다.

저장소 복제 유형을 수정 하려면:

1. 새 자격 증명 모음에서 **설정** 섹션의 **속성** 을 선택 합니다.

1. **속성** 페이지의 **백업 구성**에서 **업데이트**를 선택 합니다.

1. 저장소 복제 유형을 선택 하 고 **저장**을 선택 합니다.

    ![백업 구성 업데이트](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 자격 증명 모음을 설정 하 고 백업 항목을 포함 한 후에는 저장소 복제 유형을 수정할 수 없습니다. 이 작업을 수행 하려는 경우 자격 증명 모음을 다시 만들어야 합니다.
>

### <a name="verify-internet-access"></a>인터넷 액세스 확인

컴퓨터의 인터넷 액세스가 제한 된 경우 컴퓨터 또는 프록시의 방화벽 설정에서 다음 Url 및 IP 주소를 허용 하는지 확인 합니다.

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Azure Express 경로 사용

공용 피어 링 (이전 회로에 사용 가능) 및 Microsoft 피어 링을 사용 하 여 Azure Express 경로를 통해 데이터를 백업할 수 있습니다. 개인 피어 링에 대 한 백업은 지원 되지 않습니다.

공용 피어 링을 사용 하려면 먼저 다음 도메인 및 주소에 대 한 액세스를 확인 합니다.

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft 피어 링을 사용 하려면 다음 서비스, 지역 및 관련 커뮤니티 값을 선택 합니다.

* Azure Active Directory (12076:5060)
* Recovery Services 자격 증명 모음의 위치에 따라 Azure 지역
* Recovery Services 자격 증명 모음의 위치에 따라 Azure Storage

자세한 내용은 [express 경로 라우팅 요구 사항](https://docs.microsoft.com/azure/expressroute/expressroute-routing)을 참조 하세요.

> [!NOTE]
> 공용 피어 링은 새 회로에서 사용 되지 않습니다.

위의 모든 Url 및 IP 주소는 포트 443에서 HTTPS 프로토콜을 사용 합니다.

### <a name="private-endpoints"></a>전용 끝점

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>MARS 에이전트 다운로드

MARS 에이전트를 다운로드 하 여 백업 하려는 컴퓨터에 설치할 수 있습니다.

컴퓨터에 에이전트를 이미 설치한 경우 최신 버전의 에이전트를 실행 하 고 있는지 확인 합니다. 포털에서 최신 버전을 찾거나 [다운로드](https://aka.ms/azurebackup_agent)로 직접 이동 합니다.

1. 자격 증명 모음의 **시작**에서 **백업**을 선택 합니다.

    ![백업 목표를 엽니다.](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. **워크 로드가 실행 되는 위치**에서 **온-프레미스**를 선택 합니다. Azure VM에 MARS 에이전트를 설치 하려는 경우에도이 옵션을 선택 합니다.
1. **백업할 항목**에서 **파일 및 폴더**를 선택 합니다. **시스템 상태**를 선택할 수도 있습니다. 다른 많은 옵션을 사용할 수 있지만 이러한 옵션은 보조 백업 서버를 실행 하는 경우에만 지원 됩니다. **인프라 준비**를 선택 합니다.

    ![파일 및 폴더 구성](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. **인프라 준비**의 경우 **Recovery Services 에이전트 설치**에서 MARS 에이전트를 다운로드 합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 다운로드 메뉴에서 **저장**을 선택 합니다. 기본적으로 *MARSagentinstaller.exe* 파일이 다운로드 폴더에 저장됩니다.

1. **이미 다운로드 또는 최신 Recovery Services 에이전트 사용**을 선택 하 고 자격 증명 모음 자격 증명을 다운로드 합니다.

    ![저장소 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. **저장**을 선택합니다. 파일이 다운로드 폴더에 다운로드 됩니다. 자격 증명 모음 자격 증명 파일을 열 수 없습니다.

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

1. 백업 하려는 컴퓨터에서 *Marsagentinstaller .exe* 파일을 실행 합니다.
1. MARS 에이전트 설치 마법사에서 **설치 설정**을 선택 합니다. 여기에서 에이전트를 설치할 위치를 선택 하 고 캐시 위치를 선택 합니다. **다음**을 선택합니다.
   * Azure Backup은 캐시를 사용 하 여 데이터 스냅숏을 Azure로 보내기 전에 저장 합니다.
   * 캐시 위치에는 백업할 데이터 크기의 5% 이상에 해당 하는 여유 공간이 있어야 합니다.

    ![MARS 에이전트 설치 마법사에서 설치 설정 선택](./media/backup-configure-vault/mars1.png)

1. **프록시 구성**의 경우 Windows 컴퓨터에서 실행 되는 에이전트가 인터넷에 연결 되는 방법을 지정 합니다. **다음**을 선택합니다.

   * 사용자 지정 프록시를 사용 하는 경우 필요한 프록시 설정 및 자격 증명을 지정 합니다.
   * 에이전트는 [특정 url](#before-you-start)에 액세스할 수 있어야 합니다.

    ![MARS 마법사에서 인터넷 액세스 설정](./media/backup-configure-vault/mars2.png)

1. **설치**의 경우 필수 구성 요소를 검토 하 고 **설치**를 선택 합니다.
1. 에이전트가 설치 되 면 **등록 진행을**선택 합니다.
1. **서버 등록 마법사** > **자격 증명 모음 식별**에서 다운로드 한 자격 증명 파일을 찾아 선택 합니다. **다음**을 선택합니다.

    ![서버 등록 마법사를 사용 하 여 자격 증명 모음 자격 증명 추가](./media/backup-configure-vault/register1.png)

1. **암호화 설정** 페이지에서 컴퓨터에 대 한 백업을 암호화 하 고 암호를 해독 하는 데 사용할 암호를 지정 합니다.

    * 암호를 안전한 위치에 저장 합니다. 백업을 복원 하는 데 필요 합니다.
    * 암호를 분실 하거나 잊은 경우 Microsoft는 백업 데이터를 복구 하는 데 도움을 줍니다.

1. **마침**을 선택합니다. 이제 에이전트가 설치 되 고 컴퓨터가 자격 증명 모음에 등록 됩니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="next-steps"></a>다음 단계

[MARS 에이전트 Azure Backup를 사용 하 여 Windows 컴퓨터를 백업](backup-windows-with-mars-agent.md) 하는 방법을 알아봅니다.
