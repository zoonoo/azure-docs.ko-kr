---
title: FSLogix 프로필 컨테이너 넷앱 윈도우 가상 데스크톱 - Azure
description: Windows 가상 데스크톱에서 Azure NetApp 파일을 사용하여 FSLogix 프로필 컨테이너를 만드는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 272188b50fe59435031a4a2fb9c252f3f358bb6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535734"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Azure NetApp 파일을 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기

[Windows 가상 데스크톱 서비스에](overview.md)대한 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 사용하는 것이 좋습니다. FSLogix 프로필 컨테이너는 전체 사용자 프로필을 단일 컨테이너에 저장하며 Windows Virtual Desktop과 같은 비영구 원격 컴퓨팅 환경에서 프로파일을 로밍하도록 설계되었습니다. 로그인하면 컨테이너가 로컬로 지원되는 VHD(가상 하드 디스크)와 Hyper-V 가상 하드 디스크(VHDX)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 이러한 고급 필터 드라이버 기술을 통해 사용자 프로필을 즉시 사용할 수 있고 로컬 사용자 프로필과 똑같이 시스템에 표시할 수 있습니다. FSLogix 프로필 컨테이너에 대한 자세한 내용은 [FSLogix 프로필 컨테이너 및 Azure 파일을](fslogix-containers-azure-files.md)참조하십시오.

