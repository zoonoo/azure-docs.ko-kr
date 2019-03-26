---
title: VMware Vm의 재해 복구에 대 한 서버 및 Azure Site Recovery를 사용 하 여 물리적 서버에서 모바일 에이전트 관리 | Microsoft Docs
description: VMware Vm 및 물리적 서버에서 Azure Site Recovery 서비스를 사용 하 여 Azure로의 재해 복구를 위해 모바일 서비스 에이전트를 관리 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 441b58e60bf8dfd5f164ac24d746b9791158ade2
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420119"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>보호 된 컴퓨터에서 모바일 에이전트를 관리 합니다.

설정한 모바일 에이전트 서버에서 VMware Vm 및 물리적 서버에서 Azure로의 재해 복구를 위한 Azure Site Recovery를 사용 하는 경우. 모바일 에이전트는 보호 된 컴퓨터에 구성 서버/확장 프로세스 서버 간의 통신을 조정 하 고 데이터 복제를 관리 합니다. 이 문서는 배포 된 후에 모바일 에이전트를 관리 하기 위한 일반적인 작업을 요약 합니다.

## <a name="update-mobility-service-from-azure-portal"></a>Azure portal에서 모바일 서비스 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.
2. 포털에서 자격 증명 모음 > **복제된 항목**을 엽니다.
3. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 알림을 클릭하고 **에이전트 업데이트**에서 Mobility 서비스를 업그레이드하려는 머신을 선택합니다. 그런 후 **OK**를 클릭합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 선택한 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows 서버에서 powershell 스크립트를 통해 모바일 서비스 업데이트

Power shell cmdlet 통해 서버에 모바일 서비스를 업그레이드 하는 스크립트 사용

```azurepowershell
Update-AzureRmRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Mobility service 푸시 설치에 사용 되는 계정 업데이트

Site Recovery를 배포하여 Mobility 서비스의 푸시 설치를 사용하도록 설정한 경우, 복제가 머신에 대해 사용하도록 설정되었을 때 Site Recovery 프로세스 서버가 머신에 액세스하여 서비스를 설치하는 데 사용하는 계정을 지정했습니다. 이 계정에 대한 자격 증명을 업데이트하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 따릅니다.

## <a name="uninstall-mobility-service"></a>모바일 서비스 제거

### <a name="on-a-windows-machine"></a>Windows 머신

UI 또는 명령 프롬프트에서 제거합니다.

- **UI에서**: 머신의 제어판에서 **프로그램**을 선택합니다. **Microsoft Azure Site Recovery Mobility 서비스/마스터 대상 서버** > **제거**를 선택합니다.
- **명령 프롬프트에서**: 머신에서 명령 프롬프트 창을 관리자로 엽니다. 다음 명령 실행: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux 머신
1. Linux 머신에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /user/local/ASR로 이동합니다.
3. 다음 명령 실행:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
