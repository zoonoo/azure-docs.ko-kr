---
title: 윈도우 10 엔터프라이즈 다중 세션 자주 묻는 질문 - Azure
description: Windows 가상 데스크톱용 Windows 10 엔터프라이즈 멀티 세션 사용에 대한 자주 묻는 질문과 모범 사례입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637091"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Enterprise 다중 세션 FAQ

이 문서에서는 자주 묻는 질문에 답하고 Windows 10 엔터프라이즈 다중 세션에 대한 모범 사례에 대해 설명합니다.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Windows 10 엔터프라이즈 멀티 세션이란 무엇입니까?

이전에는 가상 데스크톱용 엔터프라이즈(EVD)로 알려진 Windows 10 엔터프라이즈 멀티 세션은 여러 개의 동시 대화형 세션을 허용하는 새로운 원격 데스크톱 세션 호스트입니다. 이전에는 Windows 서버만 이 작업을 수행할 수 있었습니다. 이 기능을 통해 사용자에게 친숙한 Windows 10 환경을 제공하는 동시에 IT 는 다중 세션의 비용 이점을 활용하고 RDS 클라이언트 액세스 라이선스(CAL) 대신 기존 사용자별 Windows 라이선스를 사용할 수 있습니다. 라이선스 및 가격 책정에 대한 자세한 내용은 [Windows 가상 데스크톱 가격](https://azure.microsoft.com/pricing/details/virtual-desktop/)책정을 참조하십시오. 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Windows 10 엔터프라이즈 멀티 세션에서 대화형 세션을 동시에 사용할 수 있는 사용자는 몇 명입니까?

동시에 활성화할 수 있는 대화형 세션 수(vCPU, 메모리, 디스크 및 vGPU), 사용자가 세션에 로그인한 상태에서 앱을 사용하는 방법, 시스템 워크로드의 사용량이 얼마나 많은지에 따라 사용할 수 있습니다. Windows 10 엔터프라이즈 다중 세션에서 사용할 수 있는 사용자 수를 파악하려면 시스템 성능의 유효성을 검사하는 것이 좋습니다. 자세한 내용은 [Windows 가상 데스크톱 가격 책정을](https://azure.microsoft.com/pricing/details/virtual-desktop/)참조하십시오. 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>응용 프로그램이 Windows 10 엔터프라이즈 다중 세션을 서버 운영 체제로 보고하는 이유는 무엇입니까?

윈도우 10 엔터프라이즈 멀티 세션은 윈도우의 가상 버전 10 기업. 차이점 중 하나는 이 운영 체제(OS)가 [ProductType값이](/windows/win32/cimwin32prov/win32-operatingsystem) Windows 서버와 동일한 값인 3을 갖는 것으로 보고한다는 것입니다. 이 속성은 기존 RDSH 관리 툴링, RDSH 다중 세션 인식 응용 프로그램 및 RDSH 환경에 대한 대부분 낮은 수준의 시스템 성능 최적화와 OS 호환을 유지합니다. 일부 응용 프로그램 설치 관리자는 ProductType이 클라이언트로 설정되어 있는지 여부에 따라 Windows 10 다중 세션에서 설치를 차단할 수 있습니다. 앱이 설치되지 않으면 응용 프로그램 공급업체에 업데이트된 버전을 문의하세요. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Windows 10 엔터프라이즈 다중 세션을 온-프레미스에서 실행할 수 있습니까?

Windows 10 엔터프라이즈 다중 세션은 Azure용 Windows 가상 데스크톱 서비스에 최적화되어 있으므로 온-프레미스 프로덕션 환경에서 실행할 수 없습니다. 프로덕션 을 위해 Azure 외부에서 Windows 10 엔터프라이즈 다중 세션을 실행하는 라이선스 계약에 위배됩니다. Windows 10 엔터프라이즈 다중 세션은 KMS(온-프레미스 키 관리 서비스)에 대해 활성화되지 않습니다.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>조직에 대한 Windows 10 엔터프라이즈 다중 세션 이미지를 사용자 지정하려면 어떻게 해야 합니까?

Windows 10 Windows 10 Enterprise 다중 세션을 사용하여 Azure에서 가상 컴퓨터(VM)를 시작하고 LOB 응용 프로그램을 설치하고 sysprep/generalize를 한 다음 Azure 포털을 사용하여 이미지를 만들어 사용자 지정할 수 있습니다.  
 
시작하려면 Windows 10 엔터프라이즈 다중 세션을 사용하여 Azure에서 VM을 만듭니다. Azure에서 VM을 시작하는 대신 VHD를 직접 다운로드할 수 있습니다. 그 후, 다운로드 한 VHD를 사용 하 여 새로운 세대를 만들 수 있습니다 10 하이퍼-V를 사용 하 여 PC.

LOB 응용 프로그램을 설치하고 이미지를 sysprep하여 필요에 맞게 이미지를 사용자 정의할 수 있습니다. 사용자 지정이 완료되면 내부의 VHD를 사용하여 Azure에 이미지를 업로드합니다. 그런 다음 Azure 마켓플레이스에서 Windows 가상 데스크톱을 얻고 이를 사용하여 사용자 지정된 이미지가 있는 새 호스트 풀을 배포합니다.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>배포 후 Windows 10 엔터프라이즈 멀티 세션을 관리하려면 어떻게 해야 합니까?

지원되는 구성 도구를 사용할 수 있지만 Windows 10 엔터프라이즈 다중 세션을 지원하므로 Configuration Manager 버전 1906을 사용하는 것이 좋습니다. 현재 Microsoft Intune 지원에 대해 작업 중입니다.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 엔터프라이즈 다중 세션이 AD(Azure Active Directory)가 조인될 수 있습니까?

Windows 10 엔터프라이즈 다중 세션은 현재 하이브리드 Azure AD 조인으로 지원됩니다. Windows 10 엔터프라이즈 다중 세션이 도메인에 가입된 후 기존 그룹 정책 개체를 사용하여 Azure AD 등록을 활성화합니다. 자세한 내용은 [하이브리드 Azure Active Directory 조인 구현 계획을](../active-directory/devices/hybrid-azuread-join-plan.md)참조하십시오.
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Windows 10 엔터프라이즈 다중 세션 이미지는 어디에서 찾을 수 있습니까?

Windows 10 엔터프라이즈 다중 세션이 Azure 갤러리에 있습니다. 이를 찾으려면 Azure 포털로 이동하여 가상 데스크톱용 Windows 10 엔터프라이즈 릴리스를 검색합니다. 오피스 프로 플러스와 통합 된 이미지에 대 한, Azure 포털로 이동 하 고 마이크로소프트 윈도 에 대 한 검색 10 + 사무실 365 프로 플러스.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>어떤 Windows 10 엔터프라이즈 다중 세션 이미지를 사용해야 합니까?

Azure 갤러리에는 Windows 10 엔터프라이즈 다중 세션, 버전 1809 및 Windows 10 엔터프라이즈 다중 세션 버전 1903을 비롯한 여러 릴리스가 있습니다. 성능과 안정성을 높이기 위해 최신 버전을 사용하는 것이 좋습니다.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>어떤 Windows 10 엔터프라이즈 다중 세션 버전이 지원되나요?

Windows 10 엔터프라이즈 다중 세션, 버전 1809 이상지원되며 Azure 갤러리에서 사용할 수 있습니다. 이러한 릴리스는 Windows 10 Enterprise와 동일한 지원 수명 주기 정책을 따르며, 이는 스프링 릴리스가 18개월 동안 지원되고 가을 릴리스가 30개월 동안 지원된다는 것을 의미합니다.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Windows 10 엔터프라이즈 다중 세션에 사용해야 하는 프로필 관리 솔루션은 무엇입니까?

비영구 환경 이나 중앙에 저장 된 프로필이 필요한 다른 시나리오에서 Windows 10 Enterprise를 구성할 때 FSLogix 프로필 컨테이너를 사용 하는 것이 좋습니다. FSLogix는 모든 사용자 세션에 대해 사용자 프로필을 사용할 수 있고 최신 상태로 유지할 수 있도록 합니다. 또한 FSLogix 프로필 컨테이너를 사용하여 적절한 사용 권한이 있는 SMB 공유에 사용자 프로필을 저장하는 것이 좋지만 필요한 경우 Azure 페이지 Blob 저장소에 사용자 프로필을 저장할 수 있습니다. Windows 가상 데스크톱 사용자는 추가 비용 없이 FSLogix를 사용할 수 있습니다.
 
FSLogix 프로필 컨테이너를 구성하는 방법에 대한 자세한 내용은 [FSLogix 프로필 컨테이너 구성을](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)참조하십시오.  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Windows 10 엔터프라이즈 다중 세션에 액세스하려면 어떤 라이선스가 필요합니까?

해당 라이선스의 전체 목록은 [Windows 가상 데스크톱 가격 책정을](https://azure.microsoft.com/pricing/details/virtual-desktop/)참조하십시오.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>로그아웃한 후 앱이 사라지는 이유는 무엇입니까?

이는 FSLogix와 같은 프로필 관리 솔루션이 있는 Windows 10 엔터프라이즈 멀티 세션을 사용하기 때문에 발생합니다. 관리자 또는 프로필 솔루션은 사용자가 로그아웃할 때 사용자 프로필을 삭제하도록 시스템을 구성했습니다. 이 구성은 로그아웃한 후 시스템에서 사용자 프로필을 삭제하면 세션 중에 설치한 앱도 제거됩니다. 설치한 앱을 유지하려면 관리자에게 Windows 가상 데스크톱 환경의 모든 사용자에 대해 이러한 앱을 프로비전하도록 요청해야 합니다.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>사용자가 로그아웃할 때 앱이 사라지지 않도록 하려면 어떻게 해야 하나요?

대부분의 가상화 환경은 사용자가 프로필에 추가 앱을 설치하지 못하도록 기본적으로 구성됩니다. 사용자가 Windows Virtual Desktop에서 로그아웃할 때 앱이 사라지지 않도록 하려면 환경의 모든 사용자 프로필에 해당 앱을 프로비전해야 합니다. 앱 프로비저닝에 대한 자세한 내용은 다음 리소스를 확인하십시오.

- [Windows 가상 데스크톱에 기본 제공 앱 게시](publish-apps.md)
- [명령줄 옵션을 서비스하는 DISM 앱 패키지](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [추가 앱프로비저닝된 패키지](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>사용자가 Microsoft Store에서 앱을 다운로드하고 설치하지 않도록 하려면 어떻게 해야 합니까?

Microsoft Store 앱을 사용하지 않도록 설정하여 사용자가 이미 프로비전한 앱 이외의 추가 앱을 다운로드하지 않도록 할 수 있습니다.

스토어 앱을 사용하지 않도록 설정하려면 다음 을 수행합니다.

1. 새 그룹 정책을 만듭니다.
2. **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소**선택.
3. **Store**를 선택합니다.
4. **저장소 응용 프로그램을**선택합니다.
5. **비활성화를**선택한 다음 **확인을**선택합니다.
6. **적용**을 선택합니다.
 
## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱 및 Windows 10 엔터프라이즈 다중 세션에 대해 자세히 알아보려면 다음을 수행하십시오.

- Windows [가상 데스크톱 미리 보기 설명서](overview.md) 읽기
- 우리의 [윈도우 가상 데스크톱 기술 커뮤니티를](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 방문
- Windows 가상 데스크톱 [자습서를](tenant-setup-azure-active-directory.md) 사용하여 Windows 가상 데스크톱 배포 설정
