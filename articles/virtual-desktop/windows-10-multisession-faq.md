---
title: Windows 10 Enterprise 다중 세션 FAQ-Azure
description: Windows 가상 데스크톱에 대 한 Windows 10 Enterprise 다중 세션 사용에 대 한 질문과 대답 및 모범 사례입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: adc0d63a51535b27b42abbe0115ff25594eb2b4f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292473"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise 다중 세션 FAQ

이 문서에서는 자주 묻는 질문과 대답을 소개 하 고 Windows 10 Enterprise 다중 세션의 모범 사례에 대해 설명 합니다.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise 다중 세션 이란?

이전에는 windows 10 enterprise 다중 세션 (예를 들어, EVD (가상 데스크톱)의 경우 Windows 10 Enterprise)은 여러 동시 대화형 세션을 허용 하는 새로운 원격 데스크톱 세션 호스트입니다. 이전에는 Windows Server 에서만이 작업을 수행할 수 있었습니다. 이 기능은 사용자에 게 친숙 한 Windows 10 환경을 제공 하며,이를 통해 다중 세션의 비용 이점을 활용 하 고 RDS Cal (클라이언트 액세스 라이선스) 대신 기존 사용자별 Windows 라이선스를 사용할 수 있습니다. 라이선스 및 가격 책정에 대 한 자세한 내용은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요. 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise 다중 세션에서 동시에 대화형 세션을 사용할 수 있는 사용자는 몇 개입니까?

동시에 활성화 될 수 있는 대화형 세션 수는 시스템의 하드웨어 리소스 (vCPU, 메모리, 디스크 및 vGPU), 사용자가 세션에 로그인 하는 동안 앱을 사용 하는 방법 및 시스템의 작업의 수에 따라 달라 집니다. Windows 10 Enterprise 다중 세션에서 수행할 수 있는 사용자 수를 파악 하기 위해 시스템의 성능에 대 한 유효성을 검사 하는 것이 좋습니다. 자세히 알아보려면 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요. 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>응용 프로그램이 Windows 10 Enterprise 다중 세션을 서버 운영 체제로 보고 하는 이유는 무엇 인가요?

Windows 10 Enterprise 다중 세션은 Windows 10 Enterprise의 가상 버전입니다. 차이점 중 하나는이 운영 체제 (OS)가 Windows Server와 동일한 값인 3 값을 갖는 [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) 를 보고 한다는 것입니다. 이 속성은 운영 체제를 기존 RDSH 관리 도구, RDSH 다중 세션 인식 응용 프로그램 및 대부분의 RDSH 환경에 대 한 낮은 수준의 시스템 성능 최적화와 호환 되도록 유지 합니다. 일부 응용 프로그램 설치 관리자는 ProductType가 Client로 설정 되었는지 여부에 따라 Windows 10 다중 세션의 설치를 차단할 수 있습니다. 앱이 설치 되지 않는 경우 응용 프로그램 공급 업체에 업데이트 된 버전을 문의 하세요. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Windows 10 Enterprise 다중 세션 온-프레미스를 실행할 수 있나요?

Windows 10 Enterprise 다중 세션은 Azure 용 Windows 가상 데스크톱 서비스에 최적화 되어 있으므로 온-프레미스 프로덕션 환경에서 실행할 수 없습니다. 프로덕션 목적으로 Azure 외부에서 Windows 10 Enterprise 다중 세션을 실행 하는 것은 사용권 계약에 대 한 것입니다. Windows 10 Enterprise 다중 세션은 온-프레미스 KMS (키 관리 서비스)에 대해 활성화 되지 않습니다.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>내 조직에 대 한 Windows 10 Enterprise 다중 세션 이미지를 사용자 지정 어떻게 할까요?

Windows 10 Windows 10 Enterprise 다중 세션을 사용 하 여 Azure에서 VM (가상 머신)을 시작 하 고 LOB 응용 프로그램, sysprep/일반화를 설치 하 여 사용자 지정 하 고 Azure Portal를 사용 하 여 이미지를 만들 수 있습니다.  
 
시작 하려면 Windows 10 Enterprise 다중 세션을 사용 하 여 Azure에서 VM을 만듭니다. Azure에서 VM을 시작 하는 대신 VHD를 직접 다운로드할 수 있습니다. 그런 다음 다운로드 한 VHD를 사용 하 여 Hyper-v를 사용 하는 Windows 10 PC에서 새 1 세대 VM을 만들 수 있습니다.

LOB 응용 프로그램을 설치 하 고 이미지를 sysprep 하 여 필요에 맞게 이미지를 사용자 지정 합니다. 사용자 지정이 완료 되 면 내에서 VHD를 사용 하 여 Azure에 이미지를 업로드 합니다. 그런 다음 Azure Marketplace에서 Windows 가상 데스크톱을 가져오고이를 사용 하 여 사용자 지정 이미지를 사용 하 여 새 호스트 풀을 배포 합니다.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>배포 후 Windows 10 Enterprise 다중 세션을 관리 어떻게 할까요?

모든 지원 되는 구성 도구를 사용할 수 있지만 Windows 10 Enterprise 다중 세션을 지원 하기 때문에 버전 1906 Configuration Manager 하는 것이 좋습니다. 현재 Microsoft Intune 지원에 대해 작업 하 고 있습니다.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 Enterprise 다중 세션이 AD (Azure Active Directory)에 조인 될 수 있나요?

