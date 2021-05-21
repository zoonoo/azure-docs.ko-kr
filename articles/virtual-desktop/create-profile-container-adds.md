---
title: FSLogix 프로필 컨테이너 Azure Files Active Directory Domain Services 만들기 - Azure
description: 이 문서에서는 Azure Files 및 Azure Active Directory Domain Services를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법을 설명합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 21db49ade3b6727775c10321e10aff2e0bf231f1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447950"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Azure Files 및 Azure AD DS를 사용하여 프로필 컨테이너 만들기

이 문서에서는 Azure Files 및 AD DS(Azure Active Directory Domain Services)를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 Azure AD DS 인스턴스를 이미 설정했다고 가정합니다. 아직 없는 경우 먼저 [기본 관리되는 도메인 만들기](../active-directory-domain-services/tutorial-create-instance.md)의 지침을 따라 만든 다음, 여기로 돌아옵니다.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS 관리자 추가

관리자를 추가하려면 새 사용자를 만들고 권한을 부여합니다.

관리자를 추가하려면 다음을 수행합니다.

1. 사이드바에서 **Azure Active Directory**, **모든 사용자**, **새 사용자** 를 차례로 선택합니다.

2.  사용자 세부 정보를 필드에 입력합니다.

3. 화면 왼쪽의 Azure Active Directory 창에서 **그룹** 을 선택합니다.

4. **AAD DC 관리자** 그룹을 선택합니다.

5. 왼쪽 창에서 **구성원** 을 선택한 후 기본 창에서 **구성원 추가** 를 선택합니다. 그러면 Azure AD에서 사용할 수 있는 모든 사용자의 목록이 표시됩니다. 방금 만든 사용자 프로필의 이름을 선택합니다.

## <a name="set-up-an-azure-storage-account"></a>Azure Storage 계정 설정

이제 SMB(서버 메시지 블록)를 통해 Azure AD DS 인증을 사용하도록 설정해야 합니다.

인증을 사용하도록 설정하려면 다음을 수행합니다.

1. 아직 하지 않았다면 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)의 지침에 따라 범용 v2 Azure Storage 계정을 설정하고 배포합니다.

2. 계정 설정을 마쳤으면 **리소스로 이동** 을 선택합니다.

3. 화면 왼쪽에 있는 창에서 **구성** 을 선택한 후 기본 창에서 **Azure Files용 Azure Active Directory 인증** 을 사용하도록 설정합니다. 완료되면 **저장** 을 선택합니다.

4. 화면 왼쪽에 있는 창에서 **개요** 를 선택한 후 기본 창에서 **파일** 을 선택합니다.

5. **파일 공유** 를 선택하고 화면 오른쪽에 표시되는 필드에 **이름** 및 **할당량** 을 입력합니다.

## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

다른 사용자가 파일 공유에 액세스하려면 액세스 권한이 필요합니다. 이렇게 하려면 각 사용자에게 적절한 액세스 권한을 가진 역할을 할당해야 합니다.

사용자에게 액세스 권한을 할당하려면 다음을 수행합니다.

