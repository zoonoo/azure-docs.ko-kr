---
title: Windows Virtual Desktop 호스트 풀 Azure Marketplace - Azure
description: Azure Marketplace를 사용하여 Windows Virtual Desktop 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127996"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>자습서: Azure Marketplace을 사용 하 여 호스트 풀 만들기

이 자습서에서는 Microsoft Azure Marketplace 제공을 사용 하 여 Windows 가상 데스크톱 테 넌 트 내에서 호스트 풀을 만드는 방법에 대해 알아봅니다.

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 자습서의 태스크는 다음과 같습니다.

> [!div class="checklist"]
>
> * Windows Virtual Desktop에서 호스트 풀 만들기
> * Azure 구독에서 VM으로 리소스 그룹 만들기
> * VM을 Active Directory 도메인에 조인
> * VM을 Windows Virtual Desktop에 등록

## <a name="prerequisites"></a>사전 요구 사항

* 가상 데스크톱의 테 넌 트. 이전 [자습서](tenant-setup-azure-active-directory.md) 에서는 테 넌 트를 만듭니다.
* [Windows 가상 데스크톱 PowerShell 모듈](/powershell/windows-virtual-desktop/overview/)입니다.

이 모듈이 있으면 다음 cmdlet을 실행 하 여 계정에 로그인 합니다.

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

그런 후 다음 섹션의 지침에 따라 적절 한 탭에 대 한 정보를 입력 합니다.

### <a name="basics"></a>기본 사항

**기본** 탭에서 수행할 수 있는 작업은 다음과 같습니다.

1. **구독**을 선택합니다.
1. **리소스 그룹**에 대해 **새로 만들기** 를 선택 하 고 새 리소스 그룹의 이름을 제공 합니다.
1. **지역을**선택 합니다.
1. Windows 가상 데스크톱 테 넌 트 내에서 고유한 호스트 풀의 이름을 입력 합니다.
1. **데스크톱 종류**를 선택 합니다. **개인**을 선택 하는 경우이 호스트 풀에 연결 하는 각 사용자는 가상 컴퓨터에 영구적으로 할당 됩니다.
1. Windows 가상 데스크톱 클라이언트에 로그인 하 고 데스크톱에 액세스할 수 있는 사용자를 입력 합니다. 쉼표로 구분 된 목록을 사용 합니다. 예를 들어 `user1@contoso.com`를 할당 하 고 액세스를 `user2@contoso.com` 하려면을 입력 *`user1@contoso.com,user2@contoso.com`*
1. **서비스 메타 데이터 위치**에 대해 Active Directory 서버에 연결 된 가상 네트워크와 동일한 위치를 선택 합니다.

   >[!IMPORTANT]
   >순수한 Azure Active Directory Domain Services (Azure AD DS) 및 Azure Active Directory (Azure AD) 솔루션을 사용 하는 경우 도메인 가입 및 자격 증명 오류가 발생 하지 않도록 하려면 Azure AD DS와 동일한 지역에 호스트 풀을 배포 해야 합니다.

1. **다음: virtual Machines 구성**을 선택 합니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

**가상 컴퓨터 구성** 탭에서 다음을 수행 합니다.

1. 기본값을 그대로 적용 하거나 가상 머신의 수와 크기를 사용자 지정 합니다.

    >[!NOTE]
    >원하는 특정 가상 머신 크기가 크기 선택기에 표시 되지 않는 경우이는 Azure Marketplace 도구로 아직 등록 하지 않았기 때문입니다. 크기를 요청 하려면 [Windows 가상 데스크톱 UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)에서 요청을 만들거나 기존 요청을 응답 합니다.

1. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어, *접두사*를 입력 하면 가상 컴퓨터는 **접두사-0**, **접두사-1**등으로 호출 됩니다.
1. **다음: 가상 머신 설정**을 선택 합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

**가상 컴퓨터 설정** 탭의 경우:

1. **이미지 원본**의 경우 원본을 선택하고 원본을 찾고 저장할 방법에 대한 적절한 정보를 입력합니다. Blob 저장소, 관리 되는 이미지 및 갤러리에 대 한 옵션이 다릅니다.

   관리 디스크를 사용 하지 않도록 선택 하는 경우 *.vhd* 파일이 포함 된 저장소 계정을 선택 합니다.
