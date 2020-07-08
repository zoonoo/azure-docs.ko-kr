---
title: Windows Virtual Desktop 호스트 풀 PowerShell 만들기 - Azure
description: PowerShell cmdlet을 사용하여 Windows Virtual Desktop에서 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 79bd13deb66998c8a1acde28774b47fd567d368c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204713"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell을 사용한 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 2019년 가을 릴리스에 적용됩니다. 2020년 봄 업데이트에 도입된 Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../create-host-pools-powershell.md)를 참조하세요.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell 클라이언트를 사용하여 호스트 풀 만들기

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우).

다음 cmdlet을 실행하여 Windows Virtual Desktop 환경에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

이제 이 cmdlet을 실행하여 Windows Virtual Desktop에 새 호스트 풀을 만듭니다.

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

다음 cmdlet을 실행하여 등록 토큰을 만들어 세션 호스트의 호스트 풀 참가를 인증하고 로컬 컴퓨터의 새 파일에 저장합니다. -ExpirationHours 매개 변수를 사용하여 등록 토큰의 유효 기간을 지정할 수 있습니다.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

그런 다음, 이 cmdlet을 실행하여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자를 추가합니다.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**RdsAppGroupUser** cmdlet은 보안 그룹 추가를 지원하지 않으며 앱 그룹에 한 번에 한 사용자만 추가합니다. 앱 그룹에 여러 사용자를 추가하려면 적절한 사용자 계정 이름을 사용하여 cmdlet을 다시 실행합니다.

다음 cmdlet을 실행하여 등록 토큰을 변수로 내보냅니다.이 변수는 나중에 [가상 머신을 Windows Virtual Desktop 호스트 풀에 등록](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)할 때 사용합니다.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대한 가상 머신 만들기

이제 Windows Virtual Desktop 호스트 풀에 참가할 수 있는 Azure 가상 머신을 만들 수 있습니다.

다음과 같은 여러 방법으로 가상 머신을 만들 수 있습니다.

- [Azure Gallery에서 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리 이미지에서 가상 머신 만들기](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Windows 7을 사용하여 호스트 OS로 가상 머신을 배포하는 경우에는 만들기 및 배포 프로세스가 약간 다릅니다. 자세한 내용은 [Windows Virtual Desktop에서 Windows 7 가상 머신 배포](deploy-windows-7-virtual-machine.md)를 참조하세요.

세션 호스트 가상 머신을 만든 후에는 [Windows 라이선스를 세션 호스트 VM에 적용](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)하여 다른 라이선스 비용 지출 없이 Windows 또는 Windows Server 가상 머신을 실행합니다.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows Virtual Desktop 에이전트 설치를 위한 가상 머신 준비

Windows Virtual Desktop 에이전트를 설치하고 가상 머신을 Windows Virtual Desktop 호스트 풀에 등록하기에 앞서 가상 머신을 준비하려면 다음 몇 가지가 필요합니다.

- 머신이 도메인에 참가해야 합니다. 이렇게 하면 들어오는 Windows Virtual Desktop 사용자를 해당 Azure Active Directory 계정에서 Active Directory 계정에 매핑하고 가상 머신에 대한 액세스를 허용할 수 있습니다.
- 가상 머신이 Windows Server OS를 실행할 경우 RDSH(원격 데스크톱 세션 호스트) 역할을 설치해야 합니다. RDSH 역할을 사용하면 Windows Virtual Desktop 에이전트를 올바르게 설치할 수 있습니다.

성공적인 도메인 참가를 위해 각 가상 머신에서 다음을 수행합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 가상 머신에서 **제어판**을 시작하고 **시스템**을 선택합니다.
3. **컴퓨터 이름**, **설정 변경**을 차례로 선택한 다음, **변경...** 을 선택합니다.
4. **도메인**을 선택한 다음, 가상 네트워크에 Active Directory 도메인을 입력합니다.
5. 도메인 참가 머신에 대한 권한이 있는 도메인 계정으로 인증합니다.

    >[!NOTE]
    > Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버이기도 한지 확인합니다.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows Virtual Desktop 호스트 풀에 가상 머신을 등록합니다.

Windows Virtual Desktop 호스트 풀에 가상 머신을 등록하는 작업은 Windows Virtual Desktop 에이전트 설치만큼 간단합니다.

Windows Virtual Desktop 에이전트를 등록하려면 각 가상 머신에서 다음을 수행합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. Windows Virtual Desktop 에이전트를 다운로드하여 설치합니다.
   - [Windows Virtual Desktop 에이전트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)를 다운로드합니다.
   - 다운로드한 설치 프로그램을 마우스 오른쪽 단추로 클릭하고 **속성**, **차단 해제**를 차례로 선택한 다음, **확인**을 선택합니다. 그러면 시스템에서 설치 프로그램을 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다. 설치 프로그램이 등록 토큰을 요청하면 **Export-RdsRegistrationInfo** cmdlet에서 가져온 값을 입력합니다.
3. Windows Virtual Desktop 에이전트 부팅 로더를 다운로드하여 설치합니다.
   - [Windows Virtual Desktop 에이전트 부팅 로더](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)를 다운로드합니다.
   - 다운로드한 설치 프로그램을 마우스 오른쪽 단추로 클릭하고 **속성**, **차단 해제**를 차례로 선택한 다음, **확인**을 선택합니다. 그러면 시스템에서 설치 프로그램을 신뢰할 수 있습니다.
   - 설치 관리자를 실행합니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀이 만들어졌으므로 이 풀을 RemoteApp으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](../manage-app-groups.md)
