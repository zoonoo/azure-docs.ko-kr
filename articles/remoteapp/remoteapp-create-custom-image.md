---
title: "Azure RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법 | Microsoft 문서"
description: "Azure RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법에 대해 알아봅니다. 하이브리드 또는 클라우드 컬렉션에서 이 템플릿을 사용할 수 있습니다."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: b9ec5b51-f7cd-470b-8545-d0fd714c5982
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c5b87c3a814b816e8bdc28d3e22b64c2197579af
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Azure RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

Azure RemoteApp은 Windows Server 2012 R2 템플릿 이미지를 사용하여 사용자와 공유할 모든 프로그램을 호스트합니다. 사용자 지정 RemoteApp 템플릿 이미지를 만들려면 기존 이미지에서 시작하거나 새 이미지를 만듭니다. 

> [!TIP]
> Azure VM에서 이미지를 만들 수 있다는 것을 알고 있나요? 이 방법을 사용하면 이미지를 가져오는 데 소요되는 시간을 크게 줄일 수 있습니다. [여기](remoteapp-image-on-azurevm.md)서 단계를 확인하세요.
> 
> 

Azure RemoteApp과 사용하기 위해 업로드할 수 있는 이미지의 요구 사항은 다음과 같습니다.

* 이미지 크기는 MB 단위의 배수여야 합니다. 크기가 정확한 배수가 아닌 이미지는 업로드에 실패합니다.
* 이미지 크기는 127GB 이하여야 합니다.
* VHD 파일에 있어야 합니다. VHDX 파일 [Hyper-V 가상 하드 드라이브]은 현재 지원되지 않습니다.
* VHD는 세대 2 가상 컴퓨터가 아니어야 합니다.
* VHD는 고정 크기이거나 동적으로 확장될 수 있습니다. 고정 크기의 VHD 파일보다 Azure에 업로드하는 시간이 짧으므로 동적으로 확장되는 VHD가 권장됩니다.
* 디스크는 MBR(마스터 부트 레코드) 파티션 스타일을 사용하여 초기화해야 합니다. GPT(GUID 파티션 테이블) 파티션 스타일은 지원되지 않습니다.
* VHD는 Windows Server 2012 R2의 단일 설치를 포함해야 합니다. 여러 볼륨을 포함할 수 있지만 한 볼륨만 Windows의 설치를 포함합니다.
* RDSH(원격 데스크톱 세션 호스트) 역할 및 데스크톱 경험 기능을 설치해야 합니다.
* 원격 데스크톱 연결 브로커 역할은 설치하면 *안 됩니다* .
* EFS(파일 시스템 암호화)를 사용하지 않도록 설정해야 합니다.
* 이미지는 **/oobe /generalize /shutdown** 매개 변수를 사용하여 SYSPREPed가 되어야 합니다. **/mode:vm** 매개 변수는 사용하지 마세요.
* 스냅숏 체인으로부터의 VHD 업로드는 지원되지 않습니다.

**시작하기 전에**

서비스를 만들기 전에 다음을 수행해야 합니다.

