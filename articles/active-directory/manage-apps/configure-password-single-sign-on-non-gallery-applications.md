---
title: Azure AD 앱에 대한 암호 단일 사인온을 구성하는 방법 | 마이크로 소프트 문서
description: Microsoft ID 플랫폼(Azure AD)에서 Azure AD 엔터프라이즈 응용 프로그램에 암호 단일 사인온(SSO)을 구성하는 방법
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063529"
---
# <a name="configure-password-single-sign-on"></a>암호 Single Sign-On 구성

Azure AD Enterprise 응용 프로그램에 [갤러리 앱](add-gallery-app.md) 또는 [비갤러리 웹 앱을](add-non-gallery-app.md) 추가하는 경우 사용할 수 있는 단일 사인온 옵션 중 하나는 암호 기반 [단일 사인온](what-is-single-sign-on.md#password-based-sso)입니다. 이 옵션은 HTML 로그인 페이지가 있는 모든 웹에서 사용할 수 있습니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다. 

암호 기반 SSO는 응용 프로그램을 Azure AD에 빠르게 통합하는 데 좋은 방법이며 다음을 수행할 수 있습니다.

-   Azure AD와 통합한 애플리케이션에 대한 사용자 이름 및 암호를 안전하게 저장 및 재생하여 **사용자에 대한 Single Sign-On** 활성화

-   로그인하는 데 사용자 이름 및 암호 필드 이상을 필요로 하는 애플리케이션에 대해 **여러 로그인 필드를 필요로 하는 애플리케이션 지원**

-   사용자가 자격 증명을 입력하는 경우 [애플리케이션 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)에 표시되는 사용자 이름 및 암호 입력 필드의 **레이블 사용자 지정**

-   **사용자**가 [애플리케이션 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)에 수동으로 입력하는 모든 기존 애플리케이션 계정에 대한 자신의 사용자 이름 및 암호를 제공하도록 허용

-   **비즈니스 그룹의 멤버**가 [셀프 서비스 애플리케이션 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 기능을 사용하여 사용자에게 할당된 사용자 이름 및 암호를 지정하도록 허용

-   **관리자가** 자격 증명 업데이트 기능을 사용하여 응용 프로그램에 로그인할 때 개인 또는 그룹에서 사용할 사용자 이름과 암호를 지정할 수 있도록 허용합니다. 

## <a name="before-you-begin"></a>시작하기 전에

애플리케이션이 Azure AD 테넌트에 추가되지 않은 경우 [갤러리 앱 추가](add-gallery-app.md) 또는 [비갤러리 앱 추가](add-non-gallery-app.md)를 참조하세요.

## <a name="open-the-app-and-select-password-single-sign-on"></a>앱을 열고 암호 단일 사인온을 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

2. Azure **Active Directory** > **엔터프라이즈 응용 프로그램으로 이동합니다.** Azure AD 테넌트에 있는 애플리케이션 샘플이 임의로 표시됩니다. 

3. **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**을 선택한 다음, **적용**을 선택합니다.

4. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

5. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

6. **암호 기반**을 선택합니다.

7. 응용 프로그램의 웹 기반 로그인 페이지의 URL을 입력합니다. 이 문자열은 사용자 이름 입력 필드를 포함하는 페이지여야 합니다.

   ![암호 기반 Single Sign-On](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. **저장**을 선택합니다. Azure AD는 사용자 이름 입력 및 암호 입력에 대한 로그인 페이지를 구문 분석하려고 합니다. 시도가 성공하면 완료된 것입니다. 
 
> [!NOTE]
> 다음 단계는 [응용 프로그램에 사용자 또는 그룹을 할당하는 것입니다.](methods-for-assigning-users-and-groups.md) 사용자 및 그룹을 할당한 후 사용자가 응용 프로그램에 로그인할 때 사용자를 대신하여 사용할 자격 증명을 제공할 수 있습니다. **사용자 및 그룹**선택, 사용자 또는 그룹 행의 확인란을 선택한 다음 자격 증명 업데이트 를 **클릭합니다.** 그런 다음 사용자 또는 그룹을 대신하여 사용할 사용자 이름과 암호를 입력합니다. 그렇지 않으면 사용자가 시작 시 자격 증명을 입력하라는 메시지가 표시됩니다.
 

## <a name="manual-configuration"></a>수동 구성

Azure AD의 구문 분석 시도가 실패하면 수동으로 사인온을 구성할 수 있습니다.

1. **응용 프로그램 이름> 구성에서 응용 프로그램 이름> 암호 단일 사인온 설정 구성을 선택하여 사인온 구성 페이지를 표시합니다. \<** ** \<** **Configure sign-on** 

2. **로그인 필드를 수동으로 검색합니다.** 로그인 필드의 수동 검색을 설명하는 추가 지침이 나타납니다.

   ![암호 기반 단일 사인온의 수동 구성](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **로그인 필드 캡처를**선택합니다. 캡처 상태 페이지가 새 탭에서 열리며 현재 진행 중인 메시지 **메타데이터 캡처가**표시됩니다.

4. 액세스 **패널 확장 필수** 상자가 새 탭에 나타나면 **지금 설치를** 선택하여 **내 앱 보안 로그인 확장** 확장 확장 확장을 설치합니다. (브라우저 확장 은 마이크로 소프트 에지, 크롬, 또는 파이어 폭스가 필요합니다.) 그런 다음 확장을 설치, 실행 및 사용하도록 설정하고 캡처 상태 페이지를 새로 고칩니다.

   그러면 브라우저 확장프로그램이 입력된 URL을 표시하는 다른 탭을 엽니다.
5. 입력된 URL이 있는 탭에서 로그인 프로세스를 진행합니다. 사용자 이름 및 암호 필드를 입력하고 로그인을 시도합니다. (올바른 암호를 제공할 필요는 없습니다.)

   캡처된 로그인 필드를 저장하라는 메시지가 표시됩니다.
6. **확인**을 선택합니다. 브라우저 확장은 **메타데이터가 응용 프로그램에 대해 업데이트되었음을**통해 캡처 상태 페이지를 업데이트합니다. 브라우저 탭이 닫힙니까?

7. Azure AD **Configure 로그인** 페이지에서 확인을 선택하면 **앱에 성공적으로 로그인할 수 있었습니다.**

8. **확인**을 선택합니다.

로그인 페이지를 캡처한 후 사용자 및 그룹을 할당할 수 있으며 일반 [암호 SSO 응용 프로그램과](what-is-single-sign-on.md)마찬가지로 자격 증명 정책을 설정할 수 있습니다.

> [!NOTE]
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
