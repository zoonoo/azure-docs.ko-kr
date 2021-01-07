---
title: Windows Virtual Desktop(클래식) 호스트 풀 Azure Marketplace - Azure
description: Azure Marketplace를 사용하여 Windows Virtual Desktop(클래식) 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04184a658ae1efd03afd3e920818eb5317bc7553
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008596"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>자습서: Windows Virtual Desktop(클래식)에서 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../create-host-pools-azure-marketplace.md)를 참조하세요.

이 자습서에서는 Microsoft Azure Marketplace 제품을 사용하여 Windows Virtual Desktop 테넌트 내에서 호스트 풀을 만드는 방법에 대해 알아봅니다.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 자습서의 작업은 다음과 같습니다.

> [!div class="checklist"]
>
> * Windows Virtual Desktop에서 호스트 풀 만들기
> * Azure 구독에서 VM으로 리소스 그룹 만들기
> * VM을 Active Directory 도메인에 조인
> * VM을 Windows Virtual Desktop에 등록

## <a name="prerequisites"></a>사전 요구 사항

* Virtual Desktop의 테넌트. 이전 [자습서](tenant-setup-azure-active-directory.md)에서 테넌트를 만듭니다.
* [Windows Virtual Desktop PowerShell 모듈](/powershell/windows-virtual-desktop/overview/)

이 모듈이 있으면 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>새 호스트 풀을 프로비저닝하기 위해 Azure Marketplace 제품 실행

새 호스트 풀을 프로비저닝하기 위해 Azure Marketplace 제품을 실행하려면 다음을 수행합니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
1. Marketplace 검색 창에 **Windows Virtual Desktop**을 입력합니다.
1. **Windows Virtual Desktop - 호스트 풀 프로비전**을 선택한 다음, **만들기** 선택합니다.

그런 다음, 다음 섹션의 지침에 따라 해당 탭에 대한 정보를 입력합니다.

### <a name="basics"></a>기본 사항

**기본 사항** 탭에서 수행하는 작업은 다음과 같습니다.

1. **구독**을 선택합니다.
1. **리소스 그룹**에 대해 **새로 만들기**를 선택하고, 새 리소스 그룹의 이름을 제공합니다.
1. **지역**을 선택합니다.
1. Windows Virtual Desktop 테넌트 내에서 고유한 호스트 풀의 이름을 입력합니다.
1. **데스크톱 유형**을 선택합니다. **개인**을 선택하는 경우 이 호스트 풀에 연결하는 각 사용자가 가상 머신에 영구적으로 할당됩니다.
1. Windows Virtual Desktop 클라이언트에 로그인하여 데스크톱에 액세스할 수 있는 사용자를 입력합니다. 쉼표로 구분된 목록을 사용합니다. 예를 들어 `user1@contoso.com` 및 `user2@contoso.com` 액세스를 할당하려면 *`user1@contoso.com,user2@contoso.com`* 을 입력합니다.
1. **서비스 메타데이터 위치**에 대해 Active Directory 서버에 연결된 가상 네트워크와 동일한 위치를 선택합니다.

   >[!IMPORTANT]
   >순수한 Azure AD DS(Azure Active Directory Domain Services) 및 Azure AD(Azure Active Directory) 솔루션을 사용하는 경우 도메인 조인 및 자격 증명 오류를 방지하기 위해 호스트 풀을 Azure AD DS와 동일한 지역에 배포해야 합니다.

1. 완료되면 **다음: 가상 머신 구성**을 선택합니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

**가상 머신 구성** 탭에서,

1. 기본값을 그대로 적용하거나 가상 머신의 수와 크기를 사용자 지정합니다.

    >[!NOTE]
    >찾고 있는 특정 가상 머신 크기가 가상 머신 크기 선택기에 표시되지 않는 경우 이는 Azure Marketplace 도구에 아직 온보딩되지 않았기 때문입니다. 크기를 요청하려면 [Windows Virtual Desktop UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)에서 요청을 만들거나 기존 요청에 찬성합니다.

1. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어 *prefix*를 입력하면 가상 머신이 **prefix-0**, **prefix-1** 등으로 호출됩니다.
1. 완료되면 **다음: 가상 머신 설정**을 선택합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

**가상 머신 설정** 탭에서,

1. **이미지 원본**의 경우 원본을 선택하고 원본을 찾고 저장할 방법에 대한 적절한 정보를 입력합니다. Blob 스토리지, 관리 이미지 및 갤러리에 대한 옵션이 다릅니다.

   관리 디스크를 사용하지 않도록 선택하는 경우 *.vhd* 파일이 포함된 스토리지 계정을 선택합니다.
