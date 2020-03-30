---
title: Windows 가상 데스크톱 호스트 풀 PowerShell 만들기 - Azure
description: PowerShell cmdlet을 사용하여 Windows 가상 데스크톱에서 호스트 풀을 만드는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246962"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell을 사용한 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell 클라이언트를 사용하여 호스트 풀 만들기

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우).

다음 cmdlet을 실행하여 Windows 가상 데스크톱 환경에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

그런 다음 이 cmdlet을 실행하여 Windows 가상 데스크톱 테넌트에서 새 호스트 풀을 만듭니다.

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

다음 cmdlet을 실행하여 등록 토큰을 만들어 세션 호스트가 호스트 풀에 참여할 수 있는 권한을 부여하고 로컬 컴퓨터의 새 파일에 저장합니다. -ExpirationHours 매개 변수를 사용하여 등록 토큰의 유효 시간을 지정할 수 있습니다.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

그런 다음 이 cmdlet을 실행하여 Azure Active Directory 사용자를 호스트 풀의 기본 데스크톱 앱 그룹에 추가합니다.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** cmdlet은 보안 그룹 추가를 지원하지 않으며 앱 그룹에 한 번에 한 명의 사용자만 추가합니다. 앱 그룹에 여러 사용자를 추가하려면 적절한 사용자 주체 이름으로 cmdlet을 다시 실행합니다.

다음 cmdlet을 실행하여 등록 토큰을 변수로 내보내며, 나중에 [사용할 Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터 등록.](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대한 가상 시스템 만들기

이제 Windows 가상 데스크톱 호스트 풀에 조인할 수 있는 Azure 가상 컴퓨터를 만들 수 있습니다.

다음과 같은 여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리되는 이미지에서 가상 시스템 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 컴퓨터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Windows 7을 호스트 OS로 사용하여 가상 컴퓨터를 배포하는 경우 생성 및 배포 프로세스가 약간 다릅니다. 자세한 내용은 [Windows 가상 데스크톱에서 Windows 7 가상 컴퓨터 배포를](deploy-windows-7-virtual-machine.md)참조하십시오.

세션 호스트 가상 컴퓨터를 만든 후 [세션 호스트 VM에 Windows 라이선스를 적용하여](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) 다른 라이선스에 대한 비용을 지불하지 않고 Windows 또는 Windows Server 가상 컴퓨터를 실행합니다. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows 가상 데스크톱 에이전트 설치를 위한 가상 컴퓨터 준비

Windows 가상 데스크톱 에이전트를 설치하고 Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록하려면 다음 작업을 수행해야 합니다.

- 컴퓨터를 도메인에 가입해야 합니다. 이렇게 하면 들어오는 Windows 가상 데스크톱 사용자가 Azure Active Directory 계정에서 Active Directory 계정으로 매핑되고 가상 시스템에 대한 액세스가 성공적으로 허용됩니다.
- 가상 시스템이 Windows 서버 OS를 실행하는 경우 RDSH(원격 데스크톱 세션 호스트) 역할을 설치해야 합니다. RDSH 역할을 사용하면 Windows 가상 데스크톱 에이전트를 제대로 설치할 수 있습니다.

도메인 조인을 성공적으로 수행하려면 각 가상 컴퓨터에서 다음과 같은 작업을 수행합니다.

1. [가상 컴퓨터를](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 만들 때 제공한 자격 증명을 사용하여 가상 컴퓨터에 연결합니다.
2. 가상 컴퓨터에서 **제어판을** 실행하고 **시스템을**선택합니다.
3. **컴퓨터 이름을**선택하고 **설정 변경을**선택한 다음 **변경을 선택합니다...**
4. **도메인을** 선택한 다음 가상 네트워크에서 Active Directory 도메인을 입력합니다.
5. 도메인 가입 컴퓨터에 권한이 있는 도메인 계정으로 인증합니다.

    >[!NOTE]
    > Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버이기도 한지 확인합니다.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록합니다.

Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록하는 것은 Windows 가상 데스크톱 에이전트를 설치하는 것만큼 간단합니다.

Windows 가상 데스크톱 에이전트를 등록하려면 각 가상 컴퓨터에서 다음을 수행합니다.

1. [가상 컴퓨터를](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 만들 때 제공한 자격 증명을 사용하여 가상 컴퓨터에 연결합니다.
2. Windows 가상 데스크톱 에이전트를 다운로드하여 설치합니다.
   - Windows [가상 데스크톱 에이전트를](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)다운로드합니다.
   - 다운로드한 설치 관리자를 마우스 오른쪽 단추로 클릭하고 **속성을**선택하고 **차단 해제를**선택한 다음 **확인을**선택합니다. 이렇게 하면 시스템에서 설치 관리자를 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다. 설치 관리자가 등록 토큰을 요청하면 **Export-RdsRegistrationInfo** cmdlet에서 얻은 값을 입력합니다.
3. Windows 가상 데스크톱 에이전트 부트 로더를 다운로드하여 설치합니다.
   - Windows [가상 데스크톱 에이전트 부트 로더를 다운로드합니다.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)
   - 다운로드한 설치 관리자를 마우스 오른쪽 단추로 클릭하고 **속성을**선택하고 **차단 해제를**선택한 다음 **확인을**선택합니다. 이렇게 하면 시스템에서 설치 관리자를 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀을 만들었으니 RemoteApps로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
