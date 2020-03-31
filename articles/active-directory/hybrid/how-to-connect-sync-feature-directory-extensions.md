---
title: 'Azure AD Connect 동기화: 디렉터리 확장 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect의 디렉터리 확장 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917916"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 동기화: 디렉터리 확장
디렉터리 확장을 사용하면 온-프레미스 Active Directory의 사용자 고유 특성을 사용하여 Azure AD(Active Directory)에서 스키마를 확장할 수 있습니다. 이 기능을 통해 온-프레미스를 계속 관리하는 특성을 이용하는 LOB 앱을 빌드할 수 있습니다. 이러한 특성은 확장을 통해 사용할 수 [있습니다.](https://docs.microsoft.com/graph/extensibility-overview
) [Microsoft 그래프 탐색기를](https://developer.microsoft.com/graph/graph-explorer)사용하여 사용 가능한 속성을 볼 수 있습니다. 이 기능을 사용하여 Azure AD에서 동적 그룹을 만들 수도 있습니다.

현재 이 특성을 이용하는 Office 365 워크로드가 없습니다.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Azure AD와 동기화할 특성 사용자 지정

설치 마법사의 사용자 지정 설정 경로에서 동기화할 추가 속성을 구성합니다.

>[!NOTE]
>사용 가능한 특성 상자에 입력을 할 때는 대/소문자를 구분해야 합니다.

![스키마 확장 마법사](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

설치 시 다음과 같은 특성이 표시됩니다. 이러한 특성은 유효한 후보입니다.

* 사용자 및 그룹 개체 유형
* 단일 값 특성: 문자열, 부울, 정수, 이진
* 다중 값 특성: 문자열, 이진


>[!NOTE]
> Azure AD Connect는 다중값 Active Directory 특성을 Azure AD에 다중값 디렉터리 확장으로 동기화하는 것을 지원하지만, 현재는 다중값 디렉터리 확장 특성에 업로드된 데이터를 검색/소비할 방법이 없습니다.

특성 목록은 Azure AD Connect 설치 도중에 만들어진 스키마 캐시에서 읽힙니다. 추가 특성을 사용하여 Active Directory 스키마를 확장한 경우 [스키마를 새로 고쳐야](how-to-connect-installation-wizard.md#refresh-directory-schema) 이러한 새 특성을 볼 수 있습니다.

Azure AD의 개체에는 최대 100개의 디렉터리 확장 특성이 있을 수 있습니다. 최대 길이는 250자입니다. 특성 값이 더 긴 경우 동기화 엔진에서 잘립니다.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>마법사에서 만든 Azure AD의 구성 변경 사항

Azure AD Connect를 설치하는 동안 이러한 특성을 사용할 수 있는 애플리케이션이 등록됩니다. Azure Portal에서 다음 애플리케이션을 볼 수 있습니다. 그 이름은 항상 **테넌트 스키마 확장 앱입니다.**

![스키마 확장 앱](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

이 응용 **프로그램을** 보려면 모든 응용 프로그램을 선택해야합니다.

특성은 **확장 \_{ApplicationId}와\_** 함께 접두사에 붙어 있습니다. ApplicationId는 Azure AD 테넌트의 모든 특성에 대해 동일한 값을 갖습니다. 이 항목의 다른 모든 시나리오에 대해 이 값이 필요합니다.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Microsoft 그래프 API를 사용하여 속성 보기

이러한 특성은 이제 Microsoft 그래프 [탐색기를](https://developer.microsoft.com/graph/graph-explorer#)사용하여 Microsoft 그래프 API를 통해 사용할 수 있습니다.

>[!NOTE]
> Microsoft 그래프 API에서 반환할 특성을 요청해야 합니다. 다음과 같은 특성을 명시적으로 `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`선택합니다.
>
> 자세한 내용은 [Microsoft Graph: 쿼리 매개 변수 사용](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)을 참조하세요.

## <a name="use-the-attributes-in-dynamic-groups"></a>동적 그룹의 특성 사용

더 유용한 시나리오 중 하나는 동적 보안 또는 Office 365 그룹에서 이러한 특성을 사용하는 것입니다.

1. Azure AD에서 새 그룹을 만듭니다. 좋은 이름을 지정하고 멤버 **자격 유형이** **동적 사용자인지**확인합니다.

   ![새 그룹과의 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. 동적 **쿼리 추가를**선택합니다. 속성을 보면 이러한 확장 된 특성이 표시되지 않습니다. 먼저 추가해야 합니다. **사용자 지정 확장 속성 받기를**클릭하고 응용 프로그램 ID를 입력하고 속성 새로 **고침을**클릭합니다.

   ![디렉터리 확장이 추가된 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. 속성 드롭다운을 열고 추가한 속성이 표시됩니다.

   ![UI에 표시되는 새 속성이 있는 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   요구 사항에 맞게 식을 완성합니다. 이 예제에서는 규칙이 **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "영업 및 마케팅")으로**설정됩니다.

4. 그룹을 만든 후 Azure AD에 구성원을 채우고 구성원을 검토할 시간을 줍니다.

   ![동적 그룹의 구성원이 있는 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](how-to-connect-sync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
