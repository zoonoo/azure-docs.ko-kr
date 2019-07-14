---
title: Azure AD 앱 갤러리에서 애플리케이션에 한 번 클릭 SSO 구성 | Microsoft Docs
description: Azure AD 앱 갤러리에서 애플리케이션에 한 번 클릭 SSO를 구성하는 단계입니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064817"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Azure AD 갤러리 애플리케이션에 대한 한 번 클릭 SSO 기능

 이 자습서에서는 SSO 구성을 위한 UI를 제공하는 모든 SAML 애플리케이션에 대해 한 번 클릭 SSO를 수행하는 방법을 알아봅니다.

## <a name="introduction-to-one-click-sso"></a>한 번 클릭 SSO 소개

한 번 클릭 SSO 기능은 SAML 프로토콜을 지원하는 Azure AD 갤러리 앱에 Single Sign On을 구성하기 위해 도입되었습니다. Azure AD SSO 구성 페이지에 고객이 애플리케이션 쪽에서 Azure AD 메타데이터를 자동으로 구성할 수 있는 옵션이 제공됩니다. 고객이 최소한의 수동 작업으로 신속하게 SSO를 설정할 수 있도록 하는 것이 목표입니다. 

## <a name="advantages-of-the-one-click-sso"></a>한 번 클릭 SSO의 이점

- 고객이 애플리케이션 쪽에서 수동 설정을 수행해야 하는 갤러리 애플리케이션의 SSO를 빠르게 구성합니다.
- 효율적이고 정확한 구성 방법입니다.
- 애플리케이션과 같이 설정에 필요한 파트너 통신 또는 지원은 SAML 구성을 위한 UI를 제공하지 않습니다.

## <a name="prerequisites"></a>필수 조건

- 한 번 클릭 SSO로 구성하려는 관리자 자격 증명을 사용하여 애플리케이션 활성 구독
- 브라우저에 설치된 Microsoft의 **내 앱 보안 로그인 브라우저 확장** 이 확장에 대한 자세한 내용은 이 [링크](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)를 참조하세요.

## <a name="one-click-sso-feature-step-by-step-details"></a>한 번 클릭 SSO 기능 단계별 세부 정보

1. Azure AD 앱 갤러리에서 애플리케이션을 추가합니다.

2. Single Sign-On을 클릭합니다.

3. Single Sign-On 사용을 클릭합니다.

4. 기본 SAML 구성 섹션의 필수 구성 값을 채웁니다.

    > [!NOTE] 
    > 애플리케이션에 사용자 지정 클레임을 구성해야 하는 경우 한 번 클릭 SSO를 수행하기 전에 구성합니다.

5. 한 번 클릭 SSO 기능이 모든 갤러리 애플리케이션에 대해 구현된 경우 다음 화면이 표시됩니다. **내 앱 보안 로그인 브라우저 확장**이 설치되지 않은 경우 **확장 설치** 옵션을 클릭해야 합니다.

    ![내 앱 보안 로그인 브라우저 확장 설치](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 브라우저에 확장을 추가한 후 **애플리케이션 이름 설정**을 클릭하면 애플리케이션 관리자 포털로 리디렉션됩니다. 애플리케이션을 사용하려면 관리자 권한으로 로그인해야 합니다.

    ![애플리케이션 이름 설정](./media/one-click-sso-tutorial/setup-sso.png)

7. 브라우저 확장이 애플리케이션을 자동으로 구성합니다. 우선 진행할 것인지 묻는 확인 메시지가 표시됩니다. **예**를 클릭합니다.

    ![자동 입력 데이터 저장](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > 애플리케이션에 추가 탐색 또는 단계가 필요한 경우에는 해당 단계를 수행할 것인지 묻는 메시지가 표시됩니다. 

8. 구성이 완료되면 **확인**을 클릭하여 변경 내용을 저장합니다.

    ![자동 입력 데이터 저장](./media/one-click-sso-tutorial/save-data.png)

9. 성공 확인 팝업 메시지가 표시되고 SSO 설정이 성공적으로 구성됩니다. 그런 다음 애플리케이션을 테스트할 수 있습니다.

    ![구성된 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 구성이 성공적으로 완료되면 애플리케이션이 로그오프되고 Azure Portal로 돌아갑니다.

11. 테스트 단추를 클릭하여 Single Sign-On을 테스트할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [내 앱 보안 로그인 브라우저 확장이란](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 