---
title: Azure Site Recovery를 사용하여 VMware/물리적 서버용 모바일 에이전트 관리
description: Azure Site Recovery 서비스를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하기 위한 Mobility Service 에이전트를 관리합니다.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: 88f4fb85a6f67b770c8a13a5701749b8369124bd
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578153"
---
# <a name="manage-the-mobility-agent"></a>모바일 에이전트 관리 

Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하는 경우 서버에서 모바일 에이전트를 설정합니다. 모바일 에이전트는 보호된 머신, 구성 서버/스케일 아웃 프로세스 서버 간 통신을 조정하고 데이터 복제를 관리합니다. 이 문서에는 배포 후에 모바일 에이전트를 관리하기 위한 일반적인 작업이 요약되어 있습니다.

>[!TIP]
>특정 OS/Linux 배포의 설치 관리자를 다운로드하려면 [여기](vmware-physical-mobility-service-overview.md#locate-installer-files)에서 참고 자료를 참조하세요. 포털에서 자동으로 업데이트하려면 설치 관리자를 다운로드할 필요가 없습니다. [ASR이 구성 서버에서 설치 관리자를 자동으로 가져와 에이전트를 업데이트합니다](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Azure Portal에서 Mobility Service 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.
    1. 9\.36 버전 이상에서는 SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7의 경우 [구성 서버 및 스케일 아웃 프로세스 서버에서 최신 설치 관리자를 사용할 수 있는지 확인](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)합니다.
1. 포털에서 자격 증명 모음 > **복제된 항목** 을 엽니다.
1. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 알림을 클릭하고 **에이전트 업데이트** 에서 Mobility 서비스를 업그레이드하려는 머신을 선택합니다. 그런 후 **OK** 를 클릭합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 선택한 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다. 모바일 에이전트가 구성 서버 버전으로 업데이트됩니다. 예를 들어 구성 서버가 버전 9.33이면 보호된 VM의 모바일 에이전트도 버전 9.33으로 업데이트됩니다.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows Server의 PowerShell 스크립트를 통해 Mobility Service 업데이트

시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

다음 스크립트를 사용하여 PowerShell cmdlet을 통해 서버의 Mobility Service를 업그레이드할 수 있습니다.

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>각 보호된 서버에서 수동으로 Mobility Service 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

2. 서버의 운영 체제에 따라 [에이전트 설치 관리자를 찾습니다](vmware-physical-mobility-service-overview.md#locate-installer-files).

>[!IMPORTANT]
> Azure 지역 간에 Azure IaaS VM을 복제하는 경우에는 이 방법을 사용하면 안 됩니다. 사용 가능한 모든 옵션에 대한 자세한 내용은 [참고 자료](azure-to-azure-autoupdate.md)를 참조하세요.

3. 보호된 머신에 설치 파일을 복사하고 실행하여 모바일 에이전트를 업데이트합니다.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Mobility Service 푸시 설치에 사용되는 계정 업데이트

Site Recovery를 배포하여 Mobility 서비스의 푸시 설치를 사용하도록 설정한 경우, 복제가 머신에 대해 사용하도록 설정되었을 때 Site Recovery 프로세스 서버가 머신에 액세스하여 서비스를 설치하는 데 사용하는 계정을 지정했습니다. 이 계정에 대한 자격 증명을 업데이트하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 따릅니다.

## <a name="uninstall-mobility-service"></a>Mobility Service 제거

### <a name="on-a-windows-machine"></a>Windows 머신

UI 또는 명령 프롬프트에서 제거합니다.

- **UI에서**: 머신의 제어판에서 **프로그램** 을 선택합니다. **Microsoft Azure Site Recovery Mobility 서비스/마스터 대상 서버** > **제거** 를 선택합니다.
- **명령 프롬프트에서**: 머신에서 관리자 권한으로 명령 프롬프트 창을 엽니다. 다음 명령 실행: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux 머신
1. Linux 머신에서 **루트** 사용자로 로그인합니다.
2. 터미널에서 /usr/local/ASR로 이동합니다.
3. 다음 명령 실행:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>원본 머신에 Site Recovery VSS 공급자 설치

애플리케이션 일관성 지점을 생성하려면 원본 머신에 Azure Site Recovery VSS 공급자가 있어야 합니다. 푸시 설치를 통한 공급자 설치에 실패한 경우 아래 지침에 따라 수동으로 설치합니다.

1. 관리자 cmd 창을 엽니다.
2. Mobility Service 설치 위치로 이동합니다. (예 - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. InMageVSSProvider_Uninstall.cmd 스크립트를 실행합니다. 서비스가 이미 있는 경우 제거됩니다.
4. InMageVSSProvider_Install.cmd 스크립트를 실행하여 VSS 공급자를 수동으로 설치합니다.

## <a name="next-steps"></a>다음 단계

- [VMware VM에 대한 재해 복구 설정](vmware-azure-tutorial.md)
- [물리적 서버에 대한 재해 복구 설정](physical-azure-disaster-recovery.md)
