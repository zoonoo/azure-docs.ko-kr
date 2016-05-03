
<properties
	pageTitle="Azure 포털을 사용하여 백업에서 가상 컴퓨터 복원 | Microsoft Azure"
	description="Azure 포털을 사용하여 복구 지점에서 Azure 가상 컴퓨터 복원"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="백업 복원; 복원하는 방법; 복구 지점;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="trinadhk; jimpark;"/>


# Azure 포털을 사용하여 가상 컴퓨터 복원

> [AZURE.SELECTOR]
- [클래식 포털에서 VM 복원](backup-azure-restore-vms.md)
- [Azure 포털에서 VM 복원](backup-azure-arm-restore-vms.md)


정의된 간격으로 데이터의 스냅숏을 찍어서 백업 서비스로 데이터를 보호합니다. 이러한 스냅숏은 복구 지점이라고 하며 복구 서비스 자격 증명 모음에 저장됩니다. VM을 복구하거나 다시 빌드해야 하는 경우 또는 그 때에 저장된 복구 지점 중 하나에서 VM을 복원할 수 있습니다. 복구 지점을 복원하면 복구 지점을 만들 때의 상태로 VM을 반환하거나 되돌립니다. 이 문서에서는 VM을 복구하는 방법을 설명합니다.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../resource-manager-deployment-model.md) 모델이 있습니다. 이 문서에서는 리소스 관리자 모델을 사용하여 배포된 VM을 복원하기 위한 정보 및 절차를 제공합니다.



## 복구 지점 복원

