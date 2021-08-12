---
title: Azure AD Connect 클라우드 동기화를 통해 식 작성기를 사용하는 방법
description: 이 문서에서는 클라우드 동기화를 통해 식 작성기를 사용하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e169c2cbabf9e9e37bb93b1e3ee26080105d501e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776942"
---
# <a name="expression-builder-with-cloud-sync"></a>클라우드 동기화를 통해 식 작성기 사용
식 작성기는 클라우드 동기화 아래에 있는 Azure의 새로운 블레이드로, 복잡한 식을 작성하는 데 도움이 되며, 이를 통해 클라우드 동기화 환경에 적용하기 전에 이러한 식을 테스트할 수 있습니다.

## <a name="use-the-expression-builder"></a>식 작성기 사용
식 작성기에 액세스하려면 다음 단계를 따르세요.

 1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택합니다.
 4. **구성** 아래에서 구성을 선택합니다.
 5. **특성 관리** 에서 **매핑을 편집하려면 클릭** 을 선택합니다.
 6. **특성 매핑 편집** 블레이드에서 **특성 매핑 추가** 를 클릭합니다.
 7. **매핑 유형** 에서 **식** 을 선택합니다.
 8. **식 작성기 사용해 보기(미리 보기)** 를 선택합니다.
 ![식 작성기 사용](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>식 작성
이 섹션에서는 드롭다운을 사용하여 지원되는 함수 목록에서 선택할 수 있습니다.  그런 다음, 선택한 함수에 따라 입력할 추가 필드를 제공합니다.  **식 적용** 을 선택하면 구문이 **식 입력** 상자에 표시됩니다.

예를 들어 드롭다운에서 **Replace** 를 선택하면 추가 상자가 제공됩니다.  함수의 구문이 연한 파란색 상자에 표시됩니다.  표시되는 상자는 선택한 함수의 구문에 해당합니다.  Replace는 제공된 매개 변수에 따라 다르게 작동합니다.  이 예에서는 다음을 사용합니다.

- oldValue 및 replacementValue가 제공된 경우:
    - 소스에서 oldValue의 모든 항목을 replacementValue로 대체합니다.

자세한 내용은 [Replace](reference-expressions.md#replace)를 참조하세요.

가장 먼저 해야 할 일은 replace 함수의 원본인 특성을 선택하는 것입니다. 이 예에서는 **mail** 특성을 선택했습니다. 

다음으로, oldValue에 대한 값을 입력합니다.  이 oldValue는 **@fabrikam.com** 입니다.  마지막으로 replacementValue 상자에서 **@contoso.com** 값을 입력합니다.

따라서 기본적으로 식은 값이 @fabrikam.com인 사용자 개체의 mail 특성을 @contoso.com 값으로 바꿉니다.  **식 추가** 단추를 클릭하면 **식 입력** 에서 구문을 볼 수 있습니다.


>[!NOTE]
>Replace를 선택한 경우 발생하는 구문에 따라 oldValue 및 replacementValue와 일치하는 값을 상자에 입력해야 합니다.

지원되는 식에 대한 자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](reference-expressions.md)을 참조하세요.

### <a name="information-on-expression-builder-input-boxes"></a>식 작성기 입력 상자에 대한 정보
선택한 함수에 따라 식 작성기에서 제공하는 상자에 여러 값이 허용됩니다.  예를 들어 JOIN 함수는 지정된 특성과 연결된 문자열 또는 값을 허용합니다.  예를 들어 특성 값 [givenName]에 포함된 값을 사용하고 이 값을 “@contoso.com” 문자열 값과 조인하여 이메일 주소를 만들 수 있습니다.

  ![입력 상자 값](media/how-to-expression-builder/expression-8.png)

허용되는 값 및 식을 작성하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 특성 매핑에 대한 식 작성](reference-expressions.md)을 참조하세요.

## <a name="test-an-expression"></a>식 테스트
이 섹션에서는 식을 테스트할 수 있습니다.  드롭다운 목록에서 **mail** 특성을 선택합니다.  **@fabrikam.com** 을 포함하는 값을 입력하고 **식 테스트** 를 클릭합니다.  

**식 출력 보기** 상자에 **@contoso.com** 값이 표시됩니다.

 ![식 테스트](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>식 배포
식에 만족하면 **식 적용** 단추를 클릭하기만 하면 됩니다.
![식 추가](media/how-to-expression-builder/expression-5.png)

그러면 에이전트 구성에 식이 추가됩니다.
![에이전트 구성](media/how-to-expression-builder/expression-6.png)

## <a name="setting-a-null-value-on-an-expression"></a>식에 NULL 값 설정
특성 값을 NULL로 설정합니다.  값이 `""`인 식을 사용할 수 있습니다.  이렇게 하면 NULL 값이 대상 특성으로 전달됩니다.

![NULL 값](media/how-to-expression-builder/expression-7.png)



## <a name="next-steps"></a>다음 단계 

- [Azure Active Directory에서 특성 매핑에 대한 식 작성](reference-expressions.md)
- [클라우드 동기화 구성](how-to-configure.md)
