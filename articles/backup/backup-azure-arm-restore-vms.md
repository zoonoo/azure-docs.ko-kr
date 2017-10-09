---
title: "Azure 백업: Azure Portal을 사용하여 가상 컴퓨터 복원 | Microsoft Docs"
description: "Azure 포털을 사용하여 복구 지점에서 Azure 가상 컴퓨터 복원"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "백업 복원; 복원하는 방법; 복구 지점;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: c4723a7d174991f5bf679bf3e8c97fe2338926a2
ms.contentlocale: ko-kr
ms.lasthandoff: 09/29/2017

---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Azure 포털을 사용하여 가상 컴퓨터 복원
> [!div class="op_single_selector"]
> * [클래식 포털에서 VM 복원](backup-azure-restore-vms.md)
> * [Azure 포털에서 VM 복원](backup-azure-arm-restore-vms.md)
>
>

정의된 간격으로 데이터의 스냅숏을 찍어 데이터를 보호합니다. 이러한 스냅숏은 복구 지점이라고 하며 복구 서비스 자격 증명 모음에 저장됩니다. VM을 복구하거나 다시 빌드해야 하는 경우 또는 그 때에 저장된 복구 지점 중 하나에서 VM을 복원할 수 있습니다. 복구 지점을 복원하면 백업 VM의 특정 시점을 나타내는 새 VM을 만들거나 복원된 VM의 사용자 지정 또는 개별 파일 복구와 함께 제공되는 디스크를 복원하고 템플릿을 사용할 수 있습니다. 이 문서에서는 새 VM에 VM을 복원하거나 모든 백업된 디스크를 복원하는 방법을 설명합니다. 개별 파일 복구는 [Azure VM 백업에서 파일 복구](backup-azure-restore-files-from-vm.md)를 참조하세요.

