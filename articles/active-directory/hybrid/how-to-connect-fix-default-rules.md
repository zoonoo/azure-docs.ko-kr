---
title: 수정된 기본 규칙을 고치는 방법 - Azure AD Connect | Microsoft Docs
description: Azure AD Connect 함께 제공되는 수정된 기본 규칙을 고치는 방법에 대해 알아봅니다.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91320050"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect에서 수정된 기본 규칙 고치기

Azure AD(Azure Active Directory) Connect는 동기화에 기본 규칙을 사용합니다.  아쉽게도 이러한 규칙이 모든 조직에 보편적으로 적용되는 것은 아닙니다. 요구 사항에 따라 규칙을 수정해야 할 수 있습니다. 이 문서에서는 가장 일반적인 사용자 지정의 2가지 예와 이러한 사용자 지정을 수행하는 올바른 방법을 설명합니다.

>[!NOTE] 
> 필요한 사용자 지정을 수행하기 위해 기존 기본 규칙을 수정하는 것은 지원되지 않습니다. 이렇게 하면 이러한 규칙을 이후 릴리스의 최신 버전으로 업데이트할 수 없습니다. 또한 필요한 버그 픽스 또는 새로운 기능을 얻을 수 없습니다. 이 문서에서는 기존 기본 규칙을 수정하지 않고 동일한 결과를 얻는 방법을 설명합니다. 

## <a name="how-to-identify-modified-default-rules"></a>수정된 기본 규칙을 식별하는 방법
Azure AD Connect 버전 1.3.7.0부터 수정된 기본 규칙을 쉽게 식별할 수 있습니다. **Apps on Desktop(데스크톱의 앱)** 으로 이동하고 **동기화 규칙 편집기** 를 선택합니다.

![동기화 규칙 편집기가 강조 표시된 Azure AD Connect](media/how-to-connect-fix-default-rules/default1.png)

편집기에서 수정된 기본 규칙은 이름 앞에 경고 아이콘과 함께 표시됩니다.

![경고 아이콘](media/how-to-connect-fix-default-rules/default2.png)

 옆에 같은 이름의 사용하지 않는 규칙도 표시됩니다. 이 규칙이 표준 기본 규칙입니다.

