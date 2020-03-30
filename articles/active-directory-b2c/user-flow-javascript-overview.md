---
title: 자바스크립트 및 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 JavaScript를 사용하도록 설정하고 페이지 레이아웃 버전을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185842"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 자바스크립트 및 페이지 레이아웃 버전

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C는 사용자 흐름 및 사용자 지정 정책의 사용자 인터페이스 요소에 대해 HTML, CSS 및 JavaScript를 포함하는 패키지된 콘텐츠 집합을 제공합니다.

응용 프로그램에 대한 자바 스크립트를 사용하려면 다음

* Azure 포털을 사용하여 사용자 흐름에서 활성화
* 페이지 [레이아웃](page-layout.md) 선택
* 요청에 [b2clogin.com](b2clogin.md) 사용

[자바스크립트](javascript-samples.md) 클라이언트 측 코드를 사용하려면 자바스크립트를 기반으로 하는 요소는 변경할 수 없어야 합니다. 변경할 수 없는 경우 변경하면 사용자 페이지에서 예기치 않은 동작이 발생할 수 있습니다. 이러한 문제를 방지하려면 페이지 레이아웃사용을 적용하고 페이지 레이아웃 버전을 지정하여 JavaScript를 기반으로 한 콘텐츠 정의가 불변인지 확인합니다. JavaScript를 사용하도록 설정하지 않으려는 경우에도 페이지의 페이지 레이아웃 버전을 지정할 수 있습니다.

## <a name="enable-javascript"></a>JavaScript 사용

사용자 흐름 **속성에서**JavaScript를 활성화할 수 있습니다. 자바 스크립트를 사용하도록 설정하면 페이지 레이아웃의 사용도 적용됩니다. 그런 다음 다음 섹션에 설명된 대로 사용자 흐름에 대한 페이지 레이아웃 버전을 설정할 수 있습니다.

![자바스크립트 사용 설정이 강조 표시된 사용자 흐름 속성 페이지](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>페이지 레이아웃 버전 선택

사용자 흐름의 속성에서 JavaScript를 사용하도록 설정하든 관계없이 사용자 흐름 페이지에 대한 페이지 레이아웃 버전을 지정할 수 있습니다. 사용자 흐름을 열고 **페이지 레이아웃을 선택합니다.** **레이아웃 이름에서**사용자 흐름 페이지를 선택하고 **페이지 레이아웃 버전을**선택합니다.

다른 페이지 레이아웃 버전에 대한 자세한 내용은 [페이지 레이아웃 버전 변경 로그를](page-layout.md)참조하십시오.

![페이지 레이아웃 버전 드롭다운을 표시하는 포털의 페이지 레이아웃 설정](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C에서 사용할 자바스크립트 샘플에서 자바스크립트](javascript-samples.md)사용 사례를 찾을 수 있습니다.
