---
title: 프로필 편집 흐름 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 프로필 편집 흐름을 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581901"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 프로필 편집 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>프로필 편집 흐름

프로필 편집 정책을 통해 사용자는 표시 이름, 성, 이름, 구/군/시 등과 같은 프로필 특성을 관리할 수 있습니다. 프로필 편집 흐름에는 다음 단계가 포함됩니다. 

1. 로컬 또는 소셜 계정을 사용하여 등록 또는 로그인 세션이 아직 활성 상태인 경우 Azure AD B2C는 사용자에게 권한을 부여하고 다음 단계로 건너뜁니다.
1. Azure AD B2C 디렉터리에서 사용자 프로필을 읽고 사용자가 특성을 편집하도록 합니다.

![프로필 편집 흐름](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>필수 구성 요소

아직 등록하지 않았다면 [Azure Active Directory B2C에 웹 애플리케이션을 등록](tutorial-register-applications.md)합니다.

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

애플리케이션에서 프로필을 편집할 수 있도록 하려면 프로필 편집 사용자 흐름을 사용합니다.

1. Azure AD B2C 테넌트 개요 페이지의 메뉴에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 탭에서 **프로필 편집** 사용자 흐름을 선택합니다. 
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다.
1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *profileediting1* 과 같습니다.
1. **ID 공급자** 에 대해 **메일 로그인** 을 선택합니다.
1. **사용자 특성** 으로 고객이 프로필에서 편집할 수 있도록 하려는 특성을 선택합니다. 예를 들어 **자세히 보기** 를 선택한 다음, **표시 이름** 및 **작업 제목** 에 대한 특성과 클레임을 모두 선택합니다. **확인** 을 클릭합니다.
1. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1* 이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭하고 이전에 만든 계정으로 로그인합니다.
1. 이제 사용자의 표시 이름 및 직함을 변경할 수 있습니다. **계속** 을 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>프로필 편집 정책 만들기

사용자 지정 정책은 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 집합입니다. 당사는 등록 및 로그인, 암호 재설정 및 프로필 편집 정책 등 미리 빌드된 몇 가지 정책으로 시작 팩을 제공합니다. 자세한 내용은 [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)을 참조하세요.

::: zone-end

## <a name="next-steps"></a>다음 단계

* [소셜 ID 공급자를 사용하여 로그인](add-identity-provider.md)을 추가합니다.