Windows 가상 데스크톱 환경에 대한 엔터프라이즈급 SMB 볼륨을 빠르고 안정적으로 프로비저닝하는 데 도움이 되는 사용하기 쉬운 Azure 네이티브 플랫폼 서비스인 [Azure NetApp Files를](https://azure.microsoft.com/services/netapp/)사용하여 FSLogix 프로필 컨테이너를 만들 수 있습니다. Azure NetApp 파일에 대한 자세한 내용은 [Azure NetApp 파일이란 무엇입니까?](../azure-netapp-files/azure-netapp-files-introduction.md)

이 가이드에서는 Azure NetApp 파일 계정을 설정하고 Windows 가상 데스크톱에서 FSLogix 프로필 컨테이너를 만드는 방법을 보여 줄 것입니다.

이 문서에서는 Windows 가상 데스크톱 환경에서 [호스트 풀을](create-host-pools-azure-marketplace.md) 설정하고 하나 이상의 테넌터로 그룹화한 것으로 가정합니다. 테넌트를 설정하는 방법을 알아보려면 [Windows 가상 데스크톱의 테넌트 만들기](tenant-setup-azure-active-directory.md) 및 기술 커뮤니티 블로그 [게시물을](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)참조하십시오.

이 가이드의 지침은 특히 Windows 가상 데스크톱 사용자를 위한 것입니다. Azure NetApp 파일을 설정하고 Windows 가상 데스크톱 외부에서 FSLogix 프로필 컨테이너를 만드는 방법에 대한 보다 일반적인 지침을 찾고 있는 경우 [Azure NetApp 파일 설정을 참조하고 NFS 볼륨 빠른 시작을 만듭니다.](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)

>[!NOTE]
>이 문서에서는 Azure NetApp 파일 공유에 대한 액세스를 보호하는 모범 사례를 다루지 않습니다.

>[!NOTE]
>Azure의 다른 FSLogix 프로필 컨테이너 저장소 옵션에 대한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대한 저장소 옵션을](store-fslogix-profile.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

호스트 풀에 대한 FSLogix 프로필 컨테이너를 만들려면 다음을 수행해야 합니다.

- Windows 가상 데스크톱 설정 및 구성
- Windows 가상 데스크톱 호스트 풀 프로비전
- [Azure NetApp 파일 구독 사용](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Azure NetApp 파일 계정 설정

시작하려면 Azure NetApp 파일 계정을 설정해야 합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 계정에 기여자 또는 관리자 권한이 있는지 확인합니다.

2. 검색 창 오른쪽에 있는 **Azure 클라우드 셸 아이콘을** 선택하여 Azure 클라우드 셸을 엽니다.

3. Azure 클라우드 셸이 열리면 **PowerShell**을 선택합니다.

4. Azure Cloud Shell을 처음 사용하는 경우 Azure NetApp 파일 및 Windows 가상 데스크톱을 보관하는 동일한 구독에서 저장소 계정을 만듭니다.

   ![창 아래쪽에 저장 저장 단추 만들기 버튼이 빨간색으로 강조 표시된 저장소 계정 창입니다.](media/create-storage-button.png)

5. Azure 클라우드 셸이 로드되면 다음 두 cmdlet을 실행합니다.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 창의 왼쪽에서 **모든 서비스를**선택합니다. 메뉴 상단에 나타나는 검색 상자에 **Azure NetApp 파일을** 입력합니다.

   ![모든 서비스 검색 상자에 "Azure NetApp 파일"을 입력하는 사용자의 스크린샷입니다. 검색 결과에 Azure NetApp 파일 리소스가 표시됩니다.](media/azure-netapp-files-search-box.png)


7. 검색 결과에서 **Azure NetApp 파일을** 선택한 다음 **을 선택합니다.**

8. **추가** 단추를 선택합니다.
9. 새 **NetApp 계정** 탭이 열리면 다음 값을 입력합니다.

    - **이름에**대 한 NetApp 계정 이름을 입력 합니다.
    - **구독의**경우 드롭다운 메뉴에서 4단계에서 설정한 저장소 계정에 대한 구독을 선택합니다.
    - **리소스 그룹의**경우 드롭다운 메뉴에서 기존 리소스 그룹을 선택하거나 새 **만들기를**선택하여 새 리소스 그룹을 만듭니다.
    - **위치의**경우 드롭다운 메뉴에서 NetApp 계정의 지역을 선택합니다. 이 리전은 세션 호스트 VM과 동일한 지역이어야 합니다.

   >[!NOTE]
   >Azure NetApp 파일은 현재 여러 지역에서 볼륨 의 탑재를 지원하지 않습니다.

10. 완료되면 **만들기를** 선택하여 NetApp 계정을 만듭니다.

## <a name="create-a-capacity-pool"></a>용량 풀 만들기

다음으로 새 용량 풀을 만듭니다. 

1. Azure NetApp 파일 메뉴로 이동하여 새 계정을 선택합니다.
2. 계정 메뉴에서 저장소 서비스 에서 **용량 풀을 선택합니다.**
3. **풀 추가를**선택합니다.
4. 새 **용량 풀** 탭이 열리면 다음 값을 입력합니다.

    - **Name의**경우 새 용량 풀의 이름을 입력합니다.
    - **서비스 수준에**대 한 드롭 다운 메뉴에서 원하는 값을 선택 합니다. 대부분의 **환경에서는 프리미엄을** 권장합니다.
       >[!NOTE]
       >프리미엄 설정은 256MBps인 프리미엄 서비스 수준에서 사용할 수 있는 최소 처리량을 제공합니다. 프로덕션 환경에 맞게 이 처리량을 조정해야 할 수 있습니다. 최종 처리량은 [처리량 제한에](../azure-netapp-files/azure-netapp-files-service-levels.md)설명된 관계를 기반으로 합니다.
    - **크기(TiB)의**경우 필요에 가장 적합한 용량 풀 크기를 입력합니다. 최소 크기는 4 TiB입니다.

5. 작업을 마쳤으면 **확인**을 선택합니다.

## <a name="join-an-active-directory-connection"></a>Active 디렉터리 연결 에 참여하기

그런 다음 Active 디렉터리 연결을 조인해야 합니다.

1. 페이지 왼쪽의 메뉴에서 **Active Directory 연결을** 선택한 다음 **조인** 단추를 선택하여 **활성 디렉터리 가입** 페이지를 엽니다.

   ![활성 디렉터리 연결 조인 메뉴의 스크린샷입니다.](media/active-directory-connections-menu.png)

2. 연결에 가입하려면 **활성 디렉터리 조인** 페이지에 다음 값을 입력합니다.

    - **기본 DNS의**경우 도메인 이름을 확인할 수 있는 환경에 DNS 서버의 IP 주소를 입력합니다.
    - **도메인의**경우 정규화된 도메인 이름(FQDN)을 입력합니다.
    - **SMB 서버(컴퓨터 계정) 접두사에**대해 컴퓨터 계정 이름에 부가할 문자열을 입력합니다.
    - **사용자 이름의**경우 도메인 가입을 수행할 수 있는 권한이 있는 계정 이름을 입력합니다.
    - **암호의**경우 계정의 암호를 입력합니다.

  >[!NOTE]
  >[Active Directory 연결 에 가입할](create-fslogix-profile-container.md#join-an-active-directory-connection) 때 만든 컴퓨터 계정이 **컴퓨터** 또는 기업의 관련 OU 아래의 도메인 컨트롤러에 나타났는 **것을**확인하는 것이 좋습니다.

## <a name="create-a-new-volume"></a>새 볼륨 만들기

다음으로 새 볼륨을 만들어야 합니다.

1. **볼륨을**선택한 다음 **볼륨 추가를**선택합니다.

2. 볼륨 **만들기** 탭이 열리면 다음 값을 입력합니다.

    - **볼륨 이름의**경우 새 볼륨의 이름을 입력합니다.
    - **용량 풀의**경우 드롭다운 메뉴에서 방금 만든 용량 풀을 선택합니다.
    - **GiB(할당량)의**경우 사용자 환경에 적합한 볼륨 크기를 입력합니다.
    - **가상 네트워크의**경우 드롭다운 메뉴에서 도메인 컨트롤러에 연결되는 기존 가상 네트워크를 선택합니다.
    - **서브넷에서** **새 만들기를 선택합니다.** 이 서브넷은 Azure NetApp 파일에 위임됩니다.

3.  **다음: 프로토콜을 \> ** 클릭하여 프로토콜 탭을 열고 볼륨 액세스 매개 변수를 구성합니다.

## <a name="configure-volume-access-parameters"></a>볼륨 액세스 매개 변수 구성

볼륨을 만든 후 볼륨 액세스 매개 변수를 구성합니다.

1.  **SMB를** 프로토콜 유형으로 선택합니다.
2.  **Active Directory** 드롭다운 메뉴의 구성에서 [Active Directory 연결 에](create-fslogix-profile-container.md#join-an-active-directory-connection)처음 연결한 디렉터리와 동일한 디렉터리를 선택합니다. 구독당 하나의 Active Directory로 제한됩니다.
3.  이름 **공유** 텍스트 상자에 세션 호스트 풀 및 해당 사용자가 사용하는 공유 이름을 입력합니다.

4.  페이지 하단에서 **검토 + 만들기를** 선택합니다. 그러면 유효성 검사 페이지가 열립니다. 볼륨의 유효성이 성공적으로 확인되면 **에서 만들기를**선택합니다.

5.  이 시점에서 새 볼륨이 배포되기 시작합니다. 배포가 완료되면 Azure NetApp 파일 공유를 사용할 수 있습니다.

6.  마운트 경로를 보려면 **리소스로 이동을** 선택하고 개요 탭에서 경로를 찾습니다.

    ![마운트 경로를 가리키는 빨간색 화살표가 있는 개요 화면의 스크린샷입니다.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>세션 호스트 가상 시스템(VM)에서 FSLogix 구성

이 섹션은 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기를](create-host-pools-user-profile.md)기반으로 합니다.

1. 세션 호스트 VM에서 원격으로 [FSLogix 에이전트 .zip 파일을 다운로드합니다.](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)

2. 다운로드한 파일의 압축을 해제합니다.

3. 파일에서 **x64** > **릴리스로** 이동하여 **FSLogixAppsSetup.exe를**실행합니다. 설치 메뉴가 열립니다.

4.  제품 키가 있는 경우 제품 키 텍스트 상자에 입력합니다.

5. **라이센스 이용 약관에 동의할**수 있는 확인란을 선택합니다.

6. **설치**을 선택합니다.

7. **C로\\이동:\\에이전트가\\** 설치된 지 확인하려면 파일 FSLogix 앱을 프로그래밍합니다.

8. 시작 메뉴에서 **RegEdit를** 관리자로 실행합니다.

9. **컴퓨터\\HKEY_LOCAL_MACHINE\\소프트웨어\\FSLogix로**이동합니다.

10. **프로필이라는 키를**만듭니다.

11.  데이터 값 1로 설정된 **REG_DWORD** 형식을 사용하여 **Enabled라는** 값을 **만듭니다.**

12. **다중 문자열** 유형으로 **VHDLocations라는** 값을 만들고 Azure NetApp 파일 공유에 대한 URI로 데이터 값을 설정합니다.

13. **DeleteLocalProfileWhenVHD라는** 값을 생성하여 로그인하기 전에 기존 로컬 프로필에 문제가 발생하지 않도록 DWORD 값 1을 적용합니다.

     >[!WARNING]
     >삭제로컬 프로필을 만들 때주의할 때VHD해야 값을 적용합니다. FSLogix 프로필 시스템에서 사용자에게 FSLogix 프로필이 있어야 하지만 로컬 프로필이 이미 있다고 판단하면 프로필 컨테이너는 로컬 프로필을 영구적으로 삭제합니다. 그러면 사용자는 새 FSLogix 프로필로 로그인됩니다.

## <a name="assign-users-to-session-host"></a>세션 호스트에 사용자 할당

1. **PowerShell ISE를** 관리자로 열고 Windows 가상 데스크톱에 로그인합니다.

2. 다음 cmdlet을 실행합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 자격 증명을 입력하면 Windows 가상 데스크톱 테넌트에서 테넌트 작성자 또는 RDS 소유자/RDS 기여자 역할을 사용하여 사용자의 자격 증명을 입력합니다.

4. 다음 cmdlet을 실행하여 사용자를 원격 데스크톱 그룹에 할당합니다.

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>사용자가 Azure NetApp 파일 공유에 액세스할 수 있는지 확인

1. 인터넷 브라우저를 열고 <https://rdweb.wvd.microsoft.com/webclient/index.html>로 이동합니다.

2. 원격 데스크톱 그룹에 할당된 사용자의 자격 증명으로 로그인합니다.

3. 사용자 세션을 설정하면 관리 계정으로 Azure 포털에 로그인합니다.

4. **Azure NetApp 파일**열기, Azure NetApp 파일 계정을 선택한 다음 **볼륨을**선택합니다. 볼륨 메뉴가 열리면 해당 볼륨을 선택합니다.

   ![볼륨 단추를 선택한 Azure 포털의 앞부분에서 설정한 NetApp 계정의 스크린샷입니다.](media/netapp-account.png)

5. **개요** 탭으로 이동하여 FSLogix 프로필 컨테이너가 공간을 사용하고 있는지 확인합니다.

6. 원격 데스크톱을 사용하여 호스트 풀의 모든 VM 부분에 직접 연결하고 **파일 탐색기를 엽니다.** 그런 다음 **마운트 경로로** 이동합니다(다음 예제에서는 \\ \\\\마운트 경로가 anf-VOL에 anf-SMB-3863.gt1107.onmicrosoft.com).

   이 폴더에는 다음 예제와 같은 프로파일 VHD(또는 VHDX)가 있어야 합니다.

   ![마운트 경로에 있는 폴더 내용의 스크린샷입니다. 내부에는 "Profile_ssbb"라는 단일 VHD 파일이 있습니다.](media/mount-path-folder.png)

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 사용하여 사용자 프로필 공유를 설정할 수 있습니다. 새 컨테이너를 사용하여 사용자 프로필 공유를 만드는 방법을 알아보려면 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기를](create-host-pools-user-profile.md)참조하십시오.