1. 사용자 이름 및 암호를 입력합니다. 이 계정은 가상 머신을 Active Directory 도메인에 조인하는 도메인 계정이어야 합니다. 이 동일한 사용자 이름과 암호는 로컬 계정으로 가상 머신에서 생성됩니다. 이러한 로컬 계정은 나중에 다시 설정할 수 있습니다.

   >[!NOTE]
   > 가상 머신을 Azure AD DS 환경에 조인하는 경우 도메인 조인 사용자가 [AAD DC 관리자 그룹](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버인지 확인합니다.
   >
   > 또한 계정은 Azure AD DS 관리형 도메인 또는 Azure AD 테넌트에 속해야 합니다. Azure AD 테넌트와 연결된 외부 디렉터리의 계정은 도메인 가입 프로세스 중에 올바르게 인증되지 않습니다.

1. Active Directory 서버에 연결된 **가상 네트워크**를 선택한 다음, 가상 머신을 호스팅하는 서브넷을 선택합니다.
1. 완료되면 **다음: Windows Virtual Desktop 테넌트 정보**를 선택합니다.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop 테넌트 정보

**Windows Virtual Desktop 테넌트 정보** 탭에서,

1. **Windows Virtual Desktop 테넌트 그룹 이름**의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
1. **Windows Virtual Desktop 테넌트 이름**의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
1. Windows Virtual Desktop 테넌트 RDS 소유자로 인증하는 데 사용할 자격 증명 유형을 지정합니다. UPN 또는 서비스 주체와 암호를 입력합니다.

   [PowerShell 자습서를 사용하여 서비스 주체 및 역할 할당 만들기](create-service-principal-role-powershell.md)를 완료했으면 **서비스 주체**를 선택합니다.

1. **서비스 주체**의 경우 **Azure AD 테넌트 ID**에 대해 서비스 주체가 포함된 Azure AD 인스턴스의 테넌트 관리자 계정을 입력합니다. 암호 자격 증명을 사용하는 서비스 주체만 지원됩니다.
1. 완료되면 **다음: 리뷰 + 만들기**를 클릭합니다.

## <a name="complete-setup-and-create-the-virtual-machine"></a>설치 완료 및 가상 머신 만들기

**검토 및 만들기**에서 설치 정보를 검토합니다. 항목을 변경해야 하는 경우 뒤로 이동하여 변경합니다. 준비가 되면 **만들기**를 선택하여 호스트 풀을 배포합니다.

만드는 가상 머신의 수에 따라 이 프로세스를 완료하는 데 30분 이상 걸릴 수 있습니다.

>[!IMPORTANT]
> Azure에서 Windows Virtual Desktop 환경을 보호하기 위해 가상 머신에서 3389 인바운드 포트를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 호스트 풀의 가상 머신에 액세스하기 위해 열린 3389 인바운드 포트가 필요하지 않습니다.
>
> 문제를 해결하기 위해 3389 포트를 열어야 하는 경우 Just-In-Time 액세스를 사용하는 것이 좋습니다. 자세한 내용은 [Just-In-Time 액세스를 사용하여 관리 포트 보호](../../security-center/security-center-just-in-time.md)를 참조하세요.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(선택 사항) 데스크톱 애플리케이션 그룹에 추가 사용자 할당

Azure Marketplace에서 풀 만들기가 완료되면 더 많은 사용자를 데스크톱 애플리케이션 그룹에 할당할 수 있습니다. 더 이상 추가하지 않으려면 이 섹션을 건너뛰세요.

사용자를 데스크톱 애플리케이션 그룹에 할당하려면 다음을 수행합니다.

1. PowerShell 창을 엽니다.

1. 다음 명령을 실행하여 Windows Virtual Desktop 환경에 로그인합니다.

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. 다음 명령을 사용하여 사용자를 데스크톱 애플리케이션 그룹에 추가합니다.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   사용자의 UPN은 Azure AD의 사용자 ID와 일치해야 합니다(예: *user1@contoso.com* ). 여러 사용자를 추가하려면 각 사용자에 대해 이 명령을 실행합니다.

데스크톱 애플리케이션 그룹에 추가되는 사용자는 지원되는 원격 데스크톱 클라이언트를 사용하여 Windows Virtual Desktop에 로그인하고 세션 데스크톱에 대한 리소스를 볼 수 있습니다.

현재 지원되는 클라이언트는 다음과 같습니다.

* [Windows 7 및 Windows 10용 원격 데스크톱 클라이언트](connect-windows-7-10-2019.md)
* [Windows Virtual Desktop 웹 클라이언트](connect-web-2019.md)

## <a name="next-steps"></a>다음 단계

호스트 풀을 만들고, 해당 데스크톱에 액세스할 수 있는 사용자를 할당했습니다. 호스트 풀을 RemoteApp 프로그램으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대해 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](manage-app-groups-2019.md)
