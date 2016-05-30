<properties
	pageTitle="Azure Resource Manager 가상 컴퓨터 백업 | Microsoft Azure"
	description="Azure 가상 컴퓨터 백업에 대한 절차를 사용하여 ARM 가상 컴퓨터를 복구 서비스 자격 증명 모음으로 검색, 등록 및 백업합니다."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="가상 컴퓨터 백업; 가상 컴퓨터 백업; 백업 및 재해 복구; ARM VM 백업"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Azure Resource Manager(ARM) 가상 컴퓨터 백업

> [AZURE.SELECTOR]
- [ARM VM을 Azure에 백업](backup-azure-arm-vms.md)
- [Azure 가상 컴퓨터 백업](backup-azure-vms.md)

이 문서는 Azure Resource Manager(ARM) 가상 컴퓨터(VM)를 복구 서비스 자격 증명 모음에 백업하는 절차를 제공합니다. ARM VM 을 백업하는 대부분의 작업은 준비 작업에 들어갑니다. Azure 가상 컴퓨터를 백업하거나 보호할 수 있으려면, VM을 보호하도록 환경을 준비하기 위한 [필수 구성 요소](backup-azure-arm-vms-prepare.md)를 완비해야 합니다. 필수 구성 요소를 완비하고 나면, VM의 스냅숏을 만드는 백업 작업을 시작할 수 있습니다.

>[AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../resource-manager-deployment-model.md) 모델이 있습니다. 이 문서는 리소스 관리자 및 ARM 기반 VM을 사용하기 위한 것입니다. 클래식 배포 모델 VM 작업에 대한 자세한 내용은 [Azure 가상 컴퓨터 백업](backup-azure-vms.md)을 참조하세요.

자세한 내용은 [Azure에서 VM 백업 인프라 계획](backup-azure-vms-introduction.md) 및 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)에 대한 문서를 참조하세요.

## 백업 작업 트리거

복구 서비스 자격 증명 모음과 연결된 백업 정책은, 백업 작업이 실행되는 빈도와 시기를 정의합니다. 기본적으로 첫 번째 예약된 백업은 초기 백업입니다. 초기 백업이 발생할 때까지 **백업 작업** 블레이드에서 최신 백업 상태가 **경고(초기 백업 보류 중)**로 표시됩니다.

![보류 중인 백업](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

초기 백업을 곧 시작할 예정이 아니라면 **지금 백업**을 실행하는 것이 좋습니다. 자격 증명 모음 대시보드에서 다음 절차가 시작됩니다. 이 절차는 모든 필수 구성 요소를 완비한 후에 초기 백업 작업을 실행하기 위한 용도입니다. 초기 백업 작업이 이미 실행되었으면, 이 절차는 제공되지 않습니다. 연결된 백업 정책이 다음 백업 작업을 결정합니다.

초기 백업 작업을 실행하려면:

1. 자격 증명 모음 대시보드의 **백업** 타일에서 **Azure 가상 컴퓨터**를 클릭합니다. <br/> ![설정 아이콘](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    **백업 항목** 블레이드가 열립니다.

2. **백업 항목** 블레이드에서 백업하려는 자격 증명 모음을 마우스 오른쪽 단추로 클릭하고 **지금 백업**을 클릭합니다.

    ![설정 아이콘](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    백업 작업이 트리거됩니다. <br/>

    ![백업 작업 트리거됨](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. 초기 백업이 완료되었는지 보려면 자격 증명 모음 대시보드의 **백업 작업** 타일에서 **Azure 가상 컴퓨터**를 클릭합니다.

    ![백업 작업 타일](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    백업 작업 블레이드가 열립니다.

4. **백업 작업** 블레이드에서 모든 작업의 상태를 볼 수 있습니다.

    ![백업 작업 타일](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] 백업 작업의 일부로 Azure 백업 서비스는 각 가상 컴퓨터에서 백업 확장에 대한 명령을 발행하여 모든 쓰기를 플러시하고 일관된 스냅숏을 찍습니다.

    백업 작업이 완료되면 상태는 *완료됨*입니다.


## 문제 해결 오류
가상 컴퓨터를 백업하는 동안 문제가 발생하면, [VM 문제 해결 문서](backup-azure-vms-troubleshoot.md)를 살펴보세요.

## 다음 단계

VM을 보호했으니, 다음 문서에서 VM으로 수행할 수 있는 추가적인 관리 작업과 VM을 복원하는 방법을 확인하세요.

- [가상 컴퓨터 관리 및 모니터링](backup-azure-manage-vms.md)
- [가상 컴퓨터 복원](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0518_2016-->