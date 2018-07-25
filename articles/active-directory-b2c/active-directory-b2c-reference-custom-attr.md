---
title: Azure Active Directory B2C에서 사용자 지정 특성 정의 | Microsoft Docs
description: Azure Active Directory B2C에서 응용 프로그램에 대한 사용자 지정 특성을 정의하여 고객에 대한 정보를 수집합니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968777"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 특성 정의

 모든 고객 지향 응용 프로그램은 수집해야 하는 정보에 대한 고유한 요구 사항이 있습니다. Azure AD(Azure Active Directory) B2C 테넌트에는 지정된 이름, 성, 도시, 우편 번호 등의 기본 제공 정보 집합이 특성에 저장되어 제공됩니다. Azure AD B2C를 사용하면 각 고객 계정에 저장된 특성 집합을 확장할 수 있습니다. 
 
 [Azure Portal](https://portal.azure.com/)에서 사용자 지정 특성을 만든 후 등록 정책, 등록 또는 로그인 정책, 프로필 편집 정책에서 사용할 수 있습니다. 또한 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 이러한 특성을 읽고 쓸 수 있습니다. Azure AD B2C의 사용자 지정 특성은 [Azure AD Graph API 디렉터리 스키마 확장](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)을 사용합니다.

## <a name="create-a-custom-attribute"></a>사용자 지정 특성 만들기

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환**을 선택합니다. 

    ![Azure AD B2C 테넌트로 전환](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    테넌트를 포함하는 디렉터리를 선택합니다.

    ![디렉터리 선택](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 특성**을 선택한 후 **추가**를 선택합니다.
5. 사용자 지정 특성(예: "ShoeSize")에 대해 **이름**을 제공합니다.
6. **데이터 형식**을 선택합니다. **문자열**를 **부울** 및 **Int**만 사용할 수 있습니다.
7. 필요에 따라 정보 제공을 위해 **설명**을 입력합니다. 
8. **만들기**를 클릭합니다.

이제 사용자 지정 특성은 **사용자 특성** 목록 및 정책에서 사용할 수 있습니다. 사용자 특성은 **사용자 특성** 목록에 추가될 때가 아니라 정책에 사용될 때 처음으로 만들어집니다.

## <a name="use-a-custom-attribute-in-your-policy"></a>정책에 사용자 지정 특성 사용

1. Azure AD B2C 테넌트에서 **등록 또는 로그인 정책**을 선택합니다.
2. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다. 
3. **편집**을 클릭합니다.
4. **등록 특성**을 선택하고 사용자 지정 특성을 선택합니다(예: "ShoeSize"). **확인**을 클릭합니다.
5. **응용 프로그램 클레임**을 선택하고 사용자 지정 특성을 선택합니다. **확인**을 클릭합니다.
6. **저장**을 클릭합니다.

정책에서 **지금 실행** 기능을 사용하여 고객 환경을 확인할 수 있습니다. 이제 등록 과정 동안 수집되는 특성 목록에 **ShoeSize**가 표시되며 응용 프로그램으로 다시 전송되는 토큰에도 표시됩니다.

