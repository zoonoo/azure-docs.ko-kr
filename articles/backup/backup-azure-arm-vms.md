---
title: "Azure VM 백업 | Microsoft Docs"
description: "Azure 가상 머신을 검색하고, 등록하고, Recovery Services 자격 증명 모음에 백업합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "가상 머신 백업; 가상 머신 백업; 백업 및 재해 복구; ARM VM 백업"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 892a88a2bc9d492f8a3afe59c05b4729f4830e6d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure 가상 머신 백업

이 문서에서는 Azure VM(Resource Manager 배포 및 클래식 배포 모두)을 Recovery Services 자격 증명 모음에 백업하는 방법에 대해 자세히 설명합니다. VM을 백업하기 위한 작업은 대부분 준비 작업입니다. VM을 백업하거나 보호할 수 있으려면, VM을 보호하도록 환경을 준비하기 위한 [필수 구성 요소](backup-azure-arm-vms-prepare.md) 를 완료해야 합니다. 필수 구성 요소를 완비하고 나면, VM의 스냅숏을 만드는 백업 작업을 시작할 수 있습니다.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

자세한 내용은 [Azure에서 VM 백업 인프라 계획](backup-azure-vms-introduction.md) 및 [Azure 가상 머신](https://azure.microsoft.com/documentation/services/virtual-machines/)에 대한 문서를 참조하세요.

## <a name="triggering-the-backup-job"></a>백업 작업 트리거
Recovery Services 자격 증명 모음과 연결된 백업 정책은, 백업 작업이 실행되는 빈도와 시기를 정의합니다. 기본적으로 첫 번째 예약된 백업은 초기 백업입니다. 초기 백업이 발생할 때까지 **Backup 작업** 블레이드에서 최신 Backup 상태가 **경고(초기 백업 보류 중)**로 표시됩니다.

![보류 중인 Backup](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

초기 백업을 곧 시작할 예정이 아니라면 **지금 백업**을 실행하는 것이 좋습니다. 자격 증명 모음 대시보드에서 다음 절차가 시작됩니다. 이 절차는 모든 필수 구성 요소를 완비한 후에 초기 백업 작업을 실행하기 위한 용도입니다. 초기 백업 작업이 이미 실행되었으면, 이 절차는 제공되지 않습니다. 연결된 백업 정책이 다음 백업 작업을 결정합니다.  

초기 백업 작업을 실행하려면:

1. 자격 증명 모음 대시보드의 **Backup 항목**에 있는 번호를 클릭하거나 **Backup 항목** 타일을 클릭합니다. <br/>
  ![설정 아이콘](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  **Backup 항목** 블레이드가 열립니다.

  ![항목 백업](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. **Backup 항목** 블레이드에서 항목을 선택합니다.

  ![설정 아이콘](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  **백업 항목** 목록이 열립니다. <br/>

  ![백업 작업 트리거됨](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. **Backup 항목** 목록에서 줄임표 **...**를 클릭하여 상황에 맞는 메뉴를 엽니다.

  ![상황에 맞는 메뉴](./media/backup-azure-vms-first-look-arm/context-menu.png)

  상황에 맞는 메뉴가 나타납니다.

  ![상황에 맞는 메뉴](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. 상황에 맞는 메뉴에서 **지금 Backup**을 클릭합니다.

  ![상황에 맞는 메뉴](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  지금 Backup 블레이드가 열립니다.

  ![지금 Backup 블레이드를 표시합니다.](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. 지금 Backup 블레이드에서 달력 아이콘을 클릭하고 달력 컨트롤을 사용하여 이 복구 지점을 유지할 마지막 날을 선택하고 **Backup**을 클릭합니다.

  ![지금 백업 복구 지점을 유지할 마지막 날을 설정합니다.](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  배포 알림을 통해 백업 작업이 트리거되고 Backup 작업 페이지에서 작업의 진행률을 모니터링할 수 있다는 것을 알립니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

6. 초기 백업의 상태를 보거나 추적하려면 자격 증명 모음 대시보드의 **Backup 작업** 타일에서 **진행 중**을 클릭합니다.

  ![Backup 작업 타일](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Backup 작업 블레이드가 열립니다.

  ![Backup 작업 타일](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  **Backup 작업** 블레이드에서 모든 작업의 상태를 볼 수 있습니다. VM에 대한 백업 작업이 진행 중인지 또는 완료되었는지를 확인합니다. 백업 작업이 완료되면 상태는 *완료됨*입니다.

  > [!NOTE]
  > 백업 작업의 일부로 Azure Backup 서비스는 각 VM에서 백업 확장에 대한 명령을 발행하여 모든 쓰기를 플러시하고 일관된 스냅숏을 찍습니다.
  >
  >

## <a name="troubleshooting-errors"></a>문제 해결 오류
가상 머신을 백업하는 동안 문제가 발생한 경우 [VM 문제 해결 문서](backup-azure-vms-troubleshoot.md)의 도움말을 참조하세요.

## <a name="next-steps"></a>다음 단계
VM을 보호했으므로 다음 문서를 확인하여 VM 관리 작업과 VM 복원 방법을 알아봅니다.

* [가상 머신 관리 및 모니터링](backup-azure-manage-vms.md)
* [가상 머신 복원](backup-azure-arm-restore-vms.md)
