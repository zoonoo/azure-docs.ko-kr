---
title: Microsoft Azure 복구 서비스(MARS) 에이전트 설치
description: Windows 컴퓨터를 백업하기 위해 MICROSOFT Azure 복구 서비스(MARS) 에이전트를 설치하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d3932b66dbc41ff2631e2cccbe716c0877a509d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422925"
---
# <a name="install-the-azure-backup-mars-agent"></a>Azure 백업 MARS 에이전트 설치

이 문서에서는 Microsoft Azure 복구 서비스(MARS) 에이전트를 설치하는 방법을 설명합니다. MARS는 Azure 백업 에이전트라고도 합니다.

## <a name="about-the-mars-agent"></a>MARS 에이전트 정보

Azure Backup은 MARS 에이전트를 사용하여 온-프레미스 컴퓨터 및 Azure VM에서 파일, 폴더 및 시스템 상태를 백업합니다. 이러한 백업은 Azure의 복구 서비스 자격 증명 모음에 저장됩니다. 에이전트를 실행할 수 있습니다.

* 온-프레미스 Windows 컴퓨터에서 직접 사용할 수 있습니다. 이러한 컴퓨터는 Azure의 복구 서비스 자격 증명 모음에 직접 백업할 수 있습니다.
* Azure VM 백업 확장과 함께 Windows를 나란히 실행하는 Azure VM에서 에이전트는 VM의 특정 파일 및 폴더를 백업합니다.
* Microsoft Azure 백업 서버(MABS) 인스턴스 또는 시스템 센터 데이터 보호 관리자(DPM) 서버에서 이 시나리오에서는 컴퓨터 및 워크로드를 MABS 또는 데이터 보호 관리자로 백업합니다. 그런 다음 MABS 또는 데이터 보호 관리자는 MARS 에이전트를 사용하여 Azure의 볼트에 백업합니다.

백업에 사용할 수 있는 데이터는 에이전트가 설치된 위치에 따라 다릅니다.

