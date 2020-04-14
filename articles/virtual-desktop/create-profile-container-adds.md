---
title: FSLogix 프로필 컨테이너 만들기 Azure 파일 활성 디렉터리 도메인 서비스 - Azure
description: 이 문서에서는 Azure 파일 및 Azure Active Directory 도메인 서비스를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법에 대해 설명합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265773"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Azure 파일을 사용하여 FSLogix 프로필 컨테이너 만들기

이 문서에서는 Azure 파일 및 Azure Active Directory 도메인 서비스(AD DS)를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법을 보여 주십니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure AD DS 인스턴스를 이미 설정했다고 가정합니다. 아직 없는 경우 [기본 관리 되는 도메인 만들기의](../active-directory-domain-services/tutorial-create-instance.md) 지침을 먼저 따라 다음 여기로 돌아갑니다.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS 관리자 추가

관리자를 추가하려면 새 사용자를 만들고 사용자에게 권한을 부여합니다.

관리자를 추가하려면 다음 단계를 수행합니다.

1. 사이드바에서 **Azure Active Directory를** 선택한 다음 **모든 사용자를**선택한 다음 새 **사용자를**선택합니다.

2.  필드에 사용자 세부 정보를 입력합니다.

3. 화면 왼쪽에 있는 Azure Active Directory 창에서 **그룹**을 선택합니다.

4. **AAD DC 관리자** 그룹을 선택합니다.

5. 왼쪽 창에서 **멤버를**선택한 다음 기본 창에서 **멤버 추가를** 선택합니다. Azure AD에서 사용할 수 있는 모든 사용자 목록이 표시됩니다. 방금 만든 사용자 프로필의 이름을 선택합니다.

## <a name="set-up-an-azure-storage-account"></a>Azure 저장소 계정 설정

이제 SMB(서버 메시지 블록)를 통해 Azure AD DS 인증을 사용하도록 설정합니다. 

인증을 사용하려면 다음을 수행합니다.

1. 아직 설정하지 않은 경우 Azure Storage 계정 [만들기의](../storage/common/storage-account-create.md)지침에 따라 범용 v2 Azure Storage 계정을 설정하고 배포합니다.

2. 계정 설정이 완료되면 **리소스로 이동을**선택합니다.

3. 화면 왼쪽의 창에서 **구성을** 선택한 다음 기본 창에서 **Azure 파일에 대한 Azure Active Directory 인증을** 사용하도록 설정합니다. 완료되면 **저장**을 선택합니다.

4. 화면 왼쪽의 창에서 **개요를** 선택한 다음 기본 창에서 **파일을** 선택합니다.

5. **파일 공유를** 선택하고 화면 오른쪽에 나타나는 필드에 **이름** 및 **할당량을** 입력합니다.

## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

다른 사용자는 파일 공유에 액세스하려면 액세스 권한이 필요합니다. 이렇게 하려면 각 사용자에게 적절한 액세스 권한이 있는 역할을 할당해야 합니다.

사용자에게 액세스 권한을 할당하려면 다음을 수행하십시오.

