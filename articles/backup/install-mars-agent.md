---
title: MARS(Microsoft Azure Recovery Services) 에이전트 설치
description: MARS(Microsoft Azure Recovery Services) 에이전트를 설치하여 Windows 머신을 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/04/2021
ms.openlocfilehash: c52b65c06a4920020e4358c131870c0fe77b2584
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970626"
---
# <a name="install-the-azure-backup-mars-agent"></a>Azure Backup MARS 에이전트 설치

이 문서에서는 MARS(Microsoft Azure Recovery Services) 에이전트를 설치하는 방법을 설명 합니다. MARS는 Azure Backup 에이전트라고도 합니다.

## <a name="about-the-mars-agent"></a>MARS 에이전트 정보

Azure Backup은 MARS 에이전트를 사용하여 온-프레미스 머신 및 Azure VM에서 파일, 폴더 및 시스템 상태를 백업합니다. 해당 백업은 Azure의 Recovery Services 자격 증명 모음에 저장됩니다. 에이전트는 다음에서 실행할 수 있습니다.

* 직접 온-프레미스 Windows 머신. 이러한 머신은 Azure의 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
* Windows와 Azure VM 백업 확장을 함께 실행하는 Azure VM. 에이전트는 가상 머신에서 특정 파일 및 폴더를 백업합니다.
* MABS(Microsoft Azure Backup Server) 인스턴스 또는 System Center DPM(Data Protection Manager) 서버. 이 시나리오에서 머신 및 워크로드는 MABS 또는 Data Protection Manager에 백업합니다. 그런 다음, MABS 또는 Data Protection Manager는 MARS 에이전트를 사용하여 Azure의 자격 증명 모음에 백업합니다.

백업에 사용할 수 있는 데이터는 에이전트 설치 위치에 따라 다릅니다.

