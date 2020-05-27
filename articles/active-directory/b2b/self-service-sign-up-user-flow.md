---
title: 셀프 서비스 가입 사용자 흐름 추가 - Azure AD
description: Facebook과 페더레이션하여 외부 사용자(게스트)가 자신의 Facebook 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfd430d750b2220882479a430322f4b4c4e0c44c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594732"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>앱에 셀프 서비스 가입 사용자 흐름 추가(미리 보기)
|     |
| --- |
| 셀프 서비스 가입은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

사용자 흐름을 애플리케이션과 연결하면 해당 앱에서 가입을 사용하도록 설정할 수 있습니다. 사용자 흐름과 연결할 애플리케이션을 둘 이상 선택할 수 있습니다. 사용자 흐름을 하나 이상의 애플리케이션과 연결하면 해당 앱을 방문하는 사용자가 사용자 흐름에 구성 된 옵션을 사용하여 가입할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="add-social-identity-providers-optional"></a>소셜 ID 공급자 추가(선택 사항)

Azure AD는 셀프 서비스 가입의 기본 ID 공급자입니다. 즉, 사용자는 기본적으로 Azure AD 계정을 사용하여 가입할 수 있습니다. 소셜 ID 공급자를 이러한 가입 흐름에 포함하여 Google 및 Facebook 계정을 지원하도록 할 수도 있습니다.

- [소셜 ID 공급자 목록에 Google 추가](google-federation.md)
- [소셜 ID 공급자 목록에 Facebook 추가](facebook-federation.md)
 
### <a name="define-custom-attributes-optional"></a>사용자 지정 특성 정의(선택 사항)

사용자 특성은 셀프 서비스 가입 중에 사용자로부터 수집된 값입니다. Azure AD에는 기본 제공 특성 세트가 함께 제공되지만 사용자 흐름에 사용할 사용자 지정 특성을 직접 만들 수 있습니다. 또한 Microsoft Graph API를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다. [사용자 흐름의 사용자 지정 특성 정의](user-flow-add-custom-attributes.md)를 참조하세요.

## <a name="enable-self-service-sign-up-for-your-tenant"></a>테넌트에 셀프 서비스 가입을 사용하도록 설정

애플리케이션에 셀프 서비스 가입 사용자 흐름을 추가하려면 먼저 테넌트에 이 기능을 사용하도록 설정해야 합니다. 사용하도록 설정하면 사용자 흐름에 컨트롤이 제공되고 이를 통해 사용자 흐름을 애플리케이션과 연결할 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. **사용자 설정**을 선택한 다음, **외부 사용자**에서 **외부 협업 설정 관리**를 선택합니다.
4. **사용자 흐름을 통해 게스트 셀프 서비스 가입 사용(미리 보기)** 토글을 **예**로 설정합니다.

   ![셀프 서비스 가입 사용](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>셀프 서비스 가입을 위한 사용자 흐름 만들기

다음으로, 셀프 서비스 가입을 위한 사용자 흐름을 만들고 애플리케이션에 추가합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **사용자 흐름(미리 보기)** 을 선택한 후 **새 사용자 흐름**을 선택합니다.

   ![새 사용자 흐름 단추 추가](media/self-service-sign-up-user-flow/new-user-flow.png)

5. **만들기** 페이지에서 사용자 흐름의 **이름**을 입력합니다. 이름 앞에 **B2X_1_** 가 자동으로 추가되는 것을 확인합니다.
6. **ID 공급자** 목록에서 외부 사용자가 애플리케이션에 로그인하는 데 사용할 수 있는 ID 공급자를 하나 이상 선택합니다. **Azure Active Directory 가입**이 기본적으로 선택됩니다. ID 공급자를 추가하는 방법에 대한 자세한 내용은 이 문서의 앞부분에 나오는 [시작하기 전에](#before-you-begin)를 참조하세요.
7. **사용자 특성**에서 사용자로부터 수집하려는 특성을 선택합니다. 추가 특성의 경우 **더 보기**를 선택합니다. 예를 들어 **자세히 보기**를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호**의 특성 및 클레임을 선택합니다. **확인**을 선택합니다.

   ![새 사용자 흐름 만들기 페이지](media/self-service-sign-up-user-flow/create-user-flow.png)

8. **만들기**를 선택합니다.
9. **사용자 흐름(미리 보기)** 목록에 새 사용자 흐름이 표시됩니다. 필요한 경우 페이지를 새로 고칩니다.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>특성 컬렉션 양식의 레이아웃 선택

가입 페이지에서 특성이 표시되는 순서를 선택할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**를 선택합니다.
2. **외부 ID**를 선택하고 **사용자 흐름(미리 보기)** 을 선택합니다.
3. 목록에서 셀프 서비스 가입 사용자 흐름을 선택합니다.
4. **사용자 지정**에서 **페이지 레이아웃**을 선택합니다.
5. 수집하도록 선택한 특성이 나열됩니다. 표시 순서를 변경하려면 특성을 선택하고 **위로 이동**, **아래로 이동**, **맨 위로 이동** 또는 **맨 아래로 이동**을 선택합니다.
6. **저장**을 선택합니다.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>셀프 서비스 가입 사용자 흐름에 애플리케이션 추가

이제 애플리케이션을 사용자 흐름과 연결할 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **셀프 서비스 가입**에서 **사용자 흐름(미리 보기)** 을 선택합니다.
5. 목록에서 셀프 서비스 가입 사용자 흐름을 선택합니다.
6. 왼쪽 메뉴의 **사용**에서 **애플리케이션**을 선택합니다.
7. **애플리케이션 추가**를 선택합니다.

   ![사용자 흐름에 애플리케이션 할당](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 목록에서 애플리케이션을 선택합니다. 또는 검색 상자를 사용하여 애플리케이션을 찾은 다음, 선택합니다.
9. **선택**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [소셜 ID 공급자 목록에 Google 추가](google-federation.md)
- [소셜 ID 공급자 목록에 Facebook 추가](facebook-federation.md)