1. 사용자 계정 이름 및 암호를 입력 합니다. 이 계정은 가상 컴퓨터를 Active Directory 도메인에 가입 시킬 도메인 계정 이어야 합니다. 이 동일한 사용자 이름과 암호는 로컬 계정으로 가상 머신에서 생성됩니다. 이러한 로컬 계정은 나중에 다시 설정할 수 있습니다.

   >[!NOTE]
   > 가상 컴퓨터를 Azure AD DS 환경에 가입 하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 구성원 인지 확인 합니다.
   >
   > 또한 계정은 Azure AD DS 관리 되는 도메인 또는 Azure AD 테 넌 트의 일부 여야 합니다. Azure AD 테넌트와 연결된 외부 디렉터리의 계정은 도메인 가입 프로세스 중에 올바르게 인증되지 않습니다.

1. Active Directory 서버에 연결 된 **가상 네트워크** 를 선택한 후 가상 컴퓨터를 호스트할 서브넷을 선택 합니다.
1. **다음: Windows 가상 데스크톱 정보**를 선택 합니다.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop 테넌트 정보

**Windows 가상 데스크톱 테 넌 트 정보** 탭의 경우:

1. **Windows Virtual Desktop 테넌트 그룹 이름**의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
1. **Windows Virtual Desktop 테넌트 이름**의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
1. Windows Virtual Desktop 테넌트 RDS 소유자로 인증하는 데 사용할 자격 증명 유형을 지정합니다. UPN 또는 서비스 주체와 암호를 입력 합니다.

   [PowerShell 자습서를 사용하여 서비스 주체 및 역할 할당 만들기](./create-service-principal-role-powershell.md)를 완료했으면 **서비스 주체**를 선택합니다.

1. **서비스 사용자**의 경우, **azure ad 테 넌 트 ID**에 대해 서비스 주체를 포함 하는 azure ad 인스턴스에 대 한 테 넌 트 관리자 계정을 입력 합니다. 암호 자격 증명을 사용하는 서비스 주체만 지원됩니다.
1. **다음: 검토 + 만들기**를 선택 합니다.

## <a name="complete-setup-and-create-the-virtual-machine"></a>설치 완료 및 가상 머신 만들기

**검토 및 만들기**에서 설치 정보를 검토 합니다. 항목을 변경 해야 하는 경우 뒤로 이동 하 여 변경 합니다. 준비가 되 면 **만들기** 를 선택 하 여 호스트 풀을 배포 합니다.

만드는 가상 컴퓨터의 수에 따라이 프로세스를 완료 하는 데 30 분 이상이 걸릴 수 있습니다.

>[!IMPORTANT]
> Azure에서 Windows 가상 데스크톱 환경을 보호 하기 위해 가상 머신에서 인바운드 포트 3389을 열지 않는 것이 좋습니다. 사용자가 호스트 풀의 가상 컴퓨터에 액세스 하려면 Windows 가상 데스크톱에 오픈 인바운드 포트 3389이 필요 하지 않습니다.
>
> 문제 해결을 위해 포트 3389을 열어야 하는 경우 just-in-time 액세스를 사용 하는 것이 좋습니다. 자세한 내용은 [just-in-time 액세스를 사용 하 여 관리 포트 보호](../security-center/security-center-just-in-time.md)를 참조 하세요.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(선택 사항) 데스크톱 애플리케이션 그룹에 추가 사용자 할당

Azure Marketplace 풀 만들기를 완료 한 후 데스크톱 응용 프로그램 그룹에 더 많은 사용자를 할당할 수 있습니다. 더 이상 추가 하지 않으려면이 섹션을 건너뜁니다.

데스크톱 응용 프로그램 그룹에 사용자를 할당 하려면:

1. PowerShell 창을 엽니다.

1. 다음 명령을 실행 하 여 Windows 가상 데스크톱 환경에 로그인 합니다.

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. 다음 명령을 사용 하 여 데스크톱 응용 프로그램 그룹에 사용자를 추가 합니다.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   사용자의 UPN은 Azure AD의 사용자 id와 일치 해야 합니다 (예: *user1@contoso.com* ). 여러 사용자를 추가 하려면 각 사용자에 대해 명령을 실행 합니다.

데스크톱 응용 프로그램 그룹에 추가 하는 사용자는 지원 되는 원격 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱에 로그인 하 고 세션 바탕 화면에 대 한 리소스를 볼 수 있습니다.

현재 지원되는 클라이언트는 다음과 같습니다.

* [Windows 7 및 Windows 10용 원격 데스크톱 클라이언트](connect-windows-7-and-10.md)
* [Windows Virtual Desktop 웹 클라이언트](connect-web.md)

## <a name="next-steps"></a>다음 단계

호스트 풀을 만들고 사용자에 게 해당 데스크톱에 액세스할 수 있는 사용자를 할당 했습니다. 호스트 풀을 RemoteApp 프로그램으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대해 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