> [!NOTE]
> 일반적으로 VM에서 Azure Backup 확장을 사용하여 Azure VM을 백업합니다. 이 방법은 전체 VM을 백업합니다. VM의 특정 파일 및 폴더를 백업하려면 확장과 함께 MARS 에이전트를 설치하고 사용합니다. 자세한 내용은 [기본 제공 Azure VM 백업 아키텍처](backup-architecture.md#architecture-built-in-azure-vm-backup)를 참조하세요.

![Backup 프로세스 단계](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>시작하기 전에

* [Azure Backup이 MARS 에이전트를 사용하여 Windows 머신을 백업](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)하는 방법을 알아봅니다.
* 보조 MABS 또는 Data Protection Manager 서버에서 MARS 에이전트를 실행하는 [백업 아키텍처](backup-architecture.md#architecture-back-up-to-dpmmabs)에 대해 알아봅니다.
* MARS 에이전트에서 [지원되는 항목과 백업할 수 있는 항목](backup-support-matrix-mars-agent.md)을 검토합니다.
* 서버 또는 클라이언트를 Azure에 백업해야 할 경우 Azure 계정을 갖고 있어야 합니다. 계정이 없을 경우, 몇 분 내로 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* 백업하려는 머신의 인터넷 액세스를 확인합니다.
* MARS 에이전트 설치 및 구성을 수행하는 사용자는 보호해야 할 서버에 대한 로컬 관리자 권한을 갖고 있어야 합니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>스토리지 복제 수정

기본적으로 자격 증명 모음은 [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md#geo-redundant-storage)를 사용합니다.

* 자격 증명 모음이 기본 백업 메커니즘인 경우 GRS를 사용하는 것이 좋습니다.
* [LRS(로컬 중복 스토리지)](../storage/common/storage-redundancy.md#locally-redundant-storage)를 사용하여 Azure Storage 비용을 줄일 수 있습니다.

스토리지 복제 형식을 수정하려면 다음을 수행합니다.

1. 새 자격 증명 모음에서 **설정** 섹션의 **속성** 을 선택합니다.

1. **속성** 페이지의 **백업 구성** 에서 **업데이트** 를 선택합니다.

1. 스토리지 복제 형식을 선택하고 **저장** 을 선택합니다.

    ![백업 구성 업데이트](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 자격 증명 모음을 설정하고 백업 항목을 포함한 후에는 스토리지 복제 유형을 수정할 수 없습니다. 이 작업을 수행하려는 경우, 자격 증명 모음을 다시 만들어야 합니다.
>

### <a name="verify-internet-access"></a>인터넷 액세스 확인

[!INCLUDE [Configuring network connectivity](../../includes/backup-network-connectivity.md)]

## <a name="download-the-mars-agent"></a>MARS 에이전트 다운로드

MARS 에이전트를 다운로드하여 백업하려는 머신에 설치할 수 있습니다.

머신에 에이전트를 이미 설치한 경우, 최신 버전의 에이전트가 실행되고 있는지 확인합니다. Azure Portal에서 최신 버전을 찾거나 [다운로드](https://aka.ms/azurebackup_agent)로 직접 이동합니다.

1. 자격 증명 모음의 **시작** 에서 **백업** 을 선택합니다.

    ![백업 목표 열기](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. **워크로드가 실행되는 위치** 아래의 **온-프레미스** 를 선택합니다. Azure VM에 MARS 에이전트를 설치하려는 경우에도 이 옵션을 선택합니다.
1. **백업할 항목** 아래에서 **파일 및 폴더** 를 선택합니다. **시스템 상태** 를 선택할 수도 있습니다. 사용 가능한 여러 옵션이 있지만, 해당 옵션은 보조 백업 서버를 실행하는 경우에만 지원됩니다. **인프라 준비** 를 선택합니다.

    ![파일 및 폴더 구성](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. **인프라 준비** 의 경우, **Recovery Services 에이전트 설치** 에서 MARS 에이전트를 다운로드합니다.

    ![인프라 준비](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 다운로드 메뉴에서 **저장** 을 선택합니다. 기본적으로 *MARSagentinstaller.exe* 파일이 다운로드 폴더에 저장됩니다.

1. **이미 다운로드 또는 최신 Recovery Services 에이전트 사용** 을 선택한 다음 자격 증명 모음 자격 증명을 다운로드합니다.

    ![저장소 자격 증명 다운로드](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. **저장** 을 선택합니다. 파일이 다운로드 폴더에 다운로드됩니다. 자격 증명 모음 자격 증명 파일을 열 수 없습니다.

## <a name="install-and-register-the-agent"></a>에이전트 설치 및 등록

1. 백업하려는 머신에서 *MARSagentinstaller.exe* 파일을 실행합니다.
1. MARS 에이전트 설치 마법사에서 **설치 설정** 을 선택합니다. 에이전트를 설치할 위치를 선택하고 캐시 위치를 선택합니다. 그런 후 **다음** 을 선택합니다.
   * Azure Backup은 캐시를 사용해 데이터 스냅샷을 Azure로 보내기 전에 저장합니다.
   * 캐시 위치는 백업할 데이터 크기의 최소 5% 이상에 달하는 여유 공간을 보유하고 있어야 합니다.

    ![MARS 에이전트 설치 마법사에서 설치 설정을 선택합니다.](./media/backup-configure-vault/mars1.png)

1. **프록시 구성** 의 경우, Windows 머신에서 실행되는 에이전트의 인터넷 연결 방법을 지정합니다. 그런 후 **다음** 을 선택합니다.

   * 사용자 지정 프록시를 사용할 경우, 필요한 프록시 설정 및 자격 증명을 지정합니다.
   * 에이전트가 [특정 URL](#before-you-start)에 액세스할 수 있어야 합니다.

    ![MARS 마법사에서 인터넷 액세스 설정](./media/backup-configure-vault/mars2.png)

1. **설치** 의 경우, 필수 구성 요소를 검토한 다음, **설치** 를 선택합니다.
1. 에이전트가 설치되면 **등록 진행하기** 를 선택합니다.
1. **서버 등록 마법사** > **자격 증명 모음 식별** 에서 다운로드한 자격 증명 파일을 찾아서 선택합니다. 그런 후 **다음** 을 선택합니다.

    ![서버 등록 마법사를 사용하여 자격 증명 모음 자격 증명 추가](./media/backup-configure-vault/register1.png)

1. **암호화 설정** 페이지에서 머신 백업을 암호화하고 암호 해독 시 사용할 암호를 지정합니다. 허용되는 암호 문자에 대한 자세한 내용은 [여기를 참조](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-)하세요.

    * 안전한 위치에 암호를 저장합니다. 이 암호는 백업을 복원할 때 필요합니다.
    * 암호를 분실하거나 잊어버린 경우, Microsoft에서는 백업 데이터 복구를 지원할 수 없습니다.

1. **마침** 을 선택합니다. 이제 에이전트 설치가 완료되었고, 컴퓨터가 자격 증명 모음에 등록되었습니다. 백업을 구성하고 일정을 예약할 준비가 완료되었습니다.

## <a name="next-steps"></a>다음 단계

[Azure Backup MARS 에이전트를 사용하여 Windows 머신을 백업](backup-windows-with-mars-agent.md)하는 방법을 알아봅니다.
