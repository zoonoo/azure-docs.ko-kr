---
title: Windows 가상 데스크톱 FSLogix 프로필 컨테이너 공유-Azure
description: 가상 컴퓨터 기반 파일 공유를 사용 하 여 Windows 가상 데스크톱 호스트 풀에 대해 FSLogix 프로필 컨테이너를 설정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002578"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기

Windows 가상 데스크톱 서비스는 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공 합니다. UPD (사용자 프로필 디스크) 솔루션을 사용 하지 않는 것이 좋습니다 .이 솔루션은 이후 버전의 Windows 가상 데스크톱에서 더 이상 사용 되지 않습니다.

이 문서에서는 가상 머신 기반 파일 공유를 사용 하 여 호스트 풀에 대해 FSLogix 프로필 컨테이너 공유를 설정 하는 방법을 설명 합니다. 파일 공유 대신 Azure Files를 사용 하는 것이 좋습니다. 추가 FSLogix 설명서는 [fslogix 사이트](https://docs.fslogix.com/)를 참조 하세요.

>[!NOTE]
>Azure의 여러 FSLogix 프로필 컨테이너 저장소 옵션에 대 한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md)을 참조 하세요.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>파일 공유 역할을 하는 새 가상 컴퓨터 만들기

가상 컴퓨터를 만들 때 호스트 풀 가상 컴퓨터와 동일한 가상 네트워크 또는 호스트 풀 가상 컴퓨터에 연결 된 가상 네트워크에 가상 컴퓨터를 넣어야 합니다. 다음과 같은 여러 방법으로 가상 머신을 만들 수 있습니다.

- [Azure Gallery 이미지에서 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리 이미지에서 가상 머신 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

가상 컴퓨터를 만든 후 다음 작업을 수행 하 여 도메인에 가입 합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 가상 머신에서 **제어판**을 시작하고 **시스템**을 선택합니다.
3. **컴퓨터 이름**, **설정 변경**, **변경...** 을 차례로 선택합니다.
4. **도메인**을 선택한 다음, 가상 네트워크에 Active Directory 도메인을 입력합니다.
5. 도메인 조인 머신에 대한 권한이 있는 도메인 계정으로 인증합니다.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>사용자 프로필에 대 한 파일 공유 역할을 하는 가상 컴퓨터 준비

다음은 사용자 프로필의 파일 공유 역할을 하는 가상 컴퓨터를 준비 하는 방법에 대 한 일반적인 지침입니다.

1. Windows 가상 데스크톱 Active Directory 사용자를 [Active Directory 보안 그룹](/windows/security/identity-protection/access-control/active-directory-security-groups/)에 추가 합니다. 이 보안 그룹은 방금 만든 파일 공유 가상 컴퓨터에 대 한 Windows 가상 데스크톱 사용자를 인증 하는 데 사용 됩니다.
2. [파일 공유 가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
3. 파일 공유 가상 컴퓨터에서 프로필 공유로 사용할 **C 드라이브** 에 폴더를 만듭니다.
4. 새 폴더를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택한 다음 **공유**를 선택 하 고 **고급 공유 ...** 를 선택 합니다.
5. **이 폴더 공유**, **권한**선택 ...을 차례로 선택 하 고 **추가**...를 선택 합니다.
6. Windows 가상 데스크톱 사용자를 추가한 보안 그룹을 검색 한 다음 그룹에 **모든 권한이**있는지 확인 합니다.
7. 보안 그룹을 추가한 후 폴더를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택한 다음 **공유**를 선택 하 고 나중에 사용할 **네트워크 경로** 를 복사 합니다.

사용 권한에 대 한 자세한 내용은 [Fslogix 설명서](/fslogix/fslogix-storage-config-ht/)를 참조 하세요.

## <a name="configure-the-fslogix-profile-container"></a>FSLogix 프로필 컨테이너 구성

FSLogix 소프트웨어를 사용 하 여 가상 머신을 구성 하려면 호스트 풀에 등록 된 각 컴퓨터에서 다음을 수행 합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 인터넷 브라우저를 시작 하 고 [이 링크로](https://go.microsoft.com/fwlink/?linkid=2084562) 이동 하 여 FSLogix 에이전트를 다운로드 합니다.
3. \\ \\ \\ .Zip 파일에서 Win32 릴리스 또는 X64 릴리스로 이동 하 \\ \\ \\ 고 **FSLogixAppsSetup** 를 실행 하 여 fslogix 에이전트를 설치 합니다.  FSLogix를 설치 하는 방법에 대해 자세히 알아보려면 [Fslogix 다운로드 및 설치](/fslogix/install-ht/)를 참조 하세요.
4. **프로그램 파일**  >  **fslogix**  >  **앱** 으로 이동 하 여 에이전트를 설치 했는지 확인 합니다.
5. 시작 메뉴에서 관리자 권한으로 **RegEdit** 를 실행 합니다. **컴퓨터 \\ HKEY_LOCAL_MACHINE \\ software \\ fslogix**로 이동 합니다.
6. **프로필**이라는 키를 만듭니다.
7. 프로필 키에 대해 다음 값을 만듭니다.

| Name                | Type               | 데이터/값                        |
|---------------------|--------------------|-----------------------------------|
| 사용             | DWORD              | 1                                 |
| VHDLocations        | 다중 문자열 값 | "파일 공유의 네트워크 경로"     |

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.
