---
title: "Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure 지역 간의 재해 복구를 설정하기 위한 컴퓨터 준비 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure 지역 간의 재해 복구를 설정하기 위해 컴퓨터를 준비하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 7658bedc0bd5c4a289f3271504a006ba54c783b6
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure VM을 다른 지역에 복제

>[!NOTE]
> Azure VM(가상 컴퓨터)에 대한 Azure Site Recovery 복제는 현재 미리 보기로 제공됩니다.

## <a name="overview"></a>개요

이 문서에서는 Azure Site Recovery를 사용하여 Azure Virtual Machines를 온-프레미스 환경에서 Azure로 마이그레이션한 후 두 Azure 지역 간의 복제를 위해 이러한 컴퓨터를 준비하도록 도와줍니다.

## <a name="disaster-recovery-and-compliance"></a>재해 복구 및 규정 준수
워크로드를 Azure로 이동하는 기업이 점점 증가하고 있습니다. 업무에 중요한 온-프레미스 프로덕션 워크로드를 Azure로 이동하는 기업에서는 Azure 지역에서 중단으로부터 보호하고 규정을 준수하기 위해 이러한 워크로드에 대한 재해 복구를 설정해야 합니다.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>마이그레이션된 컴퓨터의 복제를 준비하기 위한 단계
마이그레이션된 컴퓨터를 다른 Azure 지역으로 복제하기 위한 설정을 준비하려면

1. 마이그레이션을 완료하려면
2. 필요한 경우 Azure 에이전트를 설치합니다.
3. 모바일 서비스를 제거합니다.  
4. VM을 다시 시작합니다.

이러한 단계는 다음 섹션에 자세히 설명되어 있습니다.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>1단계: Hyper-V VM, VMware VM 및 물리적 서버에서 실행 중인 워크로드를 마이그레이션하여 Azure VM에서 실행할 수 있습니다.

복제를 설정하고 온-프레미스 Hyper-V, VMware 및 실제 워크로드를 Azure로 마이그레이션하려면 [Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션](site-recovery-migrate-azure-to-azure.md) 문서의 단계를 따르세요. 

마이그레이션 후 장애 조치를 커밋하거나 삭제할 필요가 없습니다. 대신 마이그레이션할 각 컴퓨터에 대해 **마이그레이션 완료** 옵션을 선택합니다.
1. **복제된 항목**에서 VM를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. **확인**을 클릭하여 단계를 완료합니다. **Site Recovery 작업**의 마이그레이션 완료 작업을 모니터링하여 VM 속성에서 진행률을 추적할 수 있습니다.
2. **마이그레이션 완료** 작업은 마이그레이션 프로세스를 완료하고 가상 컴퓨터에 대한 복제를 제거하며 컴퓨터에 대한 Site Recovery 청구를 중지합니다.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>2단계 - 가상 컴퓨터에 Azure VM 에이전트 설치
Site Recovery 확장이 작동하고 VM을 보호하려면 Azure [VM 에이전트](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux)를 가상 컴퓨터에 설치해야 합니다.

>[!IMPORTANT]
>9.7.0.0 버전부터 모바일 서비스 설치 관리자는 Windows 가상 컴퓨터에 사용 가능한 최신 Azure VM 에이전트도 설치합니다. 마이그레이션 시 가상 컴퓨터는 Site Recovery 확장을 포함하여 모든 VM 확장을 사용하기 위한 에이전트 설치 필수 구성 요소를 충족합니다. Azure VM 에이전트는 마이그레이션된 컴퓨터에 설치된 모바일 서비스가 버전 9.6 이하인 경우에만 수동으로 설치해야 합니다.

다음 표에서는 VM 에이전트를 설치하고 설치 유효성을 검사하는 방법에 대한 추가 정보를 제공합니다.

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 |[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다. |최신 [Linux 에이전트](../../virtual-machines/linux/agent-user-guide.md)를 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다. 리포지토리에서 배포 에이전트를 설치하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 설치하는 것은 *좋지 않습니다*.  |
| VM 에이전트 설치 유효성 검사 |1. Azure VM에서 C:\WindowsAzure\Packages 폴더로 이동합니다. WaAppAgent.exe 파일을 확인해야 합니다. <br>2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. **제품 버전** 필드가 2.6.1198.718 이상이어야 합니다. |해당 없음 |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>3단계: 마이그레이션된 가상 컴퓨터에서 모바일 서비스 제거

온-프레미스 VMware 컴퓨터 또는 물리적 Windows/Linux 서버를 마이그레이션한 경우 마이그레이션된 가상 컴퓨터에서 모바일 서비스를 수동으로 제거해야 합니다.

>[!IMPORTANT]
>Azure로 마이그레이션된 Hyper-V VM에는 이 단계가 필요하지 않습니다.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Windows Server VM에서 모바일 서비스 제거
Windows Server 컴퓨터에서 모바일 서비스를 제거하려면 다음 방법 중 하나를 사용합니다.

##### <a name="uninstall-by-using-the-windows-ui"></a>Windows UI를 사용하여 제거
1. 제어판에서 **프로그램**을 선택합니다.
2. **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**를 선택한 다음 **제거**를 선택합니다.

##### <a name="uninstall-at-a-command-prompt"></a>명령 프롬프트에서 제거
1. 관리자로 명령 프롬프트 창을 엽니다.
2. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Linux 컴퓨터에서 모바일 서비스 제거
1. Linux 서버에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /user/local/ASR로 이동합니다.
3. 모바일 서비스를 제거하려면 다음 명령을 실행합니다.

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>4단계: VM 다시 시작

모바일 서비스를 제거한 후 다른 Azure 지역으로의 복제를 설정하기 전에 VM을 다시 시작합니다.


## <a name="next-steps"></a>다음 단계
- [Azure Virtual Machines를 복제](azure-to-azure-quickstart.md)하여 워크로드 보호를 시작합니다.
- [Azure Virtual Machines 복제를 위한 네트워킹 지침](site-recovery-azure-to-azure-networking-guidance.md)에 대해 자세히 알아봅니다.