1. Azure 포털에서 [Azure 저장소 계정 설정에서](#set-up-an-azure-storage-account)만든 파일 공유를 엽니다.

2. **액세스 제어(IAM)를**선택합니다.

3. **역할 할당 추가를**선택합니다.

4. 역할 **할당 추가** 탭에서 역할 목록에서 적절한 기본 제공 역할을 선택합니다. 적절한 권한을 얻으려면 계정에 대한 **저장소 파일 데이터 SMB 공유 기여자를** 적어도 선택해야 합니다.

5. **에 대한 액세스 할당에**대해 Azure Active **Directory 사용자, 그룹 또는 서비스 주체를**선택합니다.

6. 대상 Azure Active Directory ID의 이름 또는 전자 메일 주소를 선택합니다.

7. **저장**을 선택합니다.

## <a name="get-the-storage-account-access-key"></a>저장소 계정 액세스 키 받기

다음으로 저장소 계정에 대한 액세스 키를 받아야 합니다.

저장소 계정 액세스 키를 받으려면 다음을 수행하십시오.

1. Azure 포털 사이드바에서 **저장소 계정을**선택합니다.

2. 저장소 계정 목록에서 Azure AD DS를 사용하도록 설정하고 위의 단계에서 사용자 지정 역할을 만든 계정을 선택합니다.

3. **설정에서** **키 액세스(키)를** 선택하고 **key1에서**키를 복사합니다.

4. **가상 시스템** 탭으로 이동하여 호스트 풀의 일부가 될 VM을 찾습니다.

5. **가상 컴퓨터(adVM)에서 가상** 시스템(VM)의 이름을 선택하고 **연결** 을 선택합니다.

    이렇게 하면 자체 자격 증명으로 VM에 로그인할 수 있는 RDP 파일이 다운로드됩니다.

    ![가상 시스템 창에 연결의 RDP 탭의 스크린샷입니다.](media/rdp-tab.png)

6. VM에 로그인한 경우 관리자로 명령 프롬프트를 실행합니다.

7. 다음 명령 실행:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - 선택한 `<desired-drive-letter>` 드라이브 문자(예: `y:`)로 바꿉니다.
    - 모든 인스턴스를 `<storage-account-name>` 이전에 지정한 저장소 계정의 이름으로 바꿉니다.
    - 이전에 `<share-name>` 만든 공유 이름으로 바꿉니다.
    - Azure의 저장소 계정 키로 바꿉습니다. `<storage-account-key>`

    다음은 그 예입니다.  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 다음 명령을 실행하여 사용자에게 Azure Files 공유에 대한 전체 액세스 권한을 부여합니다.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - 사용자가 `<mounted-drive-letter>` 사용할 드라이브의 문자로 바꿉습니다.
    - 이 `<user-email>` 프로필을 사용하여 세션 호스트 VM에 액세스할 사용자의 UPN으로 바꿉습니다.

    다음은 그 예입니다.
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>프로필 컨테이너 만들기

이제 프로필을 사용할 준비가 되었으므로 FSLogix 프로필 컨테이너를 만들어 보겠습니다.

FSLogix 프로필 컨테이너를 구성하려면 다음을 수행하십시오.

1. 이 문서의 시작 부분에서 구성한 세션 호스트 VM에 로그인한 다음 [FSLogix 에이전트를 다운로드하여 설치합니다.](/fslogix/install-ht/)

2. 다운로드한 FSLogix 에이전트 파일의 압축을 풀고 **x64** > **릴리스로**이동한 다음 **FSLogixAppsSetup.exe를 엽니다.**

3. 설치 프로그램이 시작되면 **라이센스 이용 약관에 동의합니다.** 해당하는 경우 새 키를 제공합니다.

4. **설치**를 선택합니다.

5. **드라이브 C를**열고 **프로그램 파일** > **FSLogix** > **앱으로** 이동하여 FSLogix 에이전트가 제대로 설치되었는지 확인합니다.

     >[!NOTE]
     > 호스트 풀에 VM이 여러 개인 경우 각 VM에 대해 1~5단계를 반복해야 합니다.

6. 관리자로 레지스트리 편집기(RegEdit)를 실행합니다. **Registry Editor**

7. **컴퓨터** > **HKEY_LOCAL_MACHINE** > **소프트웨어** > **FSLogix로**이동, **FSLogix를**마우스 오른쪽 **Key**버튼으로 클릭 , **새로**를 선택합니다 .

8. 프로필이라는 새 **키를**만듭니다.

9.  **프로필을**마우스 오른쪽 단추로 클릭하고 **새로 를**선택한 다음 **DWORD(32비트) 값을 선택합니다.** 값의 이름을 사용하도록 설정하고 **데이터** 값을 **1로** **설정합니다.**

    ![프로필 키의 스크린샷입니다. REG_DWORD 파일이 강조 표시되고 해당 데이터 값이 1로 설정됩니다.](media/dword-value.png)

10. **프로필을**마우스 오른쪽 단추로 클릭하고 **새로**를 선택한 다음 **다중 문자열 값을**선택합니다. **VHDLocations** 값의 이름을 지정하고 Azure Files `\\fsprofile.file.core.windows.net\share` 공유에 대한 URI를 데이터 값으로 입력하도록 설정합니다.

    ![VHDLocations 파일을 보여주는 프로필 키의 스크린샷입니다. 해당 데이터 값은 Azure Files 공유에 대한 URI를 표시합니다.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>세션 호스트에 사용자 할당

이제 세션 호스트에 사용자를 할당해야 합니다.

사용자를 할당하려면 다음 을 수행합니다.

1. 관리자로 Windows PowerShell을 실행한 다음 다음 cmdlet을 실행하여 PowerShell을 사용하여 Windows 가상 데스크톱에 로그인합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   자격 증명을 입력하라는 메시지가 표시되면 Windows 가상 데스크톱 테넌트에서 테넌트 작성자, RDS 소유자 또는 RDS 기여자 역할이 부여된 동일한 사용자를 입력합니다.

2. 다음 cmdlet을 실행하여 사용자를 원격 데스크톱 그룹에 할당합니다.

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    이전 cmdlet과 마찬가지로 에서 `<your-wvd-tenant>` `<wvd-pool>`를 대체하고 `<user-principal>` 관련 값으로 바꿔야 합니다.

    다음은 그 예입니다.

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>프로필이 작동하는지 확인

이제 만든 프로필이 존재하고 의도한 대로 작동하는지 확인하기만 하면 됩니다.

프로필을 확인하려면 다음 을 수행하세요.

1. 브라우저를 열고 [Windows 가상 데스크톱 웹 클라이언트로](https://rdweb.wvd.microsoft.com/webclient/index.html)이동합니다.

2. 원격 데스크톱 그룹에 할당된 사용자 계정으로 로그인합니다.

3. 사용자 세션이 설정되면 Azure 포털을 열고 관리 계정으로 로그인합니다.

4. 사이드바에서 **저장소 계정을**선택합니다.

5. 세션 호스트 풀의 파일 공유로 구성하고 Azure AD DS로 사용하도록 설정한 저장소 계정을 선택합니다.

6. **파일** 아이콘을 선택한 다음 공유를 확장합니다.

    모든 것이 올바르게 설정된 경우 다음과 같이 서식이 지정된 이름이 있는 **디렉터리가** `<user SID>-<username>`표시됩니다.

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 만드는 다른 방법을 찾고 있다면 다음 문서를 확인하십시오.

- [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너를 만듭니다.](create-host-pools-user-profile.md)
- [Azure NetApp 파일을 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)

FSLogix 프로필 컨테이너 및 Azure 파일에서 Azure 파일에 대한 [FSlogix 컨테이너와](fslogix-containers-azure-files.md)관련된 개념에 대한 자세한 정보를 찾을 수 있습니다.
