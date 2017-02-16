---
title: "Resource Manager 배포 가상 컴퓨터를 백업하기 위한 환경 준비 | Microsoft Docs"
description: "환경이 Azure의 가상 컴퓨터를 백업할 준비가 되었는지 확인합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "백업; 백업;"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: 026bfabbf53b408d0e997c0fe21845c4b53ca8b0
ms.openlocfilehash: d8d0ade0b3088805576cfb3fdc78eb2e4fec698a


---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Resource Manager 배포 가상 컴퓨터를 백업하기 위한 환경 준비
> [!div class="op_single_selector"]
> * [Resource Manager 모델](backup-azure-arm-vms-prepare.md)
> * [클래식 모델](backup-azure-vms-prepare.md)
>
>

이 문서는 Resource Manager 배포 VM(가상 컴퓨터)을 백업하기 위한 환경을 준비하는 단계를 제공합니다. 절차에 나와있는 단계는 Azure 포털을 사용합니다.  

Azure Backup 서비스에는 VM을 보호하는 두 가지 유형의 자격 증명 모음이(백업 자격 증명 모음 및 복구 서비스 자격 증명 모음) 있습니다. 백업 자격 증명 모음은 클래식 배포 모델을 사용하여 배포된 VM을 보호합니다. 복구 서비스 자격 증명 모음은 **클래식으로 배포되거나 Resource Manager 배포 VM을 양쪽 모두** 보호합니다. Resource Manager 배포 VM을 보호하려면 복구 서비스 자격 증명 모음을 사용해야 합니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 클래식 배포 모델 VM 작업에 대한 자세한 내용은 [Azure 가상 컴퓨터를 백업하기 위한 환경 준비](backup-azure-vms-prepare.md) 를 참조하세요.
>
>

Resource Manager 배포 VM(가상 컴퓨터)을 보호하거나 백업할 수 있으려면, 다음과 같은 필수 구성 요소가 있어야 합니다.

* *VM과 같은 위치*에 복구 서비스 자격 증명 모음을 만듭니다(또는 기존 복구 서비스 자격 증명 모음을 식별함).
* 시나리오를 선택하고, 백업 정책을 정의하고, 보호할 항목을 정의합니다.
* 가상 컴퓨터에서 VM 에이전트 설치를 확인합니다.
* 네트워크 연결 확인

사용자 환경이 이러한 조건을 이미 갖춘 경우 [VM 문서 백업](backup-azure-vms.md)을 진행합니다. 필수 구성 요소를 하나라도 설정하거나 확인해야 하는 경우에는, 이 문서를 참고하여 해당 필수 구성 요소를 준비하는 단계를 수행합니다.

