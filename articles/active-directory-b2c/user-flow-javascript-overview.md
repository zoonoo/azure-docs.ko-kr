---
title: Azure Active Directory B2C의 사용자 흐름에 대한 JavaScript 및 페이지 계약 버전 | Microsoft Docs
description: JavaScript를 사용하도록 설정하고 페이지 계약 버전을 사용하여 Azure Active Directory B2C의 사용자 흐름을 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360251"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>사용자 흐름에 JavaScript 및 페이지 계약 버전을 사용하는 방법 정보

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C는 사용자 흐름의 사용자 인터페이스 요소에 대해 HTML, CSS 및 JavaScript를 포함하는 패키지된 콘텐츠 세트를 제공합니다. 사용자 흐름에 [JavaScript](javascript-samples.md) 클라이언트 쪽 코드를 사용하려는 경우 JavaScript의 기초로 사용할 요소가 변경 불가능한지 확인하는 것이 좋습니다. 그러지 않으면 변경 시 사용자 흐름 페이지에서 예기치 않은 동작이 발생할 수 있습니다. 이러한 문제를 방지하기 위해 사용자 흐름에 대해 페이지 계약을 사용하고 페이지 계약 버전을 지정할 수 있습니다. 이렇게 하면 JavaScript의 기반이 되는 모든 콘텐츠 정의가 변경 불가능해집니다. 사용자 흐름에 JavaScript를 사용하지 않으려는 경우에도 사용자 흐름 페이지에 대해 페이지 계약 버전을 지정할 수 있습니다.

> [!NOTE]
> 이 문서에서는 사용자 흐름용 JavaScript에 대해 설명하지만, JavaScript를 사용하고 [사용자 지정 정책](page-contract.md)을 사용할 때 페이지 계약 버전을 선택할 수도 있습니다.

## <a name="enable-javascript"></a>JavaScript 사용

사용자 흐름 속성에서 JavaScript를 사용하도록 설정할 수 있으며, 이 경우 페이지 계약도 사용됩니다. 그런 후에 다음 섹션에 설명된 대로 페이지 계약 버전을 설정할 수 있습니다.

![JavaScript 설정 사용](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>페이지 계약 버전 지정

사용자 흐름의 속성에서 JavaScript를 사용하도록 설정했는지 여부와 관계없이, 사용자 흐름 페이지에 대해 페이지 계약 버전을 지정할 수 있습니다. 사용자 흐름을 열고 **페이지 레이아웃**을 선택합니다. **레이아웃 이름**에서 사용자 흐름 페이지를 선택한 다음, **페이지 계약 버전**을 선택합니다.

![JavaScript 설정 사용](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory B2C에서 사용하기 위한 JavaScript 샘플](javascript-samples.md)을 참조하세요.