Windows 10 Enterprise 다중 세션은 현재 하이브리드 Azure AD 조인으로 지원 됩니다. Windows 10 Enterprise 다중 세션이 도메인에 가입 된 후 기존 그룹 정책 개체를 사용 하 여 Azure AD 등록을 사용 하도록 설정 합니다. 자세한 내용은 [하이브리드 Azure Active Directory 조인 구현 계획](../active-directory/devices/hybrid-azuread-join-plan.md)을 참조 하세요.
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise 다중 세션 이미지는 어디서 찾을 수 있나요?

Windows 10 Enterprise 다중 세션은 Azure 갤러리에 있습니다. 이를 찾으려면 Azure Portal으로 이동 하 여 가상 데스크톱 릴리스에 대 한 Windows 10 Enterprise를 검색 합니다. Enterprise 용 Microsoft 365 Apps와 통합 된 이미지의 경우 Azure Portal로 이동 하 여 **Microsoft Windows 10 + Microsoft 365 Apps for enterprise**를 검색 합니다.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>어떤 Windows 10 Enterprise 다중 세션 이미지를 사용 해야 하나요?

Azure 갤러리에는 Windows 10 Enterprise 다중 세션 버전 1809 및 Windows 10 Enterprise 다중 세션 버전 1903을 비롯 한 여러 릴리스가 있습니다. 성능 및 안정성 향상을 위해 최신 버전을 사용 하는 것이 좋습니다.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>지원 되는 Windows 10 Enterprise 다중 세션 버전은 무엇 인가요?

Windows 10 Enterprise 다중 세션 버전 1809 이상이 지원 되며 Azure 갤러리에서 사용할 수 있습니다. 이러한 릴리스는 Windows 10 Enterprise와 동일한 지원 수명 주기 정책을 따릅니다. 즉, 3 월 릴리스는 18 개월 동안 지원 되 고 9 월 릴리스는 30 개월 동안 지원 됩니다.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise 다중 세션에 사용 해야 하는 프로필 관리 솔루션은 무엇 인가요?

Windows 10 Enterprise를 비영구 환경 또는 중앙에 저장 된 프로필이 필요한 다른 시나리오로 구성할 때 FSLogix 프로필 컨테이너를 사용 하는 것이 좋습니다. FSLogix를 사용 하면 모든 사용자 세션에 대해 사용자 프로필을 사용할 수 있고 최신 상태로 유지할 수 있습니다. 또한 FSLogix 프로필 컨테이너를 사용 하 여 적절 한 권한이 있는 SMB 공유에 사용자 프로필을 저장 하는 것이 좋지만, 필요한 경우 Azure 페이지 blob 저장소에 사용자 프로필을 저장할 수 있습니다. Windows 가상 데스크톱 사용자는 추가 비용 없이 FSLogix를 사용할 수 있습니다.
 
FSLogix 프로필 컨테이너를 구성 하는 방법에 대 한 자세한 내용은 [FSLogix 프로필 컨테이너 구성](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)을 참조 하세요.  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Windows 10 Enterprise 다중 세션에 액세스 하는 데 필요한 라이선스는 무엇입니까?

해당 라이선스의 전체 목록은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>로그 아웃 한 후에도 앱이 사라지는 이유는 무엇 인가요?

이는 FSLogix와 같은 프로필 관리 솔루션에 Windows 10 Enterprise 다중 세션을 사용 하 고 있기 때문에 발생 합니다. 관리자 또는 프로필 솔루션은 사용자가 로그 아웃할 때 사용자 프로필을 삭제 하도록 시스템을 구성 했습니다. 이 구성은 로그 아웃 한 후 시스템이 사용자 프로필을 삭제 하는 경우 세션 중에 설치한 앱도 모두 제거 함을 의미 합니다. 설치한 앱을 유지 하려면 Windows 가상 데스크톱 환경에서 모든 사용자에 대해 이러한 앱을 프로 비전 하도록 관리자에 게 요청 해야 합니다.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>사용자가 로그 아웃할 때 앱이 사라지지 않도록 어떻게 할까요?

사용자가 프로필에 추가 앱을 설치 하는 것을 방지 하기 위해 기본적으로 가장 가상화 된 환경이 구성 됩니다. 사용자가 Windows 가상 데스크톱에서 로그 아웃할 때 앱이 사라지지 않도록 하려면 사용자 환경에서 모든 사용자 프로필에 대해 해당 앱을 프로 비전 해야 합니다. 앱을 프로 비전 하는 방법에 대 한 자세한 내용은 다음 리소스를 확인 하세요.

- [Windows 가상 데스크톱에서 기본 제공 앱 게시](publish-apps.md)
- [DISM 앱 패키지 서비스 명령줄 옵션](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>사용자가 Microsoft Store에서 앱을 다운로드 하 여 설치 하지 않도록 어떻게 할까요?

Microsoft Store 앱을 사용 하지 않도록 설정 하 여 사용자가 이미 프로 비전 한 앱 이외에 추가 앱을 다운로드 하지 않도록 할 수 있습니다.

스토어 앱을 사용 하지 않도록 설정 하려면:

1. 새 그룹 정책을 만듭니다.
2. **컴퓨터 구성**  >  **관리 템플릿**  >  **Windows 구성 요소**를 선택 합니다.
3. **Store**를 선택합니다.
4. **응용 프로그램 스토어**를 선택 합니다.
5. **사용 안 함**을 선택한 다음, **확인**을 선택 합니다.
6. **적용**을 선택합니다.
 
## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱 및 Windows 10 Enterprise 다중 세션에 대 한 자세한 내용은 다음을 확인 하세요.

- [Windows 가상 데스크톱 설명서](overview.md) 를 참조 하십시오.
- [Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 을 방문 하세요.
- Windows [가상 데스크톱 자습서](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) 를 사용 하 여 Windows 가상 데스크톱 배포 설정