![표준 기본 규칙과 수정된 기본 규칙이 표시된 동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>일반 사용자 지정
다음은 기본 규칙에 대한 일반적인 사용자 지정입니다.

- 특성 흐름 변경
- 범위 지정 필터 변경
- 조인 조건 변경

규칙을 변경하기 전에 다음을 수행합니다.

- 동기화 스케줄러를 사용하지 않도록 설정합니다. 스케줄러 는 기본적으로 30분마다 실행됩니다. 변경을 수행하고 새 규칙의 문제를 해결하는 동안 스케줄러가 시작되지 않도록 합니다. 스케줄러를 일시적으로 사용하지 않으려면 PowerShell을 시작하고 `Set-ADSyncScheduler -SyncCycleEnabled $false`를 실행합니다.
 ![동기화 스케줄러를 사용하지 않도록 설정하는 PowerShell 명령](media/how-to-connect-fix-default-rules/default3.png)

- 범위 지정 필터의 변경으로 인해 대상 디렉터리에서 개체가 삭제될 수 있습니다. 개체의 범위를 변경하기 전에 주의해야 합니다. 활성 서버를 변경하기 전에 준비 서버를 변경하는 것이 좋습니다.
- 새 규칙을 추가한 후 [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션의 설명에 따라 단일 개체에서 미리 보기를 실행합니다.
- 새 규칙을 추가하거나 사용자 지정 동기화 규칙을 수정한 후 전체 동기화를 실행합니다. 이 동기화는 모든 개체에 새 규칙을 적용합니다.

## <a name="change-attribute-flow"></a>특성 흐름 변경
특성 흐름을 변경하는 세 가지 시나리오가 있습니다.
- 새 특성 추가
- 기존 특성 값 재정의
- 기존 특성을 동기화하지 않도록 선택

표준 기본 규칙을 변경하지 않고 이들 작업을 수행할 수 있습니다.

### <a name="add-a-new-attribute"></a>새 특성 추가
특성이 원본 디렉터리에서 대상 디렉터리로 흐르지 않는 경우 [Azure AD Connect 동기화: 디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 사용하여 이 문제를 해결합니다.

확장이 작동하지 않는 경우 다음 섹션에서 설명하는 2개의 새 동기화 규칙을 추가해봅니다.


#### <a name="add-an-inbound-sync-rule"></a>인바운드 동기화 규칙 추가
인바운드 동기화 규칙은 특성의 소스가 커넥터 공간이고 대상이 메타버스임을 의미합니다. 예를 들어 온-프레미스 Active Directory에서 Azure Active Directory로 새 특성이 흐르도록 하려면 새 인바운드 동기화 규칙을 만듭니다. **동기화 규칙 편집기** 를 시작하고 방향으로 **인바운드** 를 선택하고 **새 규칙 추가** 를 선택합니다. 

 !["인바운드" 및 "새 규칙 추가"가 선택된 "동기화 규칙 편집기"를 보여주는 스크린샷](media/how-to-connect-fix-default-rules/default3a.png)

사용자 고유의 명명 규칙에 따라 규칙의 이름을 지정합니다. 여기서는 **Custom In from AD - User** 를 사용합니다. 이는 규칙이 사용자 지정 규칙이며 Active Directory 커넥터 공간에서 메타버스로의 인바운드 규칙임을 의미합니다.   

 ![인바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3b.png)

나중에 규칙을 쉽게 유지 관리할 수 있도록 규칙에 대한 설명을 제공합니다. 예를 들어 설명은 규칙의 목적과 필요한 이유를 기반으로 할 수 있습니다.

**연결된 시스템**, **연결된 시스템 개체 유형** 및 **메타버스 개체 유형** 필드를 선택합니다.

0에서 99까지의 우선 순위 값을 지정합니다(숫자가 낮을수록 우선 순위가 높음). **태그**, **암호 동기화 사용** 및 **사용 안 함** 필드의 경우 기본 선택 항목을 사용합니다.

**범위 지정 필터** 는 비워 둡니다. 그러면 규칙이 Active Directory 연결된 시스템과 메타버스 사이에 조인된 모든 개체에 적용됩니다.

**조인 규칙** 을 비워 둡니다. 이는 이 규칙이 표준 기본 규칙에 정의된 조인 조건을 사용함을 의미합니다. 이는 표준 기본 규칙을 사용하지 않도록 설정 또는 삭제하지 않는 또 다른 이유입니다. 조인 조건이 없으면 특성이 흐르지 않습니다. 

특성에 대한 적절한 변환을 추가합니다. 상수를 할당하여 상수 값이 대상 속성으로 흐르도록 할 수 있습니다. 원본 또는 대상 특성 간에 직접 매핑을 사용할 수 있습니다. 또는 특성에 식을 사용할 수 있습니다. 다음과 같은 다양한 [식 함수](./reference-connect-sync-functions-reference.md)를 사용할 수 있습니다.

#### <a name="add-an-outbound-sync-rule"></a>아웃바운드 동기화 규칙 추가
특성을 대상 디렉터리에 연결하려면 아웃바운드 규칙을 만들어야 합니다. 즉, 원본은 메타버스이고 대상은 연결된 시스템입니다. 아웃바운드 규칙을 만들려면 **동기화 규칙 편집기** 를 시작하고, **방향** 을 **아웃바운드** 로 변경하고, **새 규칙 추가** 를 선택합니다. 

![동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default3c.png)

인바운드 규칙과 마찬가지로 자신만의 명명 규칙을 사용하여 규칙의 이름을 지정할 수 있습니다. Azure AD 테넌트로 **연결된 시스템** 을 선택하고 특성 값을 설정하려는 연결된 시스템 개체를 선택합니다. 0~99의 우선 순위를 설정합니다. 

![아웃바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3d.png)

**범위 지정 필터** 와 **조인 규칙** 을 비워 둡니다. 상수, 직접 또는 식으로 변환을 채웁니다. 

Active Directory에서 Azure Active Directory로 사용자 개체 흐름에 대한 새 특성을 만드는 방법을 배웠습니다. 이러한 단계를 사용하여 개체의 특성을 원본과 대상에 매핑할 수 있습니다. 자세한 내용은 [사용자 지정 동기화 규칙 만들기](how-to-connect-create-custom-sync-rule.md) 및 [사용자 프로비전 준비](/office365/enterprise/prepare-for-directory-synchronization)를 참조하세요.

### <a name="override-the-value-of-an-existing-attribute"></a>기존 특성 값 재정의
이미 매핑된 특성의 값을 재정의할 수 있습니다. 예를 들어 항상 Azure AD의 특성에 Null 값을 설정하려면 인바운드 규칙만 만들면 됩니다. 상수 값, `AuthoritativeNull`이 대상 특성으로 흐르도록 합니다. 

>[!NOTE] 
> 이 경우 `Null` 대신 `AuthoritativeNull`을 사용합니다. 이는 Null이 아닌 값이 우선 순위가 낮더라도(규칙에서 더 높은 숫자 값) Null 값을 대체하기 때문입니다. 반면 `AuthoritativeNull`은 다른 규칙에 의해 Null이 아닌 값으로 대체되지 않습니다. 

### <a name="dont-sync-existing-attribute"></a>기존 특성 동기화 안 함
동기화에서 특성을 제외하려는 경우 Azure AD Connect에서 제공하는 특성 필터링 기능을 사용합니다. 바탕 화면 아이콘에서 **Azure AD Connect** 를 시작하고 **동기화 옵션 사용자 지정** 을 선택합니다.

![Azure AD Connect 추가 작업 옵션](media/how-to-connect-fix-default-rules/default4.png)

 **Azure AD 앱 및 특성 필터링** 이 선택되어 있는지 확인하고 **다음** 을 선택합니다.

![Azure AD Connect 옵션 기능](media/how-to-connect-fix-default-rules/default5.png)

동기화에서 제외하려는 특성을 지웁니다.

![Azure AD Connect 특성](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>범위 지정 필터 변경
Azure AD Sync는 대부분의 개체를 처리합니다. 표준 기본 동기화 규칙을 변경하지 않고 개체의 범위와 내보낼 개체 수를 줄일 수 있습니다. 

다음 방법 중 하나를 사용하여 동기화 중인 개체의 범위를 줄입니다.

- cloudFiltered 특성
- 조직 구성 단위 필터링

동기화되는 사용자의 범위를 줄이면 필터링된 사용자에 대해서도 암호 해시 동기화가 중지됩니다. 개체가 이미 동기화 중인 경우 범위를 줄이면 필터링된 개체가 대상 디렉터리에서 삭제됩니다. 따라서 매우 신중하게 범위를 지정해야 합니다.

>[!IMPORTANT] 
> Azure AD Connect에서 구성한 개체의 범위를 늘리지 않는 것이 좋습니다. 개체의 범위를 늘리면 Microsoft 지원 팀에서 사용자 지정을 이해하기 어렵습니다. 개체의 범위를 늘려야 하는 경우 기존 규칙을 편집하고 복제한 다음, 원래 규칙을 사용하지 않도록 설정합니다. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 특성
Active Directory에서는 이 특성을 설정할 수 없습니다. 새 인바운드 규칙을 추가하여 이 특성의 값을 설정합니다. 그런 다음, **변환** 및 **식** 을 사용하여 메타버스에서 이 특성을 설정할 수 있습니다. 다음 예제에서는 부서 이름이 **HRD**(대/소문자 구분 안 함)로 시작하는 모든 사용자를 동기화하지 않으려고 합니다.

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

먼저 부서를 원본(Active Directory)에서 소문자로 변환했습니다. 그런 다음, `Left` 함수를 사용하여 처음 세 문자만 가져와 `hrd`와 비교했습니다. 일치하면 값이 `True`로 설정되고, 그렇지 않으면 `NULL`로 설정됩니다. 값을 Null로 설정하면 우선 순위가 낮은(더 높은 숫자 값) 다른 규칙이 다른 조건으로 이 값에 쓸 수 있습니다. [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션에서 설명한 대로 한 개체에 대해 미리 보기를 실행하여 동기화 규칙의 유효성을 검사합니다.

![인바운드 동기화 규칙 옵션 만들기](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>조직 구성 단위 필터링
하나 이상의 OU(조직 구성 단위)를 만들고, 동기화하지 않으려는 개체를 이러한 OU로 옮길 수 있습니다. 그런 다음, Azure AD Connect에서 OU 필터링을 구성합니다. 바탕 화면 아이콘에서 **Azure AD Connect** 를 시작하고 다음 옵션을 선택합니다. Azure AD Connect를 설치할 때 OU 필터링을 구성할 수도 있습니다. 

![Azure AD Connect 추가 작업](media/how-to-connect-fix-default-rules/default8.png)

마법사의 지침에 따라 동기화하지 않으려는 OU 선택을 취소합니다.

![Azure AD Connect 도메인 및 OU 필터링 옵션](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>조인 조건 변경
Azure AD Connect에 의해 구성된 기본 조인 조건을 사용 합니다. 기본 조인 조건을 변경하면 Microsoft 고객 지원팀에서 사용자 지정을 이해하고 제품을 지원하기 어려워집니다.

## <a name="validate-sync-rule"></a>동기화 규칙 유효성 검사
전체 동기화 주기를 실행하지 않고 미리 보기 기능을 사용하여 새로 추가된 동기화 규칙의 유효성을 검사할 수 있습니다. Azure AD Connect에서 **동기화 서비스** 를 선택합니다.

![동기화 서비스가 강조 표시된 Azure AD Connect](media/how-to-connect-fix-default-rules/default10.png)

**메타버스 검색** 을 선택합니다. 범위 개체로 **Person(사람)** 을 선택하고, **절 추가** 를 선택하고, 검색 조건을 지정합니다. 그런 다음, **검색** 을 선택하고 검색 결과에서 개체를 두 번 클릭합니다. 이 단계를 실행하기 전에 포리스트에서 가져오기 및 동기화를 실행하여 Azure AD Connect 데이터가 해당 개체에 대해 최신인지 확인합니다.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

**메타버스 개체 속성** 에서 **커넥터** 를 선택하고 해당 커넥터(포리스트)에서 개체를 선택한 다음, **속성...** 을 선택합니다.

![메타버스 개체 속성](media/how-to-connect-fix-default-rules/default12.png)

**미리 보기…** 를 선택합니다.

![커넥터 공간 개체 속성](media/how-to-connect-fix-default-rules/default13a.png)

미리 보기 창의 왼쪽 창에서 **Generate Preview(미리 보기 생성)** 와 **가져오기 특성 흐름** 을 선택합니다.

!["가져오기 특성 흐름" 및 "Generate Preview(미리 보기 생성)"가 선택된 "미리 보기" 창을 보여주는 스크린샷](media/how-to-connect-fix-default-rules/default14.png)
 
여기에서 새로 추가된 규칙이 개체에서 실행되고 `cloudFiltered` 특성을 true로 설정했음을 확인할 수 있습니다.

![미리 보기](media/how-to-connect-fix-default-rules/default15a.png)
 
수정된 규칙을 기본 규칙과 비교하려면 두 규칙을 모두 개별적으로 텍스트 파일로 내보냅니다. 이러한 규칙은 PowerShell 스크립트 파일로 내보내집니다. 파일 비교 도구(예: windiff)를 사용하여 변경 내용을 확인하는 방식으로 파일을 비교할 수 있습니다. 
 
수정된 규칙에서 `msExchMailboxGuid` 특성이 **직접** 대신 **식** 형식으로 변경되었음을 확인할 수 있습니다. 또한 값이 **NULL** 및 **ExecuteOnce** 옵션으로 변경되었습니다. 식별 및 우선 순위 차이는 무시할 수 있습니다. 

![windiff 도구 출력](media/how-to-connect-fix-default-rules/default17.png)
 
규칙을 다시 기본 설정으로 변경하도록 고치려면 수정된 규칙을 삭제하고 기본 규칙을 사용하도록 설정합니다. 수행하려는 사용자 지정이 손실되지 않도록 합니다. 준비가 되면 **전체 동기화** 를 실행합니다.

## <a name="next-steps"></a>다음 단계
- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)