![3-ways-restore-from-vm-backup](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 이 문서에서는 Resource Manager 모델을 사용하여 배포된 VM을 복원하기 위한 정보 및 절차를 제공합니다.
>
>

VM 백업에서 VM 또는 모든 디스크를 복원하는 작업은 다음과 같은 두 가지 단계를 포함합니다.

1. 복원을 위해 복원 지점 선택
2. 복원 유형 선택 - 새 VM을 만들거나 디스크를 복원하고 필수 매개 변수를 지정합니다. 

## <a name="select-restore-point-for-restore"></a>복원을 위해 복원 지점 선택
1. [Azure 포털](http://portal.azure.com/)
2. Azure 메뉴에서 **찾아보기**를 클릭하고 서비스 목록에서 **Recovery Services**를 입력합니다. 서비스 목록은 입력하는 항목에 조정됩니다. **복구 서비스 자격 증명 모음**이 표시되면 이를 선택합니다.

    ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    구독에서 자격 증명 모음의 목록이 표시됩니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. 목록에서 복원하려는 VM과 연결된 자격 증명 모음을 선택합니다. 자격 증명 모음을 클릭하면 해당 대시보드가 열립니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. 이제 자격 증명 모음 대시보드입니다. **백업 항목** 타일에서 **Azure Virtual Machines**를 클릭하여 자격 증명 모음에 연결된 VM을 표시합니다.

    ![자격 증명 모음 대시보드](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **백업 항목** 블레이드가 열리고 Azure 가상 컴퓨터의 목록을 표시합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. 목록에서 VM을 선택하여 대시보드를 엽니다. 복원 지점 타일을 포함하는 모니터링 영역에 VM 대시보드가 열립니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. VM 대시보드 메뉴에서 **복원**

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    복원 블레이드가 열립니다.

    ![복원 블레이드](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. **복원** 블레이드에서 **복원 지점**을 클릭하여 **복원 지점 선택** 블레이드를 엽니다.

    ![복원 블레이드](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    기본적으로 대화 상자는 지난 30일 동안의 모든 복원 지점을 표시합니다. **필터** 옵션을 사용하여 표시되는 복원 지점의 범위를 변경합니다. 기본적으로, 모든 일관성의 복원 지점이 표시됩니다. **모든 복원 지점** 필터를 수정하여 복원 지점에 대한 특정 일관성을 선택합니다. 복원 지점의 각 형식에 대한 자세한 내용은 [데이터 일관성](backup-azure-vms-introduction.md#data-consistency)의 설명을 참조하세요.  

   * **복원 지점 일관성** 은 다음을 선택합니다.
     * 충돌 일관성 복원 지점,
     * 응용 프로그램 일관성 복원 지점,
     * 파일 시스템 일관성 복원 지점,
     * 모든 복원 지점.  
8. 복원 지점을 선택하고 **확인**을 클릭합니다.

    ![복원 지점 선택](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **복원** 블레이드에 복원 지점이 설정된 것이 표시됩니다

    ![복원 지점이 설정됨](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. **복원** 블레이드에, 복원 지점이 설정된 후에 **복원 구성**이 자동으로 열립니다.

## <a name="choosing-a-vm-restore-configuration"></a>VM 복원 구성 선택
복원 지점을 선택했으니, 복원 VM에 대한 구성을 선택합니다. 복원 VM을 구성하기 위해 Azure 포털 또는 PowerShell을 사용하도록 선택합니다.

1. 아직 수행하지 않은 경우 **복원** 블레이드로 이동합니다. [복원 지점](#select-restore-point-for-restore)을 선택하고 **복원 구성**을 클릭하여 **복원 구성** 블레이드를 엽니다.

    ![복구 구성 마법사가 설정됩니다](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. **복원 구성** 블레이드에는 다음 두 가지 옵션이 있습니다.
   * 전체 가상 컴퓨터 복원
   * 백업된 디스크 복원

포털은 복원된 VM에 대해 빨리 만들기 옵션을 제공합니다. 새 VM을 만드는 작업의 일부로 생성된 VM 구성 또는 리소스의 이름을 사용자 지정하려는 경우 PowerShell 또는 포털을 사용하여 백업된 디스크를 복원하고 PowerShell 명령을 사용하여 선택한 VM 구성에 연결하거나 복원 디스크와 함께 제공되는 템플릿을 사용하여 복원된 VM을 사용자 지정합니다. 여러 NIC가 있는 VM을 복원하는 방법 또는 부하 분산 장치에 대한 자세한 내용은 [특수한 네트워크 구성을 사용하여 VM 복원](#restoring-vms-with-special-network-configurations)을 참조하세요. Windows VM이 [HUB 라이선스](../virtual-machines/windows/hybrid-use-benefit-licensing.md)를 사용하는 경우 복원된 VM에서 HUB 혜택을 사용할 수 있도록 VM을 만드는 동안 디스크를 복원하고 PowerShell/템플릿을 아래에서 지정된 대로 사용하여 VM을 만들고 LicenseType을 "Windows_Server"로 지정했는지 확인해야 합니다. 
 
## <a name="create-a-new-vm-from-restore-point"></a>복원 지점에서 새 VM 만들기
아직 수행하지 않은 경우 복원 지점에서 새 VM을 만들기 전에 [복원 지점을 선택합니다](#restoring-vms-with-special-network-configurations). 복원 지점을 선택하면 **복원 구성** 블레이드에서 다음 필드에 대해 값을 입력하거나 선택합니다.

* **복원 유형** - 가상 컴퓨터를 만듭니다.
* **가상 컴퓨터 이름** - VM의 이름을 제공합니다. 이름은 리소스 그룹(Resource Manager 배포 VM의 경우) 또는 클라우드 서비스(클래식 VM의 경우)에 대해 고유해야 합니다. 구독에 이미 있는 경우 가상 컴퓨터를 교체할 수 없습니다.
* **리소스 그룹** – 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 클래식 VM을 복원하는 경우 이 필드를 사용하여 새 클라우드 서비스의 이름을 지정합니다. 새 리소스 그룹/클라우드 서비스를 만들 경우 이름은 전역적으로 고유해야 합니다. 일반적으로 클라우드 서비스 이름은 공용 URL(예: [cloudservice].cloudapp.net)과 연결됩니다. 이미 사용되는 클라우드 리소스 그룹/클라우드 서비스에 대한 이름을 사용하려는 경우 Azure는 리소스 그룹/클라우드 서비스에 VM과 동일한 이름을 할당합니다. Azure는 선호도 그룹에 연결되지 않은 리소스 그룹/클라우드 서비스 및 VM을 표시합니다. 자세한 내용은 [선호도 그룹에서 지역 VNet(가상 네트워크)으로 마이그레이션하는 방법](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)을 참조하세요.
* **가상 네트워크** - VM을 만들 때 VNET(가상 네트워크)을 선택합니다. 필드는 구독과 연결된 모든 VNET을 제공합니다. VM의 리소스 그룹이 괄호 안에 표시됩니다.
* **서브넷** - VNET에 서브넷이 있는 경우 첫 번째 서브넷이 기본적으로 선택됩니다. 추가 서브넷이 있으면 원하는 서브넷을 선택합니다.
* **저장소 계정** - 이 메뉴는 복구 서비스 자격 증명 모음과 동일한 위치의 저장소 계정을 나열합니다. 영역이 중복된 저장소 계정은 지원되지 않습니다. 복구 서비스 자격 증명 모음과 동일한 위치에 있는 저장소 계정이 없는 경우 복원 작업을 시작하기 전에 계정을 만들어야 합니다. 저장소 계정의 복제 유형이 괄호 안에 언급됩니다.

![복원 구성 마법사가 설정됨](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

> [!NOTE]
> 1. Resource Manager 배포 VM을 복원하는 경우 VNET(가상 네트워크)을 식별해야 합니다. 클래식 VM의 경우 VNET(가상 네트워크)은 선택 사항입니다.
> 2. 관리 디스크를 사용하여 VM을 복원하는 경우 선택한 저장소 계정이 해당 수명 동안 SSE(저장소 서비스 암호화)에 대해 활성화되어 있지 않은지 확인하세요.
> 3. 선택한 저장소 계정 유형(프리미엄 또는 표준)에 따라 복원된 모든 디스크는 프리미엄 또는 표준 디스크 중 하나가 됩니다. 현재는 복원할 때 디스크 모드를 혼합해서 사용할 수 없습니다.  
>
>

**복원 구성** 블레이드에서 **확인**을 클릭하여 복원 구성을 완료합니다. **복원** 블레이드에서 **복원**을 클릭하여 복원 작업을 트리거합니다.

## <a name="restore-backed-up-disks"></a>백업된 디스크 복원
만들려는 가상 컴퓨터를 복원 구성 블레이드가 아닌 백업된 디스크에서 사용자 지정하려면 **복원 유형**의 값으로 **디스크 복원**을 선택합니다. 이렇게 선택하면 백업 디스크를 복사할 저장소 계정을 요청합니다. 저장소 계정을 선택할 경우 복구 서비스 자격 증명 모음과 동일한 위치를 공유하는 계정을 선택합니다. 영역이 중복된 저장소 계정은 지원되지 않습니다. 복구 서비스 자격 증명 모음과 동일한 위치에 있는 저장소 계정이 없는 경우 복원 작업을 시작하기 전에 계정을 만들어야 합니다. 저장소 계정의 복제 유형이 괄호 안에 언급됩니다.

복원 작업이 완료된 후에 다음을 수행할 수 있습니다.
* [템플릿을 사용하여 복원된 VM 사용자 지정](#use-templates-to-customize-restore-vm)
* [복원된 디스크를 사용하여 기존 가상 컴퓨터에 연결](../virtual-machines/windows/attach-managed-disk-portal.md)
* [PowerShell을 사용하여 복원된 디스크에서 새 가상 컴퓨터를 만듭니다.](./backup-azure-vms-automation.md#restore-an-azure-vm)

**복원 구성** 블레이드에서 **확인**을 클릭하여 복원 구성을 완료합니다. **복원** 블레이드에서 **복원**을 클릭하여 복원 작업을 트리거합니다.

![복구 구성 완료](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>복원 작업 추적
복원 작업을 트리거하면 백업 서비스는 복원 작업을 추적하기 위한 작업을 만듭니다. 백업 서비스는 알림을 만들고 포털의 알림 영역에 일시적으로 표시합니다. 알림이 표시되지 않으면 언제든 알림 아이콘을 클릭하여 알림을 볼 수 있습니다.

![트리거된 복원](./media/backup-azure-arm-restore-vms/restore-notification.png)

처리하는 동안 작업을 보려거나 완료되는 때를 보려면 백업 작업 목록을 엽니다.

1. Azure 메뉴에서 **찾아보기**를 클릭하고 서비스 목록에서 **Recovery Services**를 입력합니다. 서비스 목록은 입력하는 항목에 조정됩니다. **복구 서비스 자격 증명 모음**이 표시되면 이를 선택합니다.

    ![복구 서비스 자격 증명 모음 열기](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    구독에서 자격 증명 모음의 목록이 표시됩니다.

    ![복구 서비스 자격 증명 모음 목록](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. 목록에서 복원한 VM과 연결된 자격 증명 모음을 선택합니다. 자격 증명 모음을 클릭하면 해당 대시보드가 열립니다.
3. **백업 작업** 타일의 자격 증명 모음 대시보드에서 **Azure Virtual Machines**를 클릭하여 자격 증명 모음에 연결된 작업을 표시합니다.

    ![자격 증명 모음 대시보드](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **백업 작업** 블레이드가 열리고 작업 목록이 표시됩니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-restore-vm"></a>템플릿을 사용하여 복원된 VM 사용자 지정
[디스크 복원 작업이 완료되면](#Track-the-restore-operation) 복원 작업의 일부로 생성된 템플릿을 사용하여 백업 구성에서 다른 구성으로 새 VM을 만들거나 복원 지점에서 새 VM을 만들 때 생성된 리소스의 이름을 사용자 지정할 수 있습니다. 

> [!NOTE]
> 템플릿은 2017년 3월 1일 이후에 만든 복구 지점에 디스크 복원의 일부로 추가됩니다. 관리되지 않는 디스크 VM에 해당합니다. 관리 디스크 VM에 대한 지원은 곧 출시될 릴리스에서 제공됩니다. 
>
>

디스크 복원 옵션의 일부로 생성된 템플릿을 가져오려면

1. 작업에 해당하는 복원 작업 세부 정보로 이동합니다. 
2. 복원 작업 세부 정보 화면에서 *배포 템플릿* 단추를 클릭하여 템플릿 배포를 시작합니다. 

     ![복원 작업 드릴 다운](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
사용자 지정 배포를 위해 배포 템플릿 블레이드에서 템플릿 배포를 사용하여 [템플릿을 편집하고 배포](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)하거나 배포하기 전에 [템플릿을 작성](../azure-resource-manager/resource-group-authoring-templates.md)하여 사용자 지정을 추가합니다. 

   ![템플릿 배포 로딩](./media/backup-azure-arm-restore-vms/loading-template.png)
   
필수 값을 입력한 후에 *약관*에 동의하고 **구매**를 클릭합니다.

   ![템플릿 배포 제출](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>복원 후 단계
* Ubuntu와 같은 클라우드 초기화 기반 Linux 배포를 사용하는 경우 보안상의 이유로 복원 후 암호를 차단합니다. 복원된 VM에서 VMAccess 확장을 사용하여 [암호를 재설정](../virtual-machines/linux/classic/reset-access.md)하세요. 복원 후 암호를 다시 설정하지 않으려면 이러한 배포에서 SSH 키를 사용하는 것이 좋습니다.
* 백업 구성 중에 나타나는 확장을 설치하지만 사용할 수 없습니다. 문제가 있는 경우 확장을 다시 설치하세요. 
* 백업 VM에 고정 IP, post 복원이 있는 경우 복원된 VM을 만들 때 충돌을 방지하기 위해 복원된 VM는 동적 IP를 갖습니다. [복원된 VM에 고정 IP를 추가](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)할 수 있는 방법에 대한 자세한 내용
* 복원된 VM에는 가용성 값 집합이 없습니다. 복원된 디스크를 사용하여 PowerShell 또는 템플릿에서 VM을 만드는 경우 복원 디스크 옵션을 사용하고 [가용성 집합을 추가](../virtual-machines/windows/tutorial-availability-sets.md)하는 것이 좋습니다. 


## <a name="backup-for-restored-vms"></a>복원된 VM에 대한 백업
원래 백업한 VM과 같은 이름으로 같은 리소스 그룹에 VM을 복원하면, 백업이 VM 사후 복원에 계속 진행됩니다. VM을 다른 리소스 그룹에 복원하거나, 복원된 VM에 다른 이름을 지정하면, 새 VM으로 간주되어 복원된 VM에 대한 백업을 설정해야 합니다.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Azure 데이터 센터 재해 중 VM 복원
백업 자격 증명 모음을 지리적으로 중복되도록 구성해 놓은 경우, VM이 실행되는 기본 데이터 센터에 재해가 발생하면, Azure 백업을 통해 쌍을 이루는 데이터 센터에 백업한 VM을 복원할 수 있습니다. 이러한 시나리오가 발생하면, 쌍을 이루는 데이터 센터에 존재하는 저장소 계정을 선택해야 하며 나머지 복원 프로세스는 동일합니다. Azure 백업은 쌍을 이루는 지역의 계산 서비스를 사용하여 복원된 가상 컴퓨터를 생성합니다. [Azure 데이터 센터 복원력](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)에 대한 자세한 정보

## <a name="restoring-domain-controller-vms"></a>도메인 컨트롤러 VM 복원
DC(도메인 컨트롤러) 가상 컴퓨터 백업은 Azure 백업을 사용하는 지원되는 시나리오입니다. 그러나 복원 프로세스 동안 주의해야 합니다. 올바른 복원 프로세스는 도메인의 구조에 따라 다릅니다. 단순한 경우에 단일 도메인에 단일 DC가 있기도 하지만, 일반적으로 프로덕션 로드에서는 단일 도메인에 여러 DC가 있고, 일부 DC는 온-프레미스에 있을 수 있습니다. 여러 도메인이 있는 포리스트도 있을 수 있습니다. 

Active Directory 관점에서 Azure VM은 지원되는 최신 하이퍼바이저의 다른 VM과 비슷합니다. 온-프레미스 하이퍼바이저와의 주된 차이점은 Azure에서는 사용할 수 없는 VM 콘솔이 없다는 것입니다. BMR(완전 복구) 유형 백업을 사용한 복구와 같은 특정 시나리오에서는 콘솔이 필요합니다. 하지만 백업 자격 증명 모음의 VM 복원이 BMR을 완전히 대체합니다. Active Directory 복원 모드(DSRM)도 사용할 수 있으므로 모든 Active Directory 복구 모드를 실행할 수 있습니다. 자세한 배경 정보는 [Backup and Restore considerations for virtualized Domain Controllers](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers)(가상화된 도메인 컨트롤러에 대한 백업 및 복원 고려 사항) 및 [Planning for Active Directory Forest Recovery](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx)(Active Directory 포리스트 복구 계획)를 참조하세요.

### <a name="single-dc-in-a-single-domain"></a>단일 도메인의 단일 DC
Azure 포털에서 또는 PowerShell을 사용하여 다른 VM과 마찬가지로 VM을 복원할 수 있습니다.

### <a name="multiple-dcs-in-a-single-domain"></a>단일 도메인의 여러 DC
네트워크를 통해 동일한 도메인의 다른 DC에 연결할 수 있는 경우 DC를 VM처럼 복원할 수 있습니다. 도메인에 남은 마지막 DC이거나 격리된 네트워크에서 복구를 수행하는 경우에는 포리스트 복구 절차를 따라야 합니다.

### <a name="multiple-domains-in-one-forest"></a>한 포리스트의 여러 도메인
네트워크를 통해 동일한 도메인의 다른 DC에 연결할 수 있는 경우 DC를 VM처럼 복원할 수 있습니다. 그러나 다른 모든 경우에 포리스트 복구를 사용하는 것이 좋습니다.

## <a name="restoring-vms-with-special-network-configurations"></a>특수 네트워크 구성을 가진 Vm 복원
다음과 같은 특수한 네트워크 구성으로 VM을 백업하고 복원하는 것이 가능합니다. 그러나 복원 프로세스를 수행하는 동안 이러한 구성에는 몇몇 특별한 주의가 필요합니다.

* 부하 분산 장치에서의 VM(내부 및 외부)
* 다중의 예약된 IP가 있는 VM
* 다중 NIC가 있는 VM

> [!IMPORTANT]
> VM에 대한 특수 네트워크 구성을 만드는 경우 PowerShell을 사용하여 복원된 디스크에서 VM을 만들어야 합니다.
>
>

디스크에 복원한 후에 가상 컴퓨터를 다시 만들려면 다음 단계를 수행합니다.

1. [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)을 사용하여 Recovery Services Vault에서 디스크를 복원합니다.
2. PowerShell cmdlet을 사용하여 부하 분산 장치/다중 NIC/다중 예약된 IP에 필요한 VM 구성을 만들어 원하는 구성의 VM을 만드는 데 사용합니다.

   * [내부 부하 분산 장치 ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * [인터넷 연결 부하 분산 장치](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)에 연결하기 위한 VM을 만듭니다.
   * [다중 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * [다중의 예약된 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>다음 단계
VM을 복원할 수 있습니다. VM의 일반적인 오류에 대한 정보에 대한 문제 해결 문서를 참조하세요. 또한 VM을 사용하는 작업 관리에 대한 문서를 확인합니다.

* [문제 해결](backup-azure-vms-troubleshoot.md#restore)
* [가상 컴퓨터 관리](backup-azure-manage-vms.md)