* [등록](https://azure.microsoft.com/services/remoteapp/) 합니다.
* RemoteApp 서비스 계정으로 사용할 Active Directory의 사용자 계정을 만듭니다. 이 계정의 권한은 도메인에 컴퓨터를 가입시킬 수 있는 권한만으로 제한합니다. 자세한 내용은 [RemoteApp에 대해 Azure Active Directory 구성](remoteapp-ad.md) 을 참조하세요.
* 온-프레미스 네트워크에 대한 정보 수집: IP 주소 정보 및 VPN 장치 세부 정보입니다.
* [Azure PowerShell](/powershell/azureps-cmdlets-docs) 모듈을 설치합니다.
* 액세스 권한을 부여할 사용자에 대한 정보를 수집합니다. 이 정보는 사용자의 Microsoft 계정 정보나 Active Directory 작업 계정 정보가 될 수 있습니다.

## <a name="create-a-template-image"></a>템플릿 이미지 만들기
다음은 처음부터 새 템플릿 이미지를 만드는 요약된 단계입니다.

1. Windows Server 2012 R2 Update DVD 또는 ISO 이미지를 찾습니다.
2. VHD 파일을 만듭니다.
3. Windows Server 2012 R2를 설치합니다.
4. RDSH(원격 데스크톱 세션 호스트) 역할 및 데스크톱 경험 기능을 설치합니다.
5. 응용 프로그램에 필요한 추가 기능을 설치합니다.
6. 응용 프로그램을 설치하고 구성합니다. 앱을 더 쉽게 공유하려면 **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs에 있는 이미지의 **시작** 메뉴에 공유할 앱이나 프로그램을 추가합니다.
7. 응용 프로그램에 필요한 추가 Windows 구성을 수행합니다.
8. EFS(파일 시스템 암호화)를 사용하지 않도록 설정합니다.
9. **필수:** Windows Update로 이동하여 모든 중요 업데이트를 설치합니다.
10. 이미지에 sysprep을 실행합니다.

새 이미지를 만드는 자세한 단계는 다음과 같습니다.

1. Windows Server 2012 R2 Update DVD 또는 ISO 이미지를 찾습니다.
2. 디스크 관리를 사용하여 VHD 파일을 만듭니다.
   
   1. 디스크 관리(diskmgmt.msc)를 실행합니다.
   2. 40GB 크기의 동적으로 확장되는 VHD를 만듭니다. Windows, 응용 프로그램 및 사용자 지정에 필요한 공간의 양을 예상합니다. RDSH 역할 및 데스크톱 경험 기능이 설치된 Windows Server를 사용하려면 약 10GB의 공간이 필요합니다.
      
      1. **작업 > VHD 만들기**를 클릭합니다.
      2. 위치, 크기 및 VHD 형식을 지정합니다. **동적 확장**을 선택한 다음 **확인**을 클릭합니다.
      
      그러면 몇 초 정도 실행됩니다. VHD 만들기가 완료되면 디스크 관리 콘솔에 "초기화 안 됨" 상태의 드라이브 문자가 없는 새로운 디스크가 표시됩니다.
      
      * 디스크(할당되지 않은 공간이 아닌)를 마우스 오른쪽 단추로 클릭한 다음 **디스크 초기화**를 클릭합니다. 파티션 스타일로 **MBR**(마스터 부트 레코드)을 선택한 다음 **확인**을 클릭합니다.
      * 새 볼륨 만들기: 할당되지 않은 공간을 마우스 오른쪽 단추로 클릭한 다음 **새 단순 볼륨**을 클릭합니다. 마법사의 기본값을 그대로 사용하지만 템플릿 이미지를 업로드할 때 잠재적인 문제를 피할 수 있도록 드라이브 문자를 할당해야 합니다.
      * 디스크를 마우스 오른쪽 단추로 클릭한 다음 **VHD 분리**를 클릭합니다.
3. Windows Server 2012 R2 설치:
   
   1. 새 가상 컴퓨터를 만듭니다. Hyper-V 관리자 또는 클라이언트 Hyper-V에서 새 가상 컴퓨터 마법사를 사용합니다.
      1. [세대 지정] 페이지에서 **1세대**를 선택합니다.
      2. 가상 하드 디스크 연결 페이지에서 **기존 가상 하드 디스크 사용**을 선택하고 이전 단계에서 만든 VHD로 이동합니다.
      3. 설치 옵션 페이지에서 **부팅 CD/DVD-ROM에서 운영 체제 설치**를 선택한 다음 Windows Server 2012 R2 설치 미디어의 위치를 선택합니다.
      4. 마법사에서 Windows 및 응용 프로그램을 설치하는 데 필요한 다른 옵션을 선택합니다. 마법사를 마칩니다.
   2. 마법사를 마친 후 가상 컴퓨터의 설정을 편집하고 Windows 및 프로그램을 설치하는 데 필요한 기타 항목(예: 가상 프로세서의 수)을 변경한 다음 **확인**을 클릭합니다.
   3. 가상 컴퓨터에 연결하여 Windows Server 2012 R2를 설치합니다.
4. 다음과 같이 RDSH(원격 데스크톱 세션 호스트) 역할 및 데스크톱 경험 기능을 설치합니다.
   1. 서버 관리자를 실행합니다.
   2. 시작 화면 또는 **관리** 메뉴에서 **역할 및 기능 추가**를 클릭합니다.
   3. 시작하기 전에 페이지에서 **다음** 을 클릭합니다.
   4. **역할 기반 또는 기능 기반 설치**를 선택하고 **다음**을 클릭합니다.
   5. 목록에서 로컬 컴퓨터를 선택하고 **다음**을 클릭합니다.
   6. **원격 데스크톱 서비스**를 선택하고 **다음**을 클릭합니다.
   7. **사용자 인터페이스 및 인프라**를 확장하고 **데스크톱 환경**을 선택합니다.
   8. **기능 추가**를 클릭하고 **다음**을 클릭합니다.
   9. 원격 데스크톱 서비스 페이지에서 **다음**을 클릭합니다.
   10. **원격 데스크톱 세션 호스트**를 클릭합니다.
   11. **기능 추가**를 클릭하고 **다음**을 클릭합니다.
   12. [설치 선택 확인] 페이지에서 **필요한 경우 자동으로 대상 서버 다시 시작**을 선택한 다음, 다시 시작 경고에서 **예**를 클릭합니다.
   13. **Install**을 클릭합니다. 컴퓨터가 다시 시작됩니다.
5. .NET Framework 3.5와 같은 응용 프로그램에 필요한 추가 기능을 설치합니다. 이 기능을 설치하려면 역할 및 기능 추가 마법사를 실행합니다.
6. RemoteApp을 통해 게시할 프로그램 및 응용 프로그램을 설치하고 구성합니다.

> [!IMPORTANT]
> 응용 프로그램을 설치하기 전에 RDSH 역할을 설치하여 RemoteApp에 이미지를 업로드하기 전에 응용 프로그램 호환성 문제가 검색되는지 확인합니다.
> 
> 응용 프로그램(**.lnk** 파일)에 대한 바로 가기가 모든 사용자의 **시작** 메뉴(%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs)에 표시되는지 확인합니다. 또한 **시작** 메뉴에 표시되는 아이콘이 사용자에게 표시하려는 아이콘인지 확인합니다. 그렇지 않으면 변경합니다. 응용 프로그램을 시작 메뉴에 추가하지 *않아도 되지만* 추가할 경우 훨씬 쉽게 RemoteApp에 응용 프로그램을 게시할 수 있습니다. 그렇지 않으면 앱을 게시할 때 응용 프로그램의 설치 경로를 제공해야 합니다.
> 
> 

1. 응용 프로그램에 필요한 추가 Windows 구성을 수행합니다.
2. EFS(파일 시스템 암호화)를 사용하지 않도록 설정합니다. 관리자 권한 명령 창에서 다음 명령을 실행합니다.
   
     Fsutil behavior set disableencryption 1
   
   또는 레지스트리에서 다음 DWORD 값을 설정하거나 추가할 수 있습니다.
   
     HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
3. Azure 가상 컴퓨터 내에 이미지를 빌드하는 경우 **\%windir%\Panther\Unattend.xml** 파일의 이름을 바꿉니다. 이름을 그대로 사용하면 나중에 사용하는 업로드 스크립트가 작동하지 않게 됩니다. 배포를 원래대로 되돌려야 하는 경우 파일을 계속 사용할 수 있도록 파일의 이름을 Unattend.old로 변경합니다.
4. Windows Update로 이동하여 모든 중요 업데이트를 설치합니다. 모든 업데이트를 가져오려면 여러 번 Windows Update를 실행해야 할 수 있습니다. (경우에 따라 업데이트를 설치하며 자체 해당 업데이트는 업데이트 해야합니다.)
5. 이미지에 sysprep을 실행합니다. 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.
   
   **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**
   
   **참고:** 가상 컴퓨터이더라도 SYSPREP 명령의 **/mode: vm** 스위치는 사용하지 마세요.

## <a name="next-steps"></a>다음 단계
사용자 지정 템플릿 이미지를 만들었으니 이제 RemoteApp 컬렉션에 해당 이미지를 업로드해야 합니다. 다음 문서의 정보를 사용하여 컬렉션을 만듭니다.

* [RemoteApp의 하이브리드 컬렉션을 만드는 방법](remoteapp-create-hybrid-deployment.md)
* [RemoteApp의 클라우드 컬렉션을 만드는 방법](remoteapp-create-cloud-deployment.md)


