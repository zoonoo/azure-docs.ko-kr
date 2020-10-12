---
title: Azure Marketplace 애플리케이션의 한 번 클릭 SSO(Single Sign-On) 구성 | Microsoft Docs
description: Azure Marketplace의 애플리케이션에 대한 SSO 한 번 클릭 구성을 위한 단계입니다.
services: active-directory
documentationCenter: na
author: kenwith
manager: celestedg
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7e72718e13106b9d895921a2335f1525a7b88c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763128"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Single Sign-On의 한 번 클릭 앱 구성

 이 자습서에서는 Azure Marketplace에서 SAML 지원 Azure AD(Azure Active Directory) 애플리케이션에 대해 한 번 클릭 SSO(Single Sign-On) 구성을 수행하는 방법에 대해 알아봅니다.

## <a name="introduction-to-one-click-sso"></a>한 번 클릭 SSO 소개

한 번 클릭 SSO 기능은 SAML 프로토콜을 지원하는 Azure Marketplace 앱에 Single Sign On을 구성하기 위해 설계되었습니다. Azure AD SSO 구성 페이지에 이 옵션을 사용하면 애플리케이션 쪽에서 Azure AD 메타데이터를 자동으로 구성할 수 있습니다. 이 방법을 통해 최소한의 노력으로 SSO를 신속하게 설정할 수 있습니다.

## <a name="advantages-of-one-click-sso"></a>한 번 클릭 SSO의 이점

- 애플리케이션 쪽에서 수동 설치를 필요로 하는 Azure Marketplace 애플리케이션의 빠른 SSO 구성
- 더 효율적이고 정확한 SSO 구성
- 설치를 위한 파트너 통신 또는 지원 불필요. 이 애플리케이션은 SAML 구성을 위한 UI를 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

- SSO를 사용하여 구성할 애플리케이션의 활성 구독. 관리자 자격 증명도 필요합니다.
- 브라우저에 설치된 Microsoft의 **내 앱 보안 로그인 확장**. 자세한 내용은 [내 앱 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)을 참조하세요.

## <a name="one-click-sso-configuration-steps"></a>한 번 클릭 SSO 구성 단계

1. Azure Marketplace에서 애플리케이션을 추가합니다.

2. **Single Sign-On**을 선택합니다.

3. **Single Sign-On 사용**을 선택합니다.

4. **기본 SAML 구성** 섹션의 필수 구성 값을 채웁니다.

    > [!NOTE]
    > 애플리케이션에 구성해야 하는 사용자 지정 클레임이 있는 경우 한 번 클릭 SSO를 수행하기 전에 해당 클레임을 처리하세요.

5. Azure Marketplace 애플리케이션에서 한 번 클릭 SSO 기능을 사용할 수 있는 경우 다음 화면이 표시됩니다. **확장 설치**를 선택하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 할 수 있습니다.

   ![내 앱 보안 로그인 브라우저 확장 설치](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 확장이 브라우저에 추가되면 **설정 \<Application Name\>** 을 선택합니다. 애플리케이션 관리 포털로 리디렉션된 후에 관리자로 로그인합니다.

   ![애플리케이션 이름 설정](./media/one-click-sso-tutorial/setup-sso.png)

7. 브라우저 확장이 애플리케이션에서 SSO를 자동으로 구성합니다. **예**를 선택하여 확인합니다.

   ![자동 입력 데이터 저장](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > 애플리케이션에 대한 SSO 구성에 추가 단계가 필요한 경우에는 해당 단계를 수행하라는 메시지가 표시됩니다.

8. 구성이 완료되면 **확인**을 선택하여 변경 내용을 저장합니다.

   ![자동 입력 데이터 저장](./media/one-click-sso-tutorial/save-data.png)

9. SSO 설정이 성공적으로 구성되었음을 알려 주는 확인 창이 표시됩니다.

   ![구성된 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 구성이 성공적으로 완료된 후에는 애플리케이션에서 로그아웃되고 Azure Portal로 되돌아갑니다.

11. **테스트**를 선택하여 Single Sign-On을 테스트할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [내 앱 보안 로그인 브라우저 확장이란?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
