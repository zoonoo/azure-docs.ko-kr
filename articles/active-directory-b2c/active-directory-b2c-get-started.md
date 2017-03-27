---
title: "Azure Active Directory B2C: Azure Active Directory B2C 테넌트 만들기 | Microsoft Docs"
description: "Azure Active Directory B2C 테넌트를 만드는 방법에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: Azure AD B2C 테넌트 만들기
Azure AD(Microsoft Azure Active Directory) B2C를 사용하여 시작하려면 이 문서에서 요약한 네 단계를 수행합니다.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>1단계: Azure 구독에 등록
Azure 구독이 이미 있는 경우 이 단계를 건너뜁니다. 그렇지 않은 경우 [Azure 구독](../active-directory/sign-up-organization.md) 에 등록하고 Azure AD B2C에 액세스합니다.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>2단계: Azure AD B2C 테넌트 만들기
다음 단계에 따라 새 Azure AD B2C 테넌트를 만듭니다. 현재 B2C 기능은 기존 테넌트에 설정할 수 없습니다.

1. [Azure Portal](https://portal.azure.com/)에 구독 관리자로 로그인합니다. 이는 동일한 직장이나 학교 계정 또는 Azure에 등록하는 데 사용한 동일한 Microsoft 계정입니다.
2. **새로 만들기**(또는 축소된 경우 + 단추)를 클릭하고 **Marketplace 검색** 필드에 "Azure Active Directory B2C"를 입력합니다.
   
    ![Azure AD B2C 옵션 찾기 스크린샷](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. 결과에서 **Azure Active Directory B2C**를 클릭합니다.

    ![Azure Active Directory B2C에 대한 결과 스크린샷](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. B2C에 대한 세부 정보가 나온 페이지가 표시됩니다.  아래에 **만들기**를 클릭하여 새 Azure Active Directory B2C 테넌트 구성을 시작합니다.
5. **새 Azure AD B2C 테넌트 만들기**를 클릭합니다.
6. 테넌트에 사용할 **조직 이름, 도메인 이름 및 국가 또는 지역**을 선택합니다.

    ![새 테넌트를 만들기 위한 양식 스크린샷](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. **만들기**를 클릭하여 사용자의 테넌트를 만듭니다.  몇 분이 걸릴 수 있으며 완료되면 알림에 경고가 표시됩니다.

8. 이제 테넌트가 만들어지고 Active Directory 확장에 표시됩니다. 또한 사용자는 테넌트의 전역 관리자가 되었습니다. 필요에 따라 다른 전역 관리자를 추가할 수 있습니다.
   
   > [!IMPORTANT]
   > 프로덕션 앱에 대해 B2C 테넌트를 사용하려는 경우 [프로덕션 규모와 B2C 테넌트 미리 보기 비교](active-directory-b2c-reference-tenant-type.md)의 문서를 참조하세요. 기존 B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들어야 하는 경우 알려진 문제가 발생합니다. 다른 도메인 이름으로 B2C 테넌트를 만들어야 합니다.
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>3단계: Azure 포털의 B2C 기능 블레이드로 이동
1. 왼쪽 탐색 모음 아래에서 **더 많은 서비스**를 확장합니다.
2. **Azure AD B2C**를 검색하고 결과를 클릭합니다. (앞으로 빠르게 액세스할 수 있도록 즐겨찾기에 이 기능을 추가할 수 있습니다.)

    ![탐색 창에서 Azure AD B2C의 검색 스크린샷](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. 새 브라우저 탭 또는 창에 Azure 포털이 열리고 B2C 기능 블레이드가 표시됩니다.
   
4. 이 블레이드를 쉽게 액세스할 수 있도록 시작 보드에 고정합니다. (핀 고정 도구는 기능 블레이드의 오른쪽 위 모서리에 있습니다.)
   
    ![B2C 기능 블레이드 및 핀 고정 단추 스크린샷](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>4단계: Azure AD B2C 테넌트를 Azure 구독에 연결
프로덕션 앱에 대해 B2C 테넌트를 사용하려는 경우 사용 요금을 지불하기 위해 Azure AD B2C 테넌트를 Azure 구독에 연결해야 합니다. 이 방법을 알아보려면 [이 문서](active-directory-b2c-how-to-enable-billing.md)를 읽어보세요.

   > [!IMPORTANT]
   > Azure AD B2C 테넌트를 Azure 구독에 연결하지 않으면 Azure Portal의 B2C 기능 블레이드에 경고 메시지("이 B2B 테넌트에 연결된 구독이 없거나 구독에 유의해야 합니다.")가 표시됩니다. 프로덕션에 앱을 전달하기 전에 이 단계를 수행해야 합니다.
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Azure 포털의 B2C 기능 블레이드에 쉽게 액세스합니다.
검색 가능성을 개선하기 위해 Azure 포털에서 B2C 기능 블레이드에 바로 가기를 추가 했습니다.

1. B2C 테넌트의 전역 관리자로 Azure 포털에 로그인합니다. 이미 다른 테넌트로 로그인했다면 (오른쪽 위 모서리에서) 테넌트를 전환합니다.
2. 왼쪽 탐색 창에서 **찾아보기** 를 클릭합니다.
3. B2C 기능 블레이드에 액세스하려면 **Azure AD B2C** 를 클릭합니다.
   
    ![B2C 기능 블레이드로 이동 스크린샷](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory B2C: 응용 프로그램 등록](active-directory-b2c-app-registration.md)을 읽어서 Azure AD B2C에 응용 프로그램을 등록하고 빠른 시작 응용 프로그램을 빌드하는 방법을 알아봅니다.