1. [Azure 포털](http://ms.portal.azure.com/)에 로그인합니다.

2. Azure 메뉴에서 **찾아보기**를 클릭하고 서비스 목록에서 **복구 서비스**를 입력합니다. 서비스 목록은 입력하는 항목에 조정됩니다. **복구 서비스 자격 증명 모음**이 표시되면 이를 선택합니다.

    ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    구독에서 자격 증명 모음의 목록이 표시됩니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. 목록에서 복원하려는 VM과 연결된 자격 증명 모음을 선택합니다. 자격 증명 모음을 클릭하면 해당 대시보드가 열립니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. 이제 자격 증명 모음 대시보드입니다. **백업 항목** 타일에서 **Azure 가상 컴퓨터**를 클릭하여 자격 증명 모음에 연결된 VM을 표시합니다.

    ![자격 증명 모음 대시보드](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **백업 항목** 블레이드가 열리고 Azure 가상 컴퓨터의 목록을 표시합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. 목록에서 VM을 선택하여 대시보드를 엽니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault-selected.png)

    복구 지점 타일을 포함하는 모니터링 영역에 VM 대시보드가 열립니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. VM 대시보드 메뉴에서 **복원**을 클릭합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    복원 블레이드가 열립니다.

    ![복원 블레이드](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. **복원** 블레이드에서 **복구 지점**을 클릭하여 **복구 지점 선택** 블레이드를 엽니다.

    ![복원 블레이드](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    기본적으로 대화 상자는 지난 30일 동안의 모든 복구 지점을 표시합니다. 원하는 **기간**, **연도** 및 **복원 지점 일관성** 목록을 편집합니다. 복원 지점의 각 형식에 대한 자세한 내용은 [데이터 일관성](./backup-azure-vms-introduction.md#data-consistency)의 설명을 참조하세요.
    - **기간**은 특정 연도의 개월 또는 최근 30일입니다.
    - **연도**는 특정 연도입니다.
    - 이 목록에서 **복원 지점 일관성**은 다음을 선택합니다.
        - 충돌 일관성 복원 지점,
        - 응용 프로그램 일관성 복원 지점,
        - 파일 시스템 일관성 복원 지점,
        - 모든 복원 지점.  

8. 복구 지점을 선택하고 **확인**을 클릭합니다.

    ![복구 지점 선택](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **복원** 블레이드는 복구 지점이 설정된 것을 보여줍니다.

    ![복구 지점이 설정됩니다](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. **복원** 블레이드에서 **복구 구성**을 클릭하여 해당 블레이드를 엽니다.

    ![복구 구성 마법사가 설정됩니다](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## VM 복구 구성 선택

복구 지점을 선택했으므로 복구 VM을 구성하기 위한 저장소 계정 및 메서드를 선택합니다. 복구 VM을 구성하기 위해 Azure 포털 또는 PowerShell을 사용하도록 선택합니다. 저장소 계정을 선택할 경우 복구 서비스 자격 증명 모음과 동일한 위치를 공유하는 계정에서 선택해야 합니다. 영역이 중복된 저장소 계정은 지원되지 않습니다. 복구 서비스 자격 증명 모음과 동일한 위치에 있는 저장소 계정이 없는 경우 복원 작업을 시작하기 전에 계정을 만들어야 합니다.

1. 아직 수행하지 않은 경우 **복원** 블레이드로 이동합니다. **복구 지점**을 선택하고 **복구 구성**을 클릭하여 **복구 구성** 블레이드를 열도록 합니다.

    ![복구 구성 마법사가 설정됩니다](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. **복구 구성** 블레이드에서 **저장소 계정**을 클릭하여 복구 서비스 자격 증명 모음과 동일한 위치에 저장소 계정 목록을 엽니다.

3. 목록에서 저장소 계정을 선택하고 확인을 클릭합니다.

    ![저장소 계정 목록](./media/backup-azure-arm-restore-vms/list-of-storage-accounts.png)

    **복구 구성** 블레이드에서 선택한 저장소 계정은 **저장소 계정** 대화 상자에 나타납니다.

    ![저장소 계정 선택](./media/backup-azure-arm-restore-vms/selected-storage-account.png)

4. 포털에서 VM을 복원하는 경우 기본 ARM 또는 클래식 VM을 구성할 수 있습니다. 다음 목록의 옵션 중 하나와 같은 복잡한 구성을 복원하려는 경우 PowerShell을 사용하여 디스크에서 VM을 구성해야 합니다. 복잡한 구성을 구성하지 않은 경우 5단계로 건너뜁니다.

    ![복잡한 VM 구성 목록](./media/backup-azure-arm-restore-vms/complex-vm-configurations.png)

    복원 구성이 복잡한 것으로 간주*되는* 경우 **복구 구성** 블레이드에서 **확인**을 클릭합니다. **복구 구성** 블레이드를 닫고 복구 구성 옆에 확인 표시가 나타납니다. **복원**을 클릭하여 디스크 복원 작업을 시작합니다. 5단계를 진행하지 마세요. 대신에 [특수한 네트워크 구성으로 VM 복원](#restoring-vms-with-special-network-configurations)을 진행합니다.

    ![구성된 복원](./media/backup-azure-arm-restore-vms/restore-configured.png)

5. **복구 구성** 블레이드에서 **가상 컴퓨터 구성**을 클릭하여 **VM 만들기** 블레이드를 엽니다.

    ![VM 블레이드 만들기 열기](./media/backup-azure-arm-restore-vms/recovery-configuration-create-vm.png)

6. **VM 만들기** 블레이드에서 다음 필드 각각에 값을 입력하거나 선택합니다.

    - **가상 컴퓨터 이름** - VM에 대한 이름을 제공합니다. 이름은 리소스 그룹(ARM VM의 경우) 또는 클라우드 서비스(클래식 VM의 경우)에 대해 고유해야 합니다. 기존 VM을 같은 이름으로 대체하려는 경우 먼저 기존 VM 및 데이터 디스크를 삭제하고 Azure 백업에서 데이터를 복원합니다.
    - **리소스 그룹** – 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 클래식 VM을 복원하는 경우 이 필드를 사용하여 새 클라우드 서비스의 이름을 지정합니다. 새 리소스 그룹/클라우드 서비스를 만들 경우 이름은 전역적으로 고유해야 합니다. 일반적으로 클라우드 서비스 이름은 공용 URL(예: [cloudservice].cloudapp.net)과 연결됩니다. 이미 사용되는 클라우드 리소스 그룹/클라우드 서비스에 대한 이름을 사용하려는 경우 Azure는 리소스 그룹/클라우드 서비스에 VM과 동일한 이름을 할당합니다. Azure는 선호도 그룹에 연결되지 않은 리소스 그룹/클라우드 서비스 및 VM을 표시합니다. 자세한 내용은 [선호도 그룹에서 지역 VNet(가상 네트워크)으로 마이그레이션하는 방법](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)을 참조하세요.
    - **가상 네트워크** - VM을 만들 때 VNET(가상 네트워크)을 선택합니다. 필드는 구독과 연결된 모든 VNET을 제공합니다.

    > [AZURE.NOTE] ARM 기반으로 하는 VM을 복원하는 경우 VNET을 선택해야 합니다. VNET은 클래식 VM에 대한 선택 사항입니다.

    - **서브넷** - VNET에 서브넷이 있는 경우 기본적으로 첫 번째 서브넷을 선택합니다. 추가 서브넷이 있으면 원하는 서브넷을 선택합니다.

7. **VM 만들기** 블레이드에서 **확인**을 클릭하여 구성을 완료합니다.

    **VM 만들기** 블레이드를 닫으면 **복구 구성** 블레이드에서 새 VM의 이름을 표시합니다.

    ![복구 구성 완료](./media/backup-azure-arm-restore-vms/recovery-configuration-complete.png)

8. **복구 구성** 블레이드에서 **확인**을 클릭하여 복원 구성을 완료합니다.

9. **복원** 블레이드에서 **복원**을 클릭하여 복원 작업을 트리거합니다.

    ![복구 구성 완료](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## 복원 작업 추적

복원 작업을 트리거하면 백업 서비스는 복원 작업을 추적하기 위한 작업을 만듭니다. 또한 백업 서비스는 알림을 만들고 일시적으로 표시합니다.

![트리거된 복원](./media/backup-azure-arm-restore-vms/restore-triggered.png)

알림이 표시되지 않으면 언제든 알림 아이콘을 클릭하여 알림을 볼 수 있습니다.

![트리거된 복원](./media/backup-azure-arm-restore-vms/restore-notification.png)

처리하는 동안 작업을 보려거나 완료되는 때를 보려면 백업 작업 목록을 엽니다.

1. Azure 메뉴에서 **찾아보기**를 클릭하고 서비스 목록에서 **복구 서비스**를 입력합니다. 서비스 목록은 입력하는 항목에 조정됩니다. **복구 서비스 자격 증명 모음**이 표시되면 이를 선택합니다.

    ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    구독에서 자격 증명 모음의 목록이 표시됩니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. 목록에서 복원한 VM과 연결된 자격 증명 모음을 선택합니다. 자격 증명 모음을 클릭하면 해당 대시보드가 열립니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/select-vault-open-vault-jobs.png)

3. 자격 증명 모음 대시보드의 **백업 작업** 타일에서 **Azure 가상 컴퓨터**를 클릭하여 자격 증명 모음에 연결된 작업을 표시합니다.

    ![자격 증명 모음 대시보드](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **백업 작업** 블레이드가 열리고 작업 목록을 표시합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)




## 특수 네트워크 구성을 가진 Vm 복원
다음과 같은 특수한 네트워크 구성으로 VM을 백업하고 복원하는 것이 가능합니다. 그러나 복원 프로세스를 수행하는 동안 이러한 구성에는 몇몇 특별한 주의가 필요합니다.

- 부하 분산 장치에서의 VM(내부 및 외부)
- 다중의 예약된 IP가 있는 VM
- 다중 NIC가 있는 VM

>[AZURE.IMPORTANT] VM에 대한 특수한 네트워크 구성을 만들 경우 PowerShell을 사용하여 디스크에서 복원해야 합니다.


디스크에 복원한 후에 가상 컴퓨터를 다시 만들기 위해 다음 단계를 수행합니다.

1. [Azure 백업 PowerShell](../backup-azure-vms-automation.md#restore-an-azure-vm)을 사용하여 복구 서비스 백업 자격 증명 모음에서 디스크 복원

2. PowerShell cmdlet을 사용하여 부하 분산 장치/다중 NIC/다중 예약된 IP에 필요한 VM 구성을 만들어 원하는 구성의 VM을 만드는 데 사용합니다.
	- [내부 부하 분산 장치](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)를 사용하여 클라우드 서비스에서 VM 만들기
	- [인터넷 연결 부하 분산 장치](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)에 연결할 VM 만들기
	- [다중 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)를 사용하여 VM 만들기
	- [다중의 예약된 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)가 있는 VM 만들기

## 다음 단계
VM을 복원할 수 있습니다. VM의 일반적인 오류에 대한 정보에 대한 문제 해결 문서를 참조하세요. 또한 VM을 사용하는 작업 관리에 대한 문서를 확인합니다.

- [문제 해결](backup-azure-vms-troubleshoot.md#restore)
- [가상 컴퓨터 관리](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->