1. Azure Portal에서 [Azure Storage 계정 설정](#set-up-an-azure-storage-account)에서 만든 파일 공유를 엽니다.

2. **Access Control(IAM)** 을 선택합니다.

3. **역할 할당 추가** 를 선택합니다.

4. **역할 할당 추가** 탭의 역할 목록에서 적절한 기본 제공 역할을 선택합니다. 적절한 사용 권한을 얻으려면 적어도 **스토리지 파일 데이터 SMB 공유 기여자** 를 선택해야 합니다.

5. **액세스 할당** 에서 **Azure Active Directory 사용자, 그룹 또는 서비스 주체** 를 선택합니다.

6. 대상 Azure Active Directory ID의 이름 또는 이메일 주소를 선택합니다.

7. **저장** 을 선택합니다.

## <a name="get-the-storage-account-access-key"></a>스토리지 계정 액세스 키 가져오기

다음으로, 스토리지 계정의 액세스 키를 가져와야 합니다.

스토리지 계정 액세스 키를 가져오려면 다음을 수행합니다.

1. Azure Portal 사이드바에서 **스토리지 계정** 을 선택합니다.

2. 스토리지 계정 목록에서 이전 단계에서 Azure AD DS를 사용하도록 설정하고 사용자 지정 역할을 만든 계정을 선택합니다.

3. **설정** 아래에서 **액세스 키** 를 선택하고 **key1** 에서 키를 복사합니다.

4. **Virtual Machines** 탭으로 이동하고 호스트 풀의 일부가 될 VM을 찾습니다.

5. **Virtual Machines(adVM)** 아래에서 VM(가상 머신)의 이름을 선택하고 **연결** 을 선택합니다.

    그러면 자체 자격 증명을 사용하여 VM에 로그인하는 데 사용할 수 있는 RDP 파일이 다운로드됩니다.

    > [!div class="mx-imgBorder"]
    > ![가상 머신에 연결 창의 RDP 탭 스크린샷.](media/rdp-tab.png)

6. VM에 로그인했으면 관리자 권한으로 명령 프롬프트를 실행합니다.

7. 다음 명령 실행:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - `<desired-drive-letter>`를 원하는 드라이브 문자(예: `y:`)로 바꿉니다.
    - `<storage-account-name>`의 모든 인스턴스를 앞서 지정한 스토리지 계정의 이름으로 바꿉니다.
    - `<share-name>`을 앞서 만든 공유의 이름으로 바꿉니다.
    - `<storage-account-key>`를 Azure의 스토리지 계정 키로 바꿉니다.

    예를 들어:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. 다음 명령을 실행하여 Windows Virtual Desktop 사용자가 다른 사용자의 프로필 컨테이너에 대한 액세스를 차단하면서 자신의 프로필 컨테이너를 만들도록 할 수 있습니다.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - `<mounted-drive-letter>`를 드라이브를 매핑하는 데 사용한 드라이브의 문자로 바꿉니다.
    - `<user-email>`을 사용자의 UPN 또는 공유에 대한 액세스 권한이 필요한 사용자를 포함하는 Active Directory 그룹으로 바꿉니다.

    예를 들어:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>프로필 컨테이너 만들기

이제 프로필을 사용할 준비가 되었으므로 FSLogix 프로필 컨테이너를 만들어 보겠습니다.

FSLogix 프로필 컨테이너를 구성하려면 다음을 수행합니다.

1. 이 문서의 시작 부분에서 구성한 세션 호스트 VM에 로그인한 다음, [FSLogix 에이전트를 다운로드하여 설치](/fslogix/install-ht/)합니다.

2. 다운로드한 FSLogix 에이전트 파일의 압축을 풀고 **x64** > **릴리스** 로 이동한 다음, **FSLogixAppsSetup.exe** 를 엽니다.

3. 설치 프로그램이 시작되면 **사용 약관에 동의함** 을 선택합니다. 해당하는 경우 새 키를 제공합니다.

4. **설치** 를 선택합니다.

5. **C 드라이브** 를 연 다음, **프로그램 파일** > **FSLogix** > **앱** 으로 이동하여 FSLogix 에이전트가 제대로 설치되었는지 확인합니다.

     >[!NOTE]
     > 호스트 풀에 여러 VM이 있는 경우, 각 VM에 대해 1~5단계를 반복해야 합니다.

6. 관리자 권한으로 **레지스트리 편집기**(RegEdit)를 실행합니다.

7. **컴퓨터** > **HKEY_LOCAL_MACHINE** > **소프트웨어** > **FSLogix** 로 이동하고 **FSLogix** 를 마우스 오른쪽 단추로 클릭한 후 **새로 만들기** 를 선택한 다음, **키** 를 선택합니다.

8. **프로필** 이라는 새 키를 만듭니다.

9.  **프로필** 을 마우스 오른쪽 단추로 클릭하고 **새로 만들기** 를 선택한 다음, **DWORD(32비트) 값** 을 선택합니다. 값의 이름을 **Enabled** 로 설정하고 **데이터** 값을 **1** 로 설정합니다.

    > [!div class="mx-imgBorder"]
    > ![프로필 키의 스크린샷. REG_DWORD 파일이 강조 표시되고 데이터 값이 1로 설정됩니다.](media/dword-value.png)

10. **프로필** 을 마우스 오른쪽 단추로 클릭하고 **새로 만들기** 를 선택한 다음, **다중 문자열 값** 을 선택합니다. 값의 이름을 **VHDLocations** 로 설정하고 Azure Files 공유 `\\fsprofile.file.core.windows.net\share`의 URI를 데이터 값으로 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![VHDLocations 파일을 보여 주는 프로필 키의 스크린샷. 해당 데이터 값은 Azure Files 공유의 URI를 보여 줍니다.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>세션 호스트에 사용자 할당

이제 세션 호스트에 사용자를 할당해야 합니다.

사용자를 할당하려면 다음을 수행합니다.

1. 관리자 권한으로 Windows PowerShell을 실행한 후 PowerShell에서 다음 cmdlet을 실행하여 Windows 가상 데스크톱에 로그인합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   자격 증명을 입력하라는 메시지가 표시되면 Windows 가상 데스크톱 테넌트에 대한 TenantCreator, RDS 소유자 또는 RDS 기여자 역할을 부여받은 사용자를 입력합니다.

2. 다음 cmdlet을 실행하여 사용자를 원격 데스크톱 그룹에 할당합니다.

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    이전 cmdlet과 마찬가지로, `<your-wvd-tenant>`, `<wvd-pool>` 및 `<user-principal>`을 관련 값으로 바꾸어야 합니다.

    예를 들어:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>프로필이 작동하는지 확인

이제 생성된 프로필이 있고 올바르게 작동하는지 확인하기만 하면 됩니다.

프로필을 확인하려면 다음을 수행합니다.

1. 브라우저를 열고 [Windows 가상 데스크톱 웹 클라이언트](https://rdweb.wvd.microsoft.com/arm/webclient)로 이동합니다.

2. 원격 데스크톱 그룹에 할당된 사용자 계정으로 로그인합니다.

3. 사용자 세션이 설정되면 Azure Portal을 열고 관리 계정으로 로그인합니다.

4. 사이드바에서 **스토리지 계정** 을 선택합니다.

5. 세션 호스트 풀의 파일 공유로 구성하고 Azure AD DS와 함께 사용하도록 설정한 스토리지 계정을 선택합니다.

6. **파일** 아이콘을 선택한 후 공유를 확장합니다.

    모든 항목이 올바르게 설정되면 `<user SID>-<username>` 같은 형식으로 이름이 지정된 **디렉터리** 가 표시됩니다.

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 만드는 다른 방법을 찾고 있는 경우 다음 문서를 확인하세요.

- [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)
- [Azure NetApp Files를 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)

[FSLogix 프로필 컨테이너 및 Azure 파일](fslogix-containers-azure-files.md)에서 Azure 파일용 FSlogix 컨테이너와 관련된 개념에 대한 자세한 정보를 찾을 수 있습니다.
