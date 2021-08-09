---
title: Azure Virtual Desktop MSIX 앱 연결 FAQ - Azure
description: Azure Virtual Desktop용 MSIX 앱 연결에 대한 질문과 대답입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cf9770fd58a7cbd75d4647284d3ee84042c7e187
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754622"
---
# <a name="msix-app-attach-faq"></a>MSIX 앱 연결 FAQ

이 문서에서는 Azure Virtual Desktop용 MSIX 앱 연결에 대한 자주 묻는 질문에 답변합니다.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX와 MSIX 앱 연결의 차이점은 무엇인가요?

MSIX는 앱의 패키징 형식이지만 MSIX 앱 연결은 배포에 MSIX 패키지를 제공하는 기능입니다.

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 앱 연결에서 FSLogix를 사용하나요?

MSIX 앱 연결은 FSLogix를 사용하지 않습니다. 그러나 MSIX 앱 연결 및 FSLogix는 원활한 사용자 환경을 제공하기 위해 함께 작동하도록 설계되었습니다.

## <a name="can-i-use-the-msix-app-attach-outside-of-azure-virtual-desktop"></a>Azure Virtual Desktop 외부에서 MSIX 앱 연결을 사용할 수 있나요?

MSIX 앱 연결을 지원하는 API는 Windows 10 Enterprise에서 사용할 수 있습니다. 이러한 API는 Azure Virtual Desktop 외부에서 사용할 수 있습니다. 그러나 Azure Virtual Desktop 외부에서 MSIX 앱 연결에 대한 관리 평면은 없습니다.

## <a name="how-do-i-get-an-msix-package"></a>MSIX 패키지를 어떻게 가져오나요?

소프트웨어 공급업체에서 MSIX 패키지를 제공합니다. MSIX가 아닌 패키지를 MSIX로 변환할 수도 있습니다. [기존 설치 관리자를 MSIX로 이동하는 방법](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)에서 자세히 알아봅니다.

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX 앱 연결을 지원하는 운영 체제는 무엇인가요?

Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션, 버전 2004 이상

## <a name="is-msix-app-attach-currently-generally-available"></a>MSIX 앱 연결은 현재 일반 공급되나요?

MSIX 앱 연결은 Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션, 버전 2004 이상의 일부입니다. 두 운영 체제 모두 현재 일반 공급됩니다. 

## <a name="can-i-use-msix-app-attach-outside-of-azure-virtual-desktop"></a>Azure Virtual Desktop 외부에서 MSIX 앱 연결을 사용할 수 있나요?

MSIX 및 MSIX 앱 연결 API는 Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션, 버전 2004 이상의 일부입니다. 현재는 Azure Virtual Desktop 외부에서 MSIX 앱 연결용 관리 소프트웨어를 제공하지 않습니다.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>동일한 애플리케이션의 두 버전을 동시에 실행할 수 있나요?

두 버전의 동일한 MSIX 애플리케이션을 동시에 실행하려면 appxmanifest.xml 파일에 정의된 MSIX 패키지 제품군이 앱마다 달라야 합니다.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>MSIX 앱 연결을 사용할 때 자동 업데이트를 사용하지 않도록 설정해야 합니까?

예. MSIX 앱 연결은 MSIX 애플리케이션에 대한 자동 업데이트를 지원하지 않습니다.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>MSIX 앱 연결에서 사용 권한은 어떻게 작동하나요?

MSIX 앱 연결을 사용하는 호스트 풀의 모든 VM(가상 머신)에는 MSIX 이미지가 저장된 파일 공유에 대한 읽기 권한이 있어야 합니다. 또한 Azure Files를 사용하는 경우 RBAC(역할 기반 액세스 제어) 및 NTFS(New Technology File System) 권한을 모두 부여 받아야 합니다.

## <a name="how-many-users-can-use-an-msix-image-handle"></a>MSIX 이미지 핸들을 사용할 수 있는 사용자는 몇 명입니까?

MSIX 앱 연결은 사용자 단위가 아닌 머신별로 MSIX 이미지를 탑재합니다. MSIX 이미지 핸들을 사용할 수 있는 사용자의 양은 머신의 파일 시스템 크기 및 네트워크 처리량에 따라 다릅니다. 또한 Azure Files는 파일당 2,000개의 열린 핸들로 제한됩니다. 

## <a name="can-i-use-azure-active-directory-domain-services-azure-ad-ds-with-msix-app-attach"></a>MSIX 앱 연결에 Azure AD DS(Azure Active Directory Domain Services)를 사용할 수 있나요?

MSIX 앱 연결은 현재 Azure AD DS를 지원하지 않습니다. Azure AD DS 컴퓨터 개체가 Azure AD(Azure Active Directory)와 동기화되지 않으므로 관리자는 Azure Files에 대한 필요한 RBAC(역할 기반 액세스 제어) 권한을 제공할 수 없습니다.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>HTTP 또는 HTTPs에 MSIX 앱 연결을 사용할 수 있나요?

HTTP 또는 HTTPs를 통해 MSIX 앱 연결 사용은 현재 지원되지 않습니다.

## <a name="can-i-restage-the-same-msix-application"></a>동일한 MSIX 애플리케이션을 다시 스테이징할 수 있나요?

예. 이미 스테이징한 애플리케이션을 다시 스테이징할 수 있으며 이로 인해 오류가 발생하지 않아야 합니다.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX 앱 연결은 자체 서명된 인증서를 지원하나요?

예. MSIX 앱 연결이 자체 서명된 애플리케이션을 호스트하는 데 사용되는 모든 세션 호스트 VM에 자체 서명된 인증서를 설치해야 합니다. [패키지 서명용 인증서 만들기](/windows/msix/package/create-certificate-package-signing)에서 자체 서명된 인증서를 만드는 방법을 알아봅니다.

## <a name="what-applications-can-i-repackage-to-msix"></a>MSIX에 다시 패키징할 수 있는 애플리케이션은 무엇인가요?

각 애플리케이션은 OS, 프로그래밍 언어 및 프레임워크의 다양한 기능을 사용합니다. 애플리케이션을 다시 패키징하려면 [기존 설치 관리자를 MSIX로 이동하는 방법](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)의 지침을 따릅니다. 애플리케이션을 다시 패키징하기 위해 필요한 항목 목록은 [데스크톱 애플리케이션 패키징 준비](/windows/msix/desktop/desktop-to-uwp-prepare)에서 찾을 수 있습니다. 

특정 애플리케이션은 애플리케이션 계층화될 수 없습니다. 즉, MSIX 파일로 다시 패키징할 수 없습니다. 다음은 다시 패키징할 수 없는 애플리케이션 목록입니다.

- 드라이버 
- Active-X 또는 Silverlight
- VPN 클라이언트
- 바이러스 백신 프로그램

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 관해 자세히 알아보려면 [개요](what-is-app-attach.md) 및 [용어집](app-attach-glossary.md)을 확인하세요. 그러지 않으면 [앱 연결 설정](app-attach.md)을 시작합니다.