> [!NOTE]
> 일반적으로 VM에서 Azure 백업 확장을 사용하여 Azure VM을 백업합니다. 이 메서드는 전체 VM을 백업합니다. VM에서 특정 파일 및 폴더를 백업하려면 확장명과 함께 MARS 에이전트를 설치하고 사용합니다. 자세한 내용은 [기본 제공 Azure VM 백업의 아키텍처를](backup-architecture.md#architecture-built-in-azure-vm-backup)참조하십시오.

![Backup 프로세스 단계](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>시작하기 전에

* Azure [Backup에서 MARS 에이전트를 사용하여 Windows 컴퓨터를 백업하는](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)방법에 대해 알아봅니다.
* 보조 MABS 또는 데이터 보호 관리자 서버에서 MARS 에이전트를 실행하는 [백업 아키텍처에](backup-architecture.md#architecture-back-up-to-dpmmabs) 대해 알아봅니다.
* 지원되는 내용과 MARS 에이전트가 [백업할 수](backup-support-matrix-mars-agent.md) 있는 내용을 검토합니다.
* 서버 또는 클라이언트를 Azure에 백업해야 하는 경우 Azure 계정이 있는지 확인합니다. 계정이 없는 경우 단 몇 분 만에 [무료](https://azure.microsoft.com/free/) 계정을 만들 수 있습니다.
* 백업하려는 컴퓨터에서 인터넷 액세스를 확인합니다.
* MARS 에이전트의 설치 및 구성을 수행하는 사용자가 보호할 서버에 로컬 관리자 권한이 있는지 확인합니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 볼트는 [지리적 중복 저장소(GRS)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)사용합니다.

* 볼트가 기본 백업 메커니즘인 경우 GRS를 사용하는 것이 좋습니다.
* [LRS(로컬 중복 저장소)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 사용하여 Azure 저장소 비용을 줄일 수 있습니다.

저장소 복제 유형을 수정하려면 다음을 수행하십시오.

1. 새 볼트에서 **설정** 섹션에서 **속성을** 선택합니다.

1. **속성** 페이지에서 백업 **구성에서** **업데이트를**선택합니다.

1. 저장소 복제 유형을 선택하고 **저장을**선택합니다.

    ![백업 구성 업데이트](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 볼트를 설정하고 백업 항목을 포함하는 후에는 저장소 복제 유형을 수정할 수 없습니다. 이렇게 하려면 볼트를 다시 만들어야 합니다.
>

### <a name="verify-internet-access"></a>인터넷 액세스 확인

컴퓨터가 인터넷 액세스가 제한된 경우 컴퓨터 또는 프록시의 방화벽 설정으로 다음 URL 및 IP 주소를 허용해야 합니다.

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Azure 익스프레스루트 사용

공용 피어링(이전 회로에서 사용 가능)과 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링을 통해 백업은 지원되지 않습니다.

공용 피어링을 사용하려면 먼저 다음 도메인 및 주소에 대한 액세스를 확인합니다.

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft 피어링을 사용하려면 다음 서비스, 지역 및 관련 커뮤니티 값을 선택합니다.

* Azure Active Directory (12076:5060)
* 복구 서비스 자격 증명 모음의 위치에 따라 Azure 지역
* 복구 서비스 자격 증명 모음의 위치에 따라 Azure 저장소

자세한 내용은 [ExpressRoute 라우팅 요구 사항을](https://docs.microsoft.com/azure/expressroute/expressroute-routing)참조하십시오.

> [!NOTE]
> 새 회로의 경우 공용 피어링이 더 이상 사용되지 않습니다.

앞의 모든 URL 및 IP 주소는 포트 443에서 HTTPS 프로토콜을 사용합니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>화성 에이전트 다운로드

백업하려는 컴퓨터에 설치할 수 있도록 MARS 에이전트를 다운로드합니다.

컴퓨터에 에이전트를 이미 설치한 경우 에이전트의 최신 버전을 실행 중인지 확인합니다. 포털에서 최신 버전을 찾거나 [다운로드로](https://aka.ms/azurebackup_agent)바로 이동합니다.

1. 볼트에서 시작 **하기**에서 **백업을**선택합니다.

    ![백업 목표 열기](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. **워크로드가 실행 중인 위치**에서 **온-프레미스를**선택합니다. Azure VM에 MARS 에이전트를 설치하려는 경우에도 이 옵션을 선택합니다.
1. **백업할 내용**아래에서 파일 및 **폴더를 선택합니다.** **시스템 상태를**선택할 수도 있습니다. 다른 많은 옵션을 사용할 수 있지만 보조 백업 서버를 실행하는 경우에만 이러한 옵션이 지원됩니다. **인프라 준비를**선택합니다.

    ![파일 및 폴더 구성](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. **인프라 준비,** **복구 서비스 에이전트 설치에서**MARS 에이전트를 다운로드합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 다운로드 메뉴에서 **저장을**선택합니다. 기본적으로 *MARSagentinstaller.exe* 파일이 다운로드 폴더에 저장됩니다.

1. **최신 복구 서비스 에이전트를 이미 다운로드하거나 사용하여**선택한 다음 자격 증명 자격 증명을 다운로드합니다.

    ![저장소 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. **저장**을 선택합니다. 파일이 다운로드 폴더에 다운로드됩니다. 자격 증명 자격 증명 파일을 열 수 없습니다.

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

1. 백업하려는 컴퓨터에서 *MARSagentinstaller.exe* 파일을 실행합니다.
1. MARS 에이전트 설치 마법사에서 **설치 설정을**선택합니다. 여기서 에이전트를 설치할 위치를 선택하고 캐시의 위치를 선택합니다. 그런 다음 을 **선택합니다.**
   * Azure Backup은 캐시를 사용하여 데이터 스냅샷을 Azure로 보내기 전에 저장합니다.
   * 캐시 위치에는 백업할 데이터 크기의 5% 이상에 해당하는 여유 공간이 있어야 합니다.

    ![MARS 에이전트 설치 마법사에서 설치 설정 선택](./media/backup-configure-vault/mars1.png)

1. **프록시 구성의**경우 Windows 컴퓨터에서 실행되는 에이전트가 인터넷에 연결하는 방법을 지정합니다. 그런 다음 을 **선택합니다.**

   * 사용자 지정 프록시를 사용하는 경우 필요한 프록시 설정 및 자격 증명을 지정합니다.
   * 에이전트는 [특정 URL에](#before-you-start)액세스해야 합니다.

    ![MARS 마법사에서 인터넷 액세스 설정](./media/backup-configure-vault/mars2.png)

1. **설치를**위해 필수 구성 조건을 검토하고 **설치를**선택합니다.
1. 에이전트를 설치한 후 **등록 진행을**선택합니다.
1. 서버 마법사 > **볼트 ID** **등록에서**다운로드한 자격 증명 파일을 찾아보고 선택합니다. 그런 다음 을 **선택합니다.**

    ![서버 등록 마법사를 사용하여 자격 증명 추가](./media/backup-configure-vault/register1.png)

1. 암호화 **설정** 페이지에서 컴퓨터의 백업을 암호화하고 해독하는 데 사용할 암호를 지정합니다.

    * 암호를 안전한 위치에 저장합니다. 백업을 복원하려면 백업이 필요합니다.
    * 암호를 분실하거나 잊어버린 경우 Microsoft는 백업 데이터를 복구하는 데 도움을 줄 수 없습니다.

1. **마침**을 선택합니다. 이제 에이전트가 설치되고 컴퓨터가 볼트에 등록됩니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="next-steps"></a>다음 단계

[Azure 백업 MARS 에이전트를 사용하여 Windows 컴퓨터를 백업하는](backup-windows-with-mars-agent.md) 방법에 대해 알아봅니다.
