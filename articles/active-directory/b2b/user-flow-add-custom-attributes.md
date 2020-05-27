---
title: Azure AD 사용자 흐름에 대한 사용자 지정 특성 추가
description: 셀프 서비스 가입 사용자 흐름에 대한 특성을 사용자 지정하는 방법에 대해 알아봅니다.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594712"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>사용자 흐름의 사용자 지정 특성 정의(미리 보기)
|     |
| --- |
| 사용자 지정 사용자 특성 기능은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

각 애플리케이션의 경우 가입 중에 수집하려는 정보에 대한 요구 사항이 서로 다를 수 있습니다. Azure AD에는 이름, 성, 도시, 우편 번호 등의 특성에 저장된 일련의 기본 정보가 포함되어 있습니다. Azure AD를 사용하면 외부 사용자가 사용자 흐름을 통해 가입할 때 게스트 계정에 저장된 특성 세트를 확장할 수 있습니다.

Azure Portal에 사용자 지정 특성을 만들고 셀프 서비스 가입 사용자 흐름에서 사용할 수 있습니다. 또한 [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api)를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다. Microsoft Graph API는 확장 특성이 있는 사용자를 만들고 업데이트하는 것을 지원합니다. Graph API의 확장 특성은 `extension_<Application-client-id>_attributename` 규칙을 사용하여 명명됩니다. 다음은 그 예입니다.

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<Application-client-id>`는 **애플리케이션(클라이언트) ID**옆에 있는 **앱 등록** 페이지에서 찾을 수 있습니다. 이 ID는 테넌트에만 해당됩니다.

## <a name="create-a-custom-attribute"></a>사용자 지정 특성 만들기

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **사용자 지정 사용자 특성(미리 보기)** 을 선택합니다. 사용 가능한 사용자 특성이 나열됩니다.

   ![가입할 사용자 특성을 선택합니다.](media/user-flow-add-custom-attributes/user-attributes.png)

5. 특성을 추가하려면 **추가**를 선택합니다.
6. **특성 추가** 창에서 다음 값을 입력합니다.

   - **이름** - 사용자 지정 특성(예: "Shoesize")에 대해 이름을 제공합니다.
   - **데이터 형식** - 데이터 형식(**문자열**, **부울** 또는 **Int**)을 선택합니다.
   - **설명** - 필요에 따라 내부에서 사용할 사용자 지정 특성에 대한 설명을 입력합니다. 이 설명은 사용자에게 표시되지 않습니다.

   ![특성 추가](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. **만들기**를 선택합니다.

이제 사용자 지정 특성을 사용자 특성 목록 및 사용자 흐름에서 사용할 수 있습니다. 사용자 지정 특성은 사용자 특성 목록에 추가될 때가 아니라 사용자 흐름에 사용될 때 처음으로 만들어집니다.

새로 만든 사용자 지정 특성을 사용하는 사용자 흐름을 사용하여 새 사용자를 만든 후에는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 개체를 쿼리할 수 있습니다. 이제 등록 과정 동안 수집되는 특성 목록에 **ShoeSize**가 표시되며 애플리케이션으로 다시 전송되는 토큰에도 표시됩니다. 애플리케이션으로 다시 전송된 토큰에 이러한 클레임을 포함하는 방법에 대한 자세한 내용은 [디렉터리 확장 옵션 클레임 구성](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[앱에 셀프 서비스 가입 사용자 흐름 추가](self-service-sign-up-user-flow.md)