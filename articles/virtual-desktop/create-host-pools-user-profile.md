---
title: 윈도우 가상 데스크톱 FSLogix 프로필 컨테이너 공유 - Azure
description: 가상 시스템 기반 파일 공유를 사용하여 Windows 가상 데스크톱 호스트 풀에 대한 FSLogix 프로필 컨테이너를 설정하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250921"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기

Windows 가상 데스크톱 서비스는 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공합니다. 이후 버전의 Windows 가상 데스크톱에서는 더 이상 사용되지 않는 UPD(사용자 프로필 디스크) 솔루션을 사용하지 않는 것이 좋습니다.

이 문서에서는 가상 시스템 기반 파일 공유를 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 공유를 설정하는 방법을 설명합니다. 자세한 FSLogix 설명서는 [FSLogix 사이트를](https://docs.fslogix.com/)참조하십시오.

>[!NOTE]
>Azure의 다른 FSLogix 프로필 컨테이너 저장소 옵션에 대한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대한 저장소 옵션을](store-fslogix-profile.md)참조하십시오.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>파일 공유 역할을 하는 새 가상 컴퓨터 만들기

가상 컴퓨터를 만들 때는 호스트 풀 가상 컴퓨터와 동일한 가상 네트워크 또는 호스트 풀 가상 시스템에 대한 연결이 있는 가상 네트워크에 가상 컴퓨터를 배치해야 합니다. 다음과 같은 여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리되는 이미지에서 가상 시스템 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 컴퓨터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

가상 컴퓨터를 만든 후 다음 작업을 수행하여 도메인에 조인합니다.

1. [가상 컴퓨터를](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 만들 때 제공한 자격 증명을 사용하여 가상 컴퓨터에 연결합니다.
2. 가상 컴퓨터에서 **제어판을** 실행하고 **시스템을**선택합니다.
3. **컴퓨터 이름을**선택하고 **설정 변경을**선택한 다음 **변경을 선택합니다...**
4. **도메인을** 선택한 다음 가상 네트워크에서 Active Directory 도메인을 입력합니다.
5. 도메인 가입 컴퓨터에 권한이 있는 도메인 계정으로 인증합니다.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>사용자 프로필에 대한 파일 공유 역할을 할 가상 시스템 준비

다음은 사용자 프로필에 대한 파일 공유 역할을 하는 가상 컴퓨터를 준비하는 방법에 대한 일반적인 지침입니다.

1. Windows 가상 데스크톱 Active Directory 사용자를 [Active Directory 보안 그룹에](/windows/security/identity-protection/access-control/active-directory-security-groups/)추가합니다. 이 보안 그룹은 Windows 가상 데스크톱 사용자가 방금 만든 파일 공유 가상 컴퓨터를 인증하는 데 사용됩니다.
2. [파일 공유 가상 컴퓨터에 연결합니다.](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)
3. 파일 공유 가상 컴퓨터에서 프로필 공유로 사용할 **C 드라이브에** 폴더를 만듭니다.
4. 새 폴더를 마우스 오른쪽 단추로 클릭하고 **속성을**선택하고 **공유를**선택한 다음 **고급 공유를 선택합니다.**
5. **이 폴더 공유를**선택하고 사용 권한을 선택한 다음 **추가를** **선택합니다.**
6. Windows 가상 데스크톱 사용자를 추가한 보안 그룹을 검색한 다음 해당 그룹에 **전체 제어가**있는지 확인합니다.
7. 보안 그룹을 추가한 후 폴더를 마우스 오른쪽 단추로 클릭하고 **속성,** **공유를**선택한 다음 나중에 사용할 **네트워크 경로를** 복사합니다.

사용 권한에 대한 자세한 내용은 [FSLogix 문서를](/fslogix/fslogix-storage-config-ht/)참조하십시오.

## <a name="configure-the-fslogix-profile-container"></a>FSLogix 프로필 컨테이너 구성

FSLogix 소프트웨어로 가상 컴퓨터를 구성하려면 호스트 풀에 등록된 각 컴퓨터에서 다음을 수행합니다.

1. [가상 컴퓨터를](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) 만들 때 제공한 자격 증명을 사용하여 가상 컴퓨터에 연결합니다.
2. 인터넷 브라우저를 실행하고 [이 링크로](https://go.microsoft.com/fwlink/?linkid=2084562) 이동하여 FSLogix 에이전트를 다운로드합니다.
3. .zip \\ \\파일에서\\Win32 \\ \\릴리스\\또는 X64 릴리스로 이동하여 **FSLogixAppsSetup을** 실행하여 FSLogix 에이전트를 설치합니다.  FSLogix를 설치하는 방법에 대한 자세한 내용은 [다운로드 및 FSLogix 설치를](/fslogix/install-ht/)참조하십시오.
4. 프로그램 **파일** > **FSLogix** > **앱으로** 이동하여 에이전트가 설치된지 확인합니다.
5. 시작 메뉴에서 **RegEdit를** 관리자로 실행합니다. **컴퓨터\\HKEY_LOCAL_MACHINE\\소프트웨어\\FSLogix로**이동합니다.
6. **프로필이라는 키를**만듭니다.
7. 프로필 키에 대해 다음 값을 만듭니다.

| 이름                | Type               | 데이터/값                        |
|---------------------|--------------------|-----------------------------------|
| 사용             | DWORD              | 1                                 |
| VHD 위치        | 다중 문자열 값 | "파일 공유를 위한 네트워크 경로"     |

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.
