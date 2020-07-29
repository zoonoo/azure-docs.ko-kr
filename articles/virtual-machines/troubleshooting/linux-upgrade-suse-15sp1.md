---
title: SUSE Linux Enterprise Server에서 SUSE 15 SP1로 Azure VM 업그레이드 | Microsoft Docs
description: 이 문서에서는 SUSE Distribution Migration System을 사용하여 SUSE Linux Enterprise Server에서 SUSE 15 SP1로 Azure 가상 머신을 업그레이드하는 방법에 대한 일반적인 단계를 제공합니다.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 079574ab4c5846eae6266d1e13240baf99428a69
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449246"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>SLES 12에서 SLES 15 SP1로 Azure VM 업그레이드

이 문서에서는 SUSE Linux Enterprise Server(SLES) 12에서 SLES 15 SP1로 Azure VM(가상 머신)을 업그레이드하는 방법에 대한 일반적인 단계를 제공합니다. 자세한 내용은 [SUSE 배포 마이그레이션 시스템](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html)과 [SUSE Linux Enterprise Server 15 SP1 업그레이드 가이드](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update)를 참조하세요.

## <a name="supported-upgrade-paths"></a>지원되는 업그레이드 경로
SLES 15 SP1로 진행하려면 현재 SLES 버전이 SLES 12 SP4 또는 12 SP5여야 합니다.

![지원되는 업그레이드 경로에 대한 스크린샷](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>필수 구성 요소

- 승인된 가동 중지 시간에 따라 마이그레이션 작업을 계획합니다. 이는 마이그레이션 중에 VM이 다시 부팅되기 때문입니다.
- 마이그레이션 작업 전에 VM의 전체 백업을 수행합니다.
- 백업이 구성되지 않은 경우 OS 디스크의 스냅샷 백업을 수행합니다.
- [VM이 V1 세대 또는 V2 세대인지 확인합니다](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>SUSE 12 SP4 또는 SP5에서 SUSE 15 SP1로 업그레이드

1. VM의 최신 패키지를 설치합니다.

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. 설치가 완료되면 VM을 다시 시작합니다.

3. 커널 및 OS 버전을 확인합니다. 버전이 SUSE 12 SP4 또는 SUSE 12 SP5인지 확인합니다.

    ```
    uname -a
    cat /etc/os-release
    ```

4. **suse-migration-sle15-activation**을 설치합니다. 패키지 **suse-migration-sle15-activation**이 설치될 때 다른 패키지 **SLES15-Migration**이 종속성 패키지로 자동 설치됩니다. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. 설치가 완료되면 `reboot` 명령을 실행하여 VM을 다시 시작합니다.

6. [Azure Portal](https://portal.azure.com)로 이동하고, VM을 선택한 다음, **직렬 콘솔**을 선택합니다. "다시 부팅: 시스템을 다시 시작하는 중"에서 시스템이 멈춥니다. 이 프로세스에는 15 ~ 45분 정도 소요됩니다. 2세대 VM의 경우 "다시 부팅: 시스템을 다시 시작하는 중" 화면에서 멈출 수 있습니다. 이 경우 45분 정도 기다립니다. 그래도 더 이상 진행되지 않으면 Azure Portal에서 VM의 **개요** 페이지로 이동하고 VM을 중지했다가 다시 시작합니다.

     ![직렬 콘솔의 메시지에 대한 스크린샷](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. 새 커널로 시스템이 다시 시작되면 다음과 같은 메시지가 나타납니다.

     ![직렬 콘솔의 메시지에 대한 스크린샷](./media/linux-upgrade-suse-15sp1/output-message.png)
9. 커널 및 OS 버전을 확인하여 시스템이 성공적으로 업그레이드되었는지 점검합니다.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>VM의 세대 버전 확인

다음 방법 중 하나를 사용하여 세대 버전을 확인할 수 있습니다.

- SLES 터미널에서 명령 `dmidecode | grep -i hyper`를 실행합니다. 세대 V1 VM의 경우 반환된 출력이 없습니다. 세대 V2 VM의 경우 다음과 같은 출력이 표시됩니다.

     ![2세대 VM의 출력에 대한 스크린샷](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- [Azure Portal](https://portal.azure.com)에서 VM의 **속성**으로 이동하고 **VM 생성** 필드를 확인합니다.
