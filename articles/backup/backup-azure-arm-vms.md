---
title: "복구 서비스 자격 증명 모음에 Azure VM 백업 | Microsoft Docs"
description: "Azure 가상 컴퓨터 백업에 대한 절차를 사용하여 Azure 가상 컴퓨터를 복구 서비스 자격 증명 모음으로 검색, 등록 및 백업합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: "가상 컴퓨터 백업; 가상 컴퓨터 백업; 백업 및 재해 복구; ARM VM 백업"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: ac8df40db8ddcc84a0a6221dddd0f17fecbe6586
ms.openlocfilehash: e80d4fdb6f189bf46096422602508b0827f41a67


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음에 Azure VM 백업
> [!div class="op_single_selector"]
> * [복구 서비스 자격 증명 모음에 VM 백업](backup-azure-arm-vms.md)
> * [백업 자격 증명 모음에 VM 백업](backup-azure-vms.md)
>
>

이 문서는 Azure VM(Resource Manager 배포 VM과 클래식 배포 VM 모두)을 복구 서비스 자격 증명 모음에 백업하는 절차를 제공합니다. VM을 백업하기 위한 작업은 대부분 준비 작업입니다. VM을 백업하거나 보호할 수 있으려면, VM을 보호하도록 환경을 준비하기 위한 [필수 구성 요소](backup-azure-arm-vms-prepare.md) 를 완료해야 합니다. 필수 구성 요소를 완비하고 나면, VM의 스냅숏을 만드는 백업 작업을 시작할 수 있습니다.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

자세한 내용은 [Azure에서 VM 백업 인프라 계획](backup-azure-vms-introduction.md) 및 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)에 대한 문서를 참조하세요.

## <a name="triggering-the-backup-job"></a>백업 작업 트리거
복구 서비스 자격 증명 모음과 연결된 백업 정책은, 백업 작업이 실행되는 빈도와 시기를 정의합니다. 기본적으로 첫 번째 예약된 백업은 초기 백업입니다. 초기 백업이 발생할 때까지 **백업 작업** 블레이드에서 최신 백업 상태가 **경고(초기 백업 보류 중)**로 표시됩니다.

![보류 중인 백업](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

초기 백업을 곧 시작할 예정이 아니라면 **지금 백업**을 실행하는 것이 좋습니다. 자격 증명 모음 대시보드에서 다음 절차가 시작됩니다. 이 절차는 모든 필수 구성 요소를 완비한 후에 초기 백업 작업을 실행하기 위한 용도입니다. 초기 백업 작업이 이미 실행되었으면, 이 절차는 제공되지 않습니다. 연결된 백업 정책이 다음 백업 작업을 결정합니다.  

초기 백업 작업을 실행하려면:

1. 자격 증명 모음 대시보드의 **백업** 타일에서 **Azure Virtual Machines**를 클릭합니다. <br/>
    ![설정 아이콘](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

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

   > [!NOTE]
   > 백업 작업을 수행하는 동안 각 가상 컴퓨터의 백업 확장이 모든 쓰기를 플러시하고 일관된 스냅숏을 생성합니다.
   >
   >

    백업 작업이 완료되면 상태는 *완료됨*입니다.

## <a name="troubleshooting-errors"></a>문제 해결 오류
가상 컴퓨터를 백업하는 동안 문제가 발생한 경우 [VM 문제 해결 문서](backup-azure-vms-troubleshoot.md)의 도움말을 참조하세요.

## <a name="next-steps"></a>다음 단계
VM을 보호했으므로 다음 문서를 확인하여 VM 관리 작업과 VM 복원 방법을 알아봅니다.

* [가상 컴퓨터 관리 및 모니터링](backup-azure-manage-vms.md)
* [가상 컴퓨터 복원](backup-azure-arm-restore-vms.md)



<!--HONumber=Nov16_HO3-->