##<a name="supported-operating-system-for-backup"></a>지원되는 백업용 운영 체제
 * **Linux**: Azure 백업은 Core OS Linux를 제외한 [Azure 인증 배포 목록](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 을 지원합니다. _가상 컴퓨터에서 VM 에이전트를 사용할 수 있고 Python에 대한 지원이 지속하는 한 기타 Bring-Your-Own-Linux 배포도 작동합니다. 그러나 이러한 배포판을 백업에 대해서는 보증하지 않습니다._
 * **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>VM 백업 및 복원 시의 제한 사항
환경을 준비하기 전에, 제한 사항을 이해해 주시기 바랍니다.

* 16개 이상의 데이터 디스크가 있는 가상 컴퓨터의 백업은 지원되지 않습니다.
* 예약된 IP 주소가 있고 정의된 끝점이 없는 가상 컴퓨터의 백업은 지원되지 않습니다.
* Docker 확장을 포함한 Linux 가상 컴퓨터의 백업이 지원되지 않습니다.
* 백업 데이터는 VM에 연결된 네트워크 탑재된 드라이브를 포함하지 않습니다.
* 복원하는 동안 기존 가상 컴퓨터의 교체는 지원되지 않습니다. VM이 존재하는 경우 VM 복원을 시도하면, 복원 작업이 실패합니다.
* 지역 간 백업 및 복원은 지원되지 않습니다.
* Azure의 모든 공영 지역에 있는 가상 컴퓨터를 백업할 수 있습니다(지원되는 지역의 [검사 목록](https://azure.microsoft.com/regions/#services) 참조). 찾는 지역이 현재 지원되지 않는 경우, 자격 증명 모음을 만드는 동안 드롭다운 목록에 표시되지 않습니다.
* 다중 DC 구성의 일부인 도메인 컨트롤러(DC) VM 복원은 PowerShell을 통해서만 지원됩니다. [다중 DC 도메인 컨트롤러 복원](backup-azure-restore-vms.md#restoring-domain-controller-vms)에 대해 자세히 알아보세요.
* 다음과 같은 특수 네트워크 구성을 포함하는 가상 컴퓨터 복원은 PowerShell 통해서만 지원됩니다. UI에서 복원 워크플로를 사용하여 만든 VM은 복원 작업이 완료된 후 이러한 네트워크 구성을 갖지 않습니다. 자세한 내용은 [특수 네트워크 구성을 가진 VM 복원](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations)을 참조하세요.
  * 부하 분산 장치 구성에서의 가상 컴퓨터(내부 및 외부)
  * 다중의 예약된 IP 주소가 있는 가상 컴퓨터
  * 다중 네트워크 어댑터가 있는 가상 컴퓨터

## <a name="create-a-recovery-services-vault-for-a-vm"></a>VM에 대한 복구 서비스 자격 증명 모음 만들기
복구 서비스 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. 복구 서비스 자격 증명 모음에는 보호된 가상 컴퓨터와 연결된 백업 정책도 포함됩니다.

복구 서비스 자격 증명 모음을 만들려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **찾아보기**를 클릭하고 리소스 목록에서 **복구 서비스**를 입력합니다. 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **복구 서비스 자격 증명 모음**을 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다.
3. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 2단계](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    복구 서비스 자격 증명 모음 블레이드가 열리고 **이름**, **구독**, **리소스 그룹** 및 **위치**를 입력하라는 메시지가 표시됩니다.

    ![복구 서비스 자격 증명 모음 만들기 5단계](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. **구독** 을 클릭하여 사용 가능한 구독 목록을 볼 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 조직 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
6. **리소스 그룹**을 클릭하여 사용 가능한 리소스 그룹 목록을 표시하거나 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
7. **위치** 를 클릭하여 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 **반드시** 보호하려는 가상 컴퓨터와 동일한 지역에 있어야 합니다.

   > [!IMPORTANT]
   > VM이 있는 위치를 정확히 알지 못하는 경우 자격 증명 모음 만들기 대화 상자를 닫고 포털에서 가상 컴퓨터의 목록으로 이동합니다. 가상 컴퓨터가 여러 지역에 있으면 각 지역에 복구 서비스 자격 증명 모음을 만들어야 합니다. 첫 번째 위치에서 다음 위치로 이동하기 전에 자격 증명 모음을 만듭니다. 백업 데이터를 저장하기 위해 저장소 계정을 지정할 필요는 없습니다. 복구 서비스 자격 증명 모음 및 Azure 백업 서비스가 자동으로 처리합니다.
   >
   >
8. **만들기**를 클릭합니다. 복구 서비스 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 복구 서비스 자격 증명 모음 목록에 나타납니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

    자격 증명 모음을 만들었으므로 저장소 복제를 설정하는 방법에 대해 알아보십시오.

## <a name="set-storage-replication"></a>저장소 복제 설정
저장소 복제 옵션을 사용하면 지역 중복 저장소와 로컬 중복 저장소 중에서 선택할 수 있습니다. 기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. 기본 백업인 경우 지역 중복 저장소 옵션이 설정된 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우에는 로컬 중복 저장소를 선택합니다. [지역 중복](../storage/storage-redundancy.md#geo-redundant-storage) 및 [로컬 중복](../storage/storage-redundancy.md#locally-redundant-storage) 저장소 옵션에 대한 자세한 내용은 [Azure Storage 복제 개요](../storage/storage-redundancy.md)를 참조하세요.

저장소 복제 설정을 편집하려면

1. 자격 증명 모음 대시보드 및 설정 블레이드를 열 자격 증명 모음을 선택합니다. **설정** 블레이드가 열리지 않을 경우 자격 증명 모음 대시보드에서 **모든 설정**을 클릭합니다.
2. **설정** 블레이드에서 **백업 인프라** > **백업 구성**을 클릭하여 **백업 구성** 블레이드를 엽니다. **백업 구성** 블레이드에서 자격 증명 모음에 대한 저장소 복제 옵션을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    자격 증명 모음에 대한 저장소 옵션을 선택하면 자격 증명 모음이 있는 VM에 연결할 준비가 됩니다. 연결을 시작하려면 Azure 가상 컴퓨터를 검색하고 등록해야 합니다.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>백업 목표 선택, 정책 설정, 보호할 항목 정의
자격 증명 모음으로 VM을 등록하기 전에 검색 프로세스를 실행하여 구독에 추가된 새 가상 컴퓨터를 식별하도록 합니다. 프로세스는 클라우드 서비스 이름 및 지역과 같은 추가 정보와 함께 구독의 가상 컴퓨터 목록을 Azure에 쿼리합니다. Azure 포털에서 시나리오란 복구 서비스 자격 증명 모음에 저장할 항목을 가리킵니다. 정책은 복구 지점이 발생하는 빈도 및 시기에 대한 일정입니다. 정책에는 복구 지점의 보존 범위도 포함됩니다.

1. 복구 서비스 자격 증명 모음이 이미 열려 있으면 2단계를 진행합니다. 복구 서비스 자격 증명 모음이 열려 있지 않지만 Azure 포털에 있는 경우 허브 메뉴에서 **찾아보기**를 클릭합니다.

   * 리소스 목록에서 **복구 서비스**를 입력합니다.
   * 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **복구 서비스 자격 증명 모음**이 표시되면 클릭합니다.

     ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

     복구 서비스 자격 증명 모음의 목록이 표시됩니다.
   * 복구 서비스 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

     선택한 자격 증명 모음 대시보드가 열립니다.

     ![자격 증명 모음 블레이드 열기](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. 자격 증명 모음 대시보드 메뉴에서 **백업** 을 클릭하여 백업 블레이드를 엽니다.

    ![백업 블레이드 열기](./media/backup-azure-vms-first-look-arm/backup-button.png)

    블레이드가 열리면 백업 서비스에서 구독에 있는 모든 새 VM을 검색합니다.

    ![VM 검색](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. 백업 블레이드에서 **백업 목표** 를 클릭하여 백업 목표 블레이드를 엽니다.

    ![시나리오 블레이드 열기](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. 백업 목표 블레이드에서 **워크로드 실행 위치**를 Azure로 설정하고 **백업할 항목**을 가상 컴퓨터로 설정한 다음 **확인**을 클릭합니다.

    백업 목표 블레이드가 닫히고 백업 정책 블레이드가 열립니다.

    ![시나리오 블레이드 열기](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. 백업 정책 블레이드에서 자격 증명 모음에 적용할 백업 정책을 선택하고 **확인**을 클릭합니다.

    ![백업 정책 선택](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    상세 정보에 기본 정책에 대한 자세한 내용이 나열됩니다. 새 정책을 만들려는 경우 드롭다운 메뉴에서 **새로 만들기** 를 선택합니다. 드롭다운 메뉴는 스냅숏을 생성하는 시간을 오후 7시로 전환하는 옵션도 제공합니다. 백업 정책 정의에 대한 지침은 [백업 정책 정의](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)를 참조하세요. **확인**을 클릭하면 백업 정책이 자격 증명 모음과 연결됩니다.

    다음으로 자격 증명 모음과 연결할 VM을 선택합니다.
6. 지정된 정책과 연결할 가상 컴퓨터를 선택하고 **선택**을 클릭합니다.

    ![워크로드 선택](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    원하는 VM이 표시되지 않으면 복구 서비스 자격 증명 모음과 같은 Azure 위치에 있는지 확인하세요.
7. 이제 자격 증명 모음에 대한 모든 설정을 정의했으므로 백업 블레이드에서 페이지 맨 아래에 있는 **백업 사용** 을 클릭합니다. 그러면 정책이 자격 증명 모음 및 VM에 배포됩니다.

    ![백업 사용](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

다음 준비 단계는 VM 에이전트를 설치하거나 VM 에이전트가 설치되었는지 확인하는 단계입니다.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>가상 컴퓨터에 VM 에이전트 설치
Azure VM 에이전트는 작업할 백업 확장을 위한 Azure 가상 컴퓨터에 설치되어야 합니다. Azure 갤러리에서 VM을 만든 경우 VM 에이전트는 이미 가상 컴퓨터에 있습니다. 이 정보는 Azure 갤러리를 통해 생성된 VM을 사용하지 *않는* 상황을(예: 온-프레미스 데이터 센터에서 VM을 마이그레이션한 경우) 위해 제공됩니다. 이런 경우, 가상 컴퓨터를 보호하기 위해 VM 에이전트를 설치해야 합니다.

[VM 에이전트](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) 및 [VM 에이전트 설치 방법](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)에 대해 알아보세요.

Azure VM을 백업하는 데 문제가 있는 경우 Azure VM 에이전트가 가상 컴퓨터에 올바르게 설치되었는지 확인합니다(아래 테이블 참조). 사용자 지정 VM을 만든 경우 가상 컴퓨터를 프로비전하기 전에 [**VM 에이전트 설치** 확인란이 선택되어 있는지 확인해야 합니다](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

다음 테이블에서는 Windows 및 Linux VM용 VM 에이전트에 대한 추가 정보를 제공합니다.

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 |<li>[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li>[VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 하여 에이전트가 설치되었다고 표시합니다. |<li> GitHub에서 최신 [Linux 에이전트](https://github.com/Azure/WALinuxAgent) 를 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. <li> [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 하여 에이전트가 설치되었다고 표시합니다. |
| VM 에이전트 업데이트 |VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |[Linux VM 에이전트 업데이트 ](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침을 따르세요. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |
| VM 에이전트 설치 유효성 검사 |<li>Azure VM에서 *C:\WindowsAzure\Packages* 폴더로 이동합니다. <li>WaAppAgent.exe 파일을 찾습니다.<li> 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다. |해당 없음 |

### <a name="backup-extension"></a>백업 확장
가상 컴퓨터에 VM 에이전트를 설치하면 Azure 백업 서비스는 VM 에이전트에 대한 백업 확장을 설치합니다. Azure 백업 서비스가 백업 확장을 원활하게 업그레이드하고 패치합니다.

백업 확장은 VM의 실행 여부와 상관없이 백업 서비스에 의해 설치됩니다. 실행 중인 VM은 응용 프로그램 일치 복구 지점을 확보할 수 있는 큰 기회를 제공합니다. 그러나 Azure 백업 서비스는 VM이 꺼져 확장을 설치할 수 없더라도 VM을 계속 백업합니다. 오프라인 VM이라고 합니다. 이 경우 복구 지점은 *충돌 일치*가 됩니다.

## <a name="network-connectivity"></a>네트워크 연결
VM 스냅숏을 관리하려면, 백업 확장에 Azure 공용 IP 주소에 대한 연결이 필요합니다. 올바른 인터넷 연결이 없으면, 가상 컴퓨터의 HTTP 요청 시간이 초과되고 백업 작업이 실패합니다. 배포에 액세스 제한이 있다면(예: 네트워크 보안 그룹(NSG)을 통해), 백업 트래픽에 대해 명확한 경로를 제공하기 위해 이 옵션 중 하나를 선택합니다.

* [Azure 데이터 센터 IP 범위 허용 목록](http://www.microsoft.com/en-us/download/details.aspx?id=41653) - IP 주소 허용 목록을 만드는 방법에 대한 지침은 이 문서를 참조하세요.
* 트래픽 라우팅을 위해 HTTP 프록시 서버를 배포합니다.

사용할 옵션을 결정할 때는, 관리 효율성, 세부적인 제어 및 비용 사이에 균형을 유지합니다.

| 옵션 | 장점 | 단점 |
| --- | --- | --- |
| 허용 목록 IP 범위 |추가 비용 없음<br><br>NSG에서 액세스를 여는 경우 <i>Set-AzureNetworkSecurityRule</i> cmdlet을 사용합니다. |시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기가 복잡합니다.<br><br>저장소 뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다. |
| HTTP 프록시 |허용되는 저장소 URL에 걸친 프록시에서 세부적인 제어<br>VM에 대한 인터넷 액세스의 단일 지점<br>Azure IP 주소 변경이 적용되지 않음 |프록시 소프트웨어를 사용하여 VM을 실행하기 위한 추가 비용입니다. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위 허용 목록
Azure 데이터 센터 IP 범위의 허용 목록을 만들려면, [Azure 웹 사이트](http://www.microsoft.com/en-us/download/details.aspx?id=41653)에서 IP 범위에 대한 자세한 내용과 지침을 참조하세요.

### <a name="using-an-http-proxy-for-vm-backups"></a>VM 백업에 HTTP 프록시 사용
VM을 백업할 때, VM의 백업 확장이 HTTPS API를 사용하여 Azure 저장소에 스냅숏 관리 명령을 보냅니다. 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소이므로, HTTP 프록시를 통해 백업 확장 트래픽을 라우팅합니다.

> [!NOTE]
> 사용해야 할 프록시 소프트웨어에 대한 권장 사항은 없습니다. 아래의 구성 단계와 호환되는 프록시를 선택하도록 합니다.
>
>

아래 예제 이미지는 HTTP 프록시를 사용하는 데 필요한 세 가지 구성 단계를 보여줍니다.

* 앱 VM은 프록시 VM을 통해 공용 인터넷으로 향하는 모든 HTTP 트래픽을 라우팅합니다.
* 프록시 VM은 가상 네트워크의 VM에서 들어오는 트래픽을 허용합니다.
* NSF-lockdown이라는 이름의 네트워크 보안 그룹(NSG)에 프록시 VM의 아웃바운드 인터넷 트래픽을 허용하는 보안 규칙이 필요합니다.

![HTTP 프록시 배포 다이어그램을 사용하는 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

공용 인터넷 통신에 HTTP 프록시를 사용하려면, 다음 단계를 수행합니다.

#### <a name="step-1-configure-outgoing-network-connections"></a>1단계. 나가는 네트워크 연결 구성
###### <a name="for-windows-machines"></a>Windows 컴퓨터의 경우
로컬 시스템 계정에 대한 프록시 서버 구성을 설정합니다.

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. 관리자 권한 프롬프트에서 다음 명령을 실행합니다.

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Internet Explorer 창이 열립니다.
3. 도구 -> 인터넷 옵션 -> 연결 -> LAN 설정으로 이동합니다.
4. 시스템 계정에 대한 프록시 설정을 확인합니다. 프록시 IP 및 포트를 설정합니다.
5. Internet Explorer를 닫습니다.

시스템 수준의 프록시 구성을 설정하고 나가는 HTTP/HTTPS 트래픽에 사용됩니다.

현재 사용자 계정(로컬 시스템 계정이 아닌)으로 프록시 서버를 설정한 경우에는, 다음 스크립트를 사용하여 SYSTEMACCOUNT에 적용합니다.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> 프록시 서버 로그에 "(407)프록시 인증 필요"가 발견되면, 인증이 제대로 설정되었는지 확인합니다.
>
>

###### <a name="for-linux-machines"></a>Linux 컴퓨터의 경우
다음 줄을 ```/etc/environment``` 파일에 추가합니다.

```
http_proxy=http://<proxy IP>:<proxy port>
```

```/etc/waagent.conf``` 파일에 다음 줄을 추가합니다.

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2단계. 프록시 서버에서 들어오는 연결을 허용합니다.
1. 프록시 서버에서 Windows 방화벽을 엽니다. 방화벽에 액세스하는 가장 쉬운 방법은 고급 보안이 포함된 Windows 방화벽을 검색하는 것입니다.

    ![방화벽 열기](./media/backup-azure-vms-prepare/firewall-01.png)
2. Windows 방화벽 대화 상자에서 마우스 오른쪽 단추로 **인바운드 규칙**을 클릭한 다음 **새 규칙...**을 클릭합니다.

    ![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-02.png)
3. **새 인바운드 규칙 마법사**에서 **규칙 형식**에 **사용자 지정** 옵션을 선택하고 **다음**을 클릭합니다.
4. **프로그램**을 선택하려는 페이지에서 **모든 프로그램**을 선택하고 **다음**을 클릭합니다.
5. **프로토콜 및 포트** 페이지에서 다음 정보를 입력하고 **다음**을 클릭합니다.

    ![새 규칙 만들기](./media/backup-azure-vms-prepare/firewall-03.png)

   * *프로토콜 유형*에서 *TCP*를 선택합니다.
   * *로컬 포트*에서 *특정 포트*를 선택하고 아래의 필드에서 구성되어 있는 ```<Proxy Port>```를 지정합니다.
   * *원격 포트*에서 *모든 포트*를 선택합니다.

     마법사의 나머지 부분의 경우 끝까지 클릭하고 이 규칙에 이름을 지정합니다.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3단계. NSG에 예외 규칙 추가
Azure PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

다음 명령은 NSG에 예외를 추가합니다. 이 예외는 10.0.0.5의 모든 포트에서 오는 TCP 트래픽을 포트 80(HTTP) 또는 443(HTTPS)의 모든 인터넷 주소에 허용합니다. 공용 인터넷에 특정 포트가 필요하면, 해당 포트를 ```-DestinationPortRange```에도 추가해야 합니다.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*이 단계에서는 이 예제에 대해 특정 이름과 값을 사용합니다. 코드에 자세한 내용을 입력하거나, 잘라내거나, 붙여 넣는 경우, 배포의 이름과 값을 사용합니다.*

네트워크 연결이 준비되었으니, VM을 백업할 준비가 되었습니다. [Resource Manager 배포 VM 백업](backup-azure-arm-vms.md)을 참조하세요.

## <a name="questions"></a>질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>다음 단계
VM을 백업하기 위한 환경을 준비했으므로 이제 백업을 만들어야 합니다. 계획 문서에서는 VM 백업에 대한 보다 자세한 정보를 제공합니다.

* [가상 컴퓨터 설정](backup-azure-vms.md)
* [VM 백업 인프라 계획](backup-azure-vms-introduction.md)
* [가상 컴퓨터 백업 관리](backup-azure-manage-vms.md)



<!--HONumber=Dec16_HO3-->


