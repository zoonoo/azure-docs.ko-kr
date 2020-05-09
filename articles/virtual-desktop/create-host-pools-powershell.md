---
title: Windows 가상 데스크톱 호스트 풀 만들기 PowerShell-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에서 호스트 풀을 만드는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930406"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell을 사용한 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 여러 RemoteApp 그룹, 하나의 데스크톱 앱 그룹 및 여러 세션 호스트와 연결할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [PowerShell 모듈 설정](powershell-module.md)의 지침을 이미 따르는 것으로 가정 합니다.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell 클라이언트를 사용 하 여 호스트 풀 만들기

다음 cmdlet을 실행하여 Windows Virtual Desktop 환경에 로그인합니다.

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

이 cmdlet은 호스트 풀, 작업 영역 및 데스크톱 앱 그룹을 만듭니다. 또한 데스크톱 앱 그룹을 작업 영역에 등록 합니다. 이 cmdlet을 사용 하 여 작업 영역을 만들거나 기존 작업 영역을 사용할 수 있습니다. 

다음 cmdlet을 실행 하 여 등록 토큰을 만들어 호스트 풀에 조인 하 고 로컬 컴퓨터의 새 파일에 저장 하도록 세션 호스트에 권한을 부여 합니다. -ExpirationHours 매개 변수를 사용 하 여 등록 토큰의 유효 기간을 지정할 수 있습니다.

>[!NOTE]
>토큰의 만료 날짜는 1 시간 미만이 고 한 달이 하 여야 합니다. *-ExpirationTime* 를 해당 한도를 벗어난 값으로 설정 하면 cmdlet이 토큰을 생성 하지 않습니다.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

예를 들어 2 시간 후에 만료 되는 토큰을 만들려면 다음 cmdlet을 실행 합니다. 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

그런 다음이 cmdlet을 실행 하 여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자를 추가 합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

다음 cmdlet을 실행 하 여 호스트 풀의 기본 데스크톱 앱 그룹에 Azure Active Directory 사용자 그룹을 추가 합니다.

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

다음 cmdlet을 실행 하 여 등록 토큰을 변수로 내보냅니다 .이 변수는 나중에 [가상 컴퓨터를 Windows 가상 데스크톱 호스트 풀에 등록](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)하는 데 사용 됩니다.

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>호스트 풀에 대 한 가상 컴퓨터 만들기

이제 Windows 가상 데스크톱 호스트 풀에 가입할 수 있는 Azure 가상 컴퓨터를 만들 수 있습니다.

여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리 되는 이미지에서 가상 머신 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리 되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Windows 7을 사용 하 여 호스트 OS로 가상 컴퓨터를 배포 하는 경우 만들기 및 배포 프로세스는 약간 다릅니다. 자세한 내용은 [Windows 가상 데스크톱에 windows 7 가상 컴퓨터 배포](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)를 참조 하세요.

세션 호스트 가상 컴퓨터를 만든 후에는 다른 라이선스를 지불 하지 않고 Windows 또는 Windows Server 가상 컴퓨터를 실행 하기 위해 [세션 호스트 VM에 windows 라이선스를 적용](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) 합니다. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows 가상 데스크톱 에이전트 설치용 가상 컴퓨터 준비

가상 컴퓨터를 준비 하려면 먼저 다음 작업을 수행 해야 합니다. Windows 가상 데스크톱 에이전트를 설치 하 고 Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록 하려면 다음을 수행 해야 합니다.

- 컴퓨터에 도메인에 가입 해야 합니다. 이렇게 하면 들어오는 Windows 가상 데스크톱 사용자를 Azure Active Directory 계정에서 Active Directory 계정으로 매핑할 수 있으며 가상 컴퓨터에 대 한 액세스를 성공적으로 수행할 수 있습니다.
- 가상 컴퓨터가 Windows Server OS를 실행 하는 경우에는 RDSH (원격 데스크톱 세션 호스트) 역할을 설치 해야 합니다. RDSH 역할을 사용 하면 Windows 가상 데스크톱 에이전트를 제대로 설치할 수 있습니다.

성공적으로 도메인에 가입 하려면 각 가상 머신에서 다음 작업을 수행 합니다.

1. 가상 컴퓨터를 만들 때 제공한 자격 증명을 사용 하 여 [가상 컴퓨터에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 합니다.
2. 가상 컴퓨터에서 **제어판** 을 시작 하 고 **시스템**을 선택 합니다.
3. **컴퓨터 이름**을 선택 하 고 **설정 변경**을 선택한 다음 **변경 ...** 을 선택 합니다.
4. **도메인** 을 선택 하 고 가상 네트워크에 Active Directory 도메인을 입력 합니다.
5. 도메인 가입 컴퓨터에 대 한 권한이 있는 도메인 계정을 사용 하 여 인증 합니다.

    >[!NOTE]
    > Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버이기도 한지 확인합니다.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터 등록

Windows 가상 데스크톱 호스트 풀에 가상 컴퓨터를 등록 하는 것은 Windows 가상 데스크톱 에이전트를 설치 하는 것 만큼 간단 합니다.

Windows 가상 데스크톱 에이전트를 등록 하려면 각 가상 컴퓨터에서 다음을 수행 합니다.

1. 가상 컴퓨터를 만들 때 제공한 자격 증명을 사용 하 여 [가상 컴퓨터에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 합니다.
2. Windows 가상 데스크톱 에이전트를 다운로드 하 여 설치 합니다.
   - [Windows 가상 데스크톱 에이전트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)를 다운로드 합니다.
   - 설치 관리자를 실행합니다. 설치 관리자에서 등록 토큰을 묻는 메시지가 표시 되 면 **RdsRegistrationInfo** cmdlet에서 가져온 값을 입력 합니다.
3. Windows 가상 데스크톱 에이전트 부팅 로더를 다운로드 하 여 설치 합니다.
   - [Windows 가상 데스크톱 에이전트 부팅 로더](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)를 다운로드 합니다.
   - 설치 관리자를 실행합니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다. 또한 Vm을 공용 IP에 할당 하지 않는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀을 만들었으므로 Remoteapp으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대한 자세한 내용은 앱 그룹 관리 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
