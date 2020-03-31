---
title: Azure 사이트 복구를 통해 VMware/물리적 서버에 대한 모빌리티 에이전트 관리
description: Azure 사이트 복구 서비스를 사용하여 VMware VM 및 물리적 서버의 재해 복구를 위해 Azure 에 대한 모빌리티 서비스 에이전트를 관리합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256966"
---
# <a name="manage-the-mobility-agent"></a>모바일 에이전트 관리 

VMware VM 및 물리적 서버를 Azure에 복구하기 위해 Azure 사이트 복구를 사용할 때 서버에서 이동성 에이전트를 설정합니다. 모빌리티 에이전트는 보호된 컴퓨터, 구성 서버/확장 프로세스 서버 간의 통신을 조정하고 데이터 복제를 관리합니다. 이 문서에서는 이동 에이전트가 배포된 후 이동성 에이전트를 관리하기 위한 일반적인 작업을 요약합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Azure 포털에서 모빌리티 서비스 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.
2. 포털에서 자격 증명 모음 > **복제된 항목**을 엽니다.
3. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 알림을 클릭하고 **에이전트 업데이트**에서 Mobility 서비스를 업그레이드하려는 머신을 선택합니다. 그런 다음 **확인**을 클릭합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 선택한 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows 서버의 powershell 스크립트를 통해 이동성 서비스 업데이트

시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

다음 스크립트를 사용하여 전원 셸 cmdlet을 통해 서버의 모빌리티 서비스를 업그레이드합니다.

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>보호된 각 서버에서 수동으로 모빌리티 서비스 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

2. 서버의 운영 체제에 따라 [에이전트 설치 관리자를 찾습니다.](vmware-physical-mobility-service-overview.md#locate-installer-files)

>[!IMPORTANT]
> Azure IaaS VM을 한 Azure 리전에서 다른 Azure 리전으로 복제하는 경우 이 메서드를 사용하지 마십시오. 사용 가능한 모든 옵션에 대한 자세한 내용은 [당사의 지침을](azure-to-azure-autoupdate.md) 참조하십시오.

3. 설치 파일을 보호된 컴퓨터에 복사하고 실행하여 이동성 에이전트를 업데이트합니다.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>모빌리티 서비스의 푸시 설치에 사용되는 업데이트 계정

Site Recovery를 배포하여 Mobility 서비스의 푸시 설치를 사용하도록 설정한 경우, 복제가 머신에 대해 사용하도록 설정되었을 때 Site Recovery 프로세스 서버가 머신에 액세스하여 서비스를 설치하는 데 사용하는 계정을 지정했습니다. 이 계정에 대한 자격 증명을 업데이트하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 따릅니다.

## <a name="uninstall-mobility-service"></a>모빌리티 서비스 제거

### <a name="on-a-windows-machine"></a>Windows 머신

UI 또는 명령 프롬프트에서 제거합니다.

- **UI에서**: 머신의 제어판에서 **프로그램**을 선택합니다. **마이크로소프트 Azure 사이트 복구 이동성 서비스/마스터 대상 서버** > **제거를**선택합니다.
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
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>소스 컴퓨터에 사이트 복구 VSS 공급자 설치

응용 프로그램 일관성 지점을 생성하기 위해 원본 컴퓨터에서 Azure 사이트 복구 VSS 공급자가 필요합니다. 공급자설치가 푸시 설치를 통과하지 못한 경우 아래 지침에 따라 수동으로 설치하십시오.

1. 관리자 cmd 창을 엽니다.
2. 모빌리티 서비스 설치 위치로 이동합니다. (예 : C :\프로그램 파일 (x86)\마이크로 소프트 Azure 사이트 복구 \에이전트)
3. 스크립트InMageVSSProvider_Uninstall.cmd를 실행합니다. 이렇게 하면 서비스가 이미 있는 경우 제거됩니다.
4. 스크립트 InMageVSSProvider_Install.cmd를 실행하여 VSS 공급자를 수동으로 설치합니다.

## <a name="next-steps"></a>다음 단계

- [VMware VM에 대한 재해 복구 설정](vmware-azure-tutorial.md)
- [물리적 서버에 대한 재해 복구 설정](physical-azure-disaster-recovery.md)
