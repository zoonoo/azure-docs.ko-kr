---
title: JavaScript 및 페이지 계약 버전-Azure Active Directory B2C | Microsoft Docs
description: JavaScript를 사용 하도록 Azure Active Directory B2C에서 페이지 계약 버전을 사용 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511972"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 JavaScript 및 페이지 계약 버전

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 사용자 지정 정책을 확인 하 고 사용자 흐름에서 사용자 인터페이스 요소에 대 한 HTML, CSS 및 JavaScript를 포함 하는 패키지 콘텐츠를 제공 합니다. JavaScript 응용 프로그램을 사용 하려면 요소를 추가 해야 하 [사용자 지정 정책](active-directory-b2c-overview-custom.md) 또는 사용자 흐름에 대 한 포털을 사용 하도록 설정, 페이지 계약을 선택 하 고 사용 하 여 [b2clogin.com](b2clogin.md) 요청에서.

사용 하도록 설정 하려는 경우 [JavaScript](javascript-samples.md) 클라이언트 쪽 코드 해야 되도록에서 JavaScript를 기반으로 하는 요소는 변경할 수 없습니다. 그렇지 않은 경우 변경 내용을 사용자 페이지에 예기치 않은 동작이 발생할 수 있습니다. 이러한 문제를 방지 하려면 페이지 계약의 사용을 적용 하 고 페이지 계약 버전을 지정할 수 있습니다. 이렇게 하면 JavaScript를 기반으로 한 사용자는 모든 콘텐츠 정의 변경할 수 없습니다. JavaScript를 사용 하도록 설정 하지 않으려는 경우에 페이지에 대 한 페이지 계약 버전을 지정할 수 있습니다.

## <a name="user-flows"></a>사용자 흐름

사용자 흐름 속성에서 JavaScript를 사용하도록 설정할 수 있으며, 이 경우 페이지 계약도 사용됩니다. 그런 후에 다음 섹션에 설명된 대로 페이지 계약 버전을 설정할 수 있습니다.

![JavaScript 설정 사용](media/user-flow-javascript-overview/javascript-settings.png)

사용자 흐름의 속성에서 JavaScript를 사용하도록 설정했는지 여부와 관계없이, 사용자 흐름 페이지에 대해 페이지 계약 버전을 지정할 수 있습니다. 사용자 흐름을 열고 **페이지 레이아웃**을 선택합니다. **레이아웃 이름**에서 사용자 흐름 페이지를 선택한 다음, **페이지 계약 버전**을 선택합니다.

![JavaScript 설정 사용](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>사용자 지정 정책

추가한 사용자 지정 정책에서 JavaScript를 사용 하도록 설정 합니다 **ScriptExecution** 요소를 **RelyingParty** 요소 사용자 지정 정책 파일에서. 자세한 내용은 [Azure Active Directory B2C에서 사용 하기 위해 JavaScript 샘플](javascript-samples.md)합니다.

사용자 지정 정책에서 JavaScript를 사용 하면, 여부 페이지에 대 한 페이지 계약 버전을 지정할 수 있습니다. 페이지 계약을 지정 하는 방법에 대 한 자세한 내용은 참조 하세요. [사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 페이지에 대 한 계약 선택](page-contract.md)합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C에서 사용하기 위한 JavaScript 샘플](javascript-samples.md)을 참조하세요.
