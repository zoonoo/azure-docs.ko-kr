---
title: 수정 된 기본 규칙을 수정 하는 방법-Azure AD Connect | Microsoft Docs
description: Azure AD Connect와 함께 제공 되는 수정 된 기본 규칙을 수정 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320050"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect에서 수정 된 기본 규칙 수정

Azure AD (Azure Active Directory) 연결은 동기화에 기본 규칙을 사용 합니다.  아쉽게도 이러한 규칙은 모든 조직에 전체적으로 적용 되지 않습니다. 요구 사항에 따라 수정 해야 할 수도 있습니다. 이 문서에서는 가장 일반적인 사용자 지정의 두 가지 예제를 설명 하 고 이러한 사용자 지정을 수행 하는 올바른 방법을 설명 합니다.

>[!NOTE] 
> 필요한 사용자 지정을 얻기 위해 기존 기본 규칙을 수정 하는 것은 지원 되지 않습니다. 이렇게 하면 이후 릴리스에서 이러한 규칙을 최신 버전으로 업데이트할 수 없습니다. 필요한 버그 수정 이나 새로운 기능을 얻을 수 없습니다. 이 문서에서는 기존 기본 규칙을 수정 하지 않고 동일한 결과를 얻는 방법을 설명 합니다. 

## <a name="how-to-identify-modified-default-rules"></a>수정 된 기본 규칙을 확인 하는 방법
Azure AD Connect 버전 1.3.7.0부터 수정 된 기본 규칙을 쉽게 식별할 수 있습니다. **바탕 화면에서 앱**으로 이동 하 고 **동기화 규칙 편집기**를 선택 합니다.

![동기화 규칙 편집기가 강조 표시 된 Azure AD Connect](media/how-to-connect-fix-default-rules/default1.png)

편집기에서 수정 된 기본 규칙은 이름 앞에 경고 아이콘이 표시 됩니다.

![경고 아이콘](media/how-to-connect-fix-default-rules/default2.png)

 옆에 동일한 이름을 가진 비활성화 된 규칙이 나타납니다 (표준 기본 규칙).

![표준 기본 규칙 및 수정 된 기본 규칙을 표시 하는 동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>일반 사용자 지정
기본 규칙에 대 한 일반적인 사용자 지정은 다음과 같습니다.

- 특성 흐름 변경
- 범위 지정 필터 변경
- 조인 조건 변경

규칙을 변경 하기 전에 다음을 수행 합니다.

- 동기화 스케줄러를 사용 하지 않도록 설정 합니다. 스케줄러 는 기본적으로 30분마다 실행됩니다. 변경 하 고 새 규칙의 문제를 해결 하는 동안 아직 시작 되지 않았는지 확인 합니다. 스케줄러를 일시적으로 사용 하지 않도록 설정 하려면 PowerShell을 시작 하 고를 실행 `Set-ADSyncScheduler -SyncCycleEnabled $false` 합니다.
 ![동기화 스케줄러를 사용 하지 않도록 설정 하는 PowerShell 명령](media/how-to-connect-fix-default-rules/default3.png)

- 범위 지정 필터의 변경으로 인해 대상 디렉터리에서 개체가 삭제 될 수 있습니다. 개체의 범위를 변경 하기 전에 주의 해야 합니다. 활성 서버를 변경 하기 전에 준비 서버를 변경 하는 것이 좋습니다.
- 새 규칙을 추가한 후 [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션에서 설명한 대로 단일 개체에서 미리 보기를 실행 합니다.
- 새 규칙을 추가 하거나 사용자 지정 동기화 규칙을 수정한 후 전체 동기화를 실행 합니다. 이 동기화는 모든 개체에 새 규칙을 적용 합니다.

## <a name="change-attribute-flow"></a>특성 흐름 변경
특성 흐름을 변경 하는 세 가지 시나리오는 다음과 같습니다.
- 새 특성을 추가 합니다.
- 기존 특성의 값을 재정의 합니다.
- 기존 특성을 동기화 하지 않도록 선택 합니다.

표준 기본 규칙을 변경 하지 않고이 작업을 수행할 수 있습니다.

### <a name="add-a-new-attribute"></a>새 특성 추가
특성이 원본 디렉터리에서 대상 디렉터리로 전달 되지 않는 경우 [Azure AD Connect sync: 디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md) 을 사용 하 여이 문제를 해결 합니다.

확장이 작동 하지 않는 경우 다음 섹션에서 설명 하는 두 개의 새 동기화 규칙을 추가 해 보세요.


#### <a name="add-an-inbound-sync-rule"></a>인바운드 동기화 규칙 추가
인바운드 동기화 규칙은 특성의 소스가 커넥터 공간 임을 의미 하 고 대상은 메타 버스입니다. 예를 들어 온-프레미스 Active Directory에서 Azure Active Directory로 새 특성 흐름을 설정 하려면 새 인바운드 동기화 규칙을 만듭니다. **동기화 규칙 편집기**를 시작 하 고 방향으로 **인바운드** 를 선택 하 고 **새 규칙 추가**를 선택 합니다. 

 !["인바운드" 및 "새 규칙 추가"가 선택 된 "동기화 규칙 편집기"를 보여 주는 스크린샷](media/how-to-connect-fix-default-rules/default3a.png)

고유한 명명 규칙에 따라 규칙의 이름을 지정 합니다. 여기서는 **AD 사용자의에서 사용자 지정**을 사용 합니다. 즉, 규칙은 사용자 지정 규칙 이며 Active Directory 커넥터 공간에서 메타 버스 까지의 인바운드 규칙입니다.   

 ![인바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3b.png)

규칙에 대 한 고유한 설명을 제공 하 여 나중에 규칙을 쉽게 유지 관리할 수 있도록 합니다. 예를 들어 설명은 규칙의 목표와 필요한 이유를 기준으로 할 수 있습니다.

**연결 된 시스템**, **연결 된 시스템 개체 유형**및 **메타 버스 개체 유형** 필드를 선택 합니다.

0에서 99 사이의 우선 순위 값을 지정 합니다. 숫자가 낮을수록 우선 순위가 높습니다. **태그**에 대해 **암호 동기화를 사용 하도록 설정**하 고 사용 **하지 않도록** 설정 된 필드에는 기본 선택 항목을 사용 합니다.

**범위 지정 필터** 를 비워 둡니다. 즉, Active Directory 연결 된 시스템과 메타 버스 간에 조인 된 모든 개체에 규칙이 적용 됩니다.

**조인 규칙** 을 비워 둡니다. 즉,이 규칙은 표준 기본 규칙에 정의 된 join 조건을 사용 합니다. 이는 표준 기본 규칙을 사용 하지 않도록 설정 하거나 삭제 하지 않는 또 다른 이유입니다. 조인 조건이 없는 경우 특성은 전달 되지 않습니다. 

특성에 대 한 적절 한 변환을 추가 합니다. 상수를 할당 하 여 상수 값을 대상 특성으로 흐르게 할 수 있습니다. 원본 또는 대상 특성 간의 직접 매핑을 사용할 수 있습니다. 또는 특성에 식을 사용할 수 있습니다. 사용할 수 있는 다양 한 [식 함수](./reference-connect-sync-functions-reference.md) 는 다음과 같습니다.

#### <a name="add-an-outbound-sync-rule"></a>아웃 바운드 동기화 규칙 추가
특성을 대상 디렉터리에 연결 하려면 아웃 바운드 규칙을 만들어야 합니다. 이는 원본이 메타 버스이 고 대상이 연결 된 시스템 임을 의미 합니다. 아웃 바운드 규칙을 만들려면 **동기화 규칙 편집기**를 시작 하 고, **방향을** **아웃 바운드**로 변경 하 고, **새 규칙 추가**를 선택 합니다. 

![동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default3c.png)

인바운드 규칙과 마찬가지로 고유한 명명 규칙을 사용 하 여 규칙의 이름을 지정할 수 있습니다. Azure AD 테 넌 트로 **연결 된 시스템** 을 선택 하 고 특성 값을 설정 하려는 연결 된 시스템 개체를 선택 합니다. 우선 순위를 0에서 99으로 설정 합니다. 

![아웃바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3d.png)

**범위 지정 필터** 및 **조인 규칙** 을 비워 둡니다. 상수, 직접 또는 식으로 변환을 채웁니다. 

이제 Active Directory에서 Azure Active Directory으로 사용자 개체 흐름에 대 한 새 특성을 만드는 방법을 알고 있습니다. 이러한 단계를 사용 하 여 모든 개체의 특성을 원본 및 대상에 매핑할 수 있습니다. 자세한 내용은 사용자 [지정 동기화 규칙 만들기](how-to-connect-create-custom-sync-rule.md) 및 [사용자 프로 비전 준비를](/office365/enterprise/prepare-for-directory-synchronization)참조 하세요.

### <a name="override-the-value-of-an-existing-attribute"></a>기존 특성의 값을 재정의 합니다.
이미 매핑된 특성의 값을 재정의할 수 있습니다. 예를 들어 항상 Azure AD의 특성에 null 값을 설정 하려면 인바운드 규칙만 만들면 됩니다. 상수 값을 `AuthoritativeNull` 대상 특성으로 이동 합니다. 

>[!NOTE] 
> `AuthoritativeNull`이 경우 대신를 사용 `Null` 합니다. 이는 null이 아닌 값이 더 낮은 우선 순위 (규칙의 더 높은 숫자 값)를 포함 하는 경우에도 null 값을 대체 하기 때문입니다. `AuthoritativeNull`반면, 다른 규칙에서 null이 아닌 값으로 대체 되지 않습니다. 

### <a name="dont-sync-existing-attribute"></a>기존 특성을 동기화 하지 않음
동기화에서 특성을 제외 하려는 경우 Azure AD Connect에서 제공 하는 특성 필터링 기능을 사용 합니다. 바탕 화면 아이콘에서 **Azure AD Connect** 를 시작 하 고 **동기화 옵션 사용자 지정**을 선택 합니다.

![추가 작업 옵션 Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 **AZURE AD 앱 및 특성 필터링** 이 선택 되어 있는지 확인 하 고 **다음**을 선택 합니다.

![선택적 기능 Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

동기화에서 제외 하려는 특성을 선택 취소 합니다.

![Azure AD Connect 특성](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>범위 지정 필터 변경
Azure AD Sync 대부분의 개체를 처리 합니다. 표준 기본 동기화 규칙을 변경 하지 않고 개체의 범위를 줄이고 내보낼 개체의 수를 줄일 수 있습니다. 

다음 방법 중 하나를 사용 하 여 동기화 할 개체의 범위를 줄입니다.

- cloudFiltered 된 특성
- 조직 구성 단위 필터링

동기화 중인 사용자의 범위를 줄이면 필터링 된 사용자에 대 한 암호 해시 동기화도 중지 됩니다. 개체가 이미 동기화 중인 경우 범위를 줄인 후에는 필터링 된 개체가 대상 디렉터리에서 삭제 됩니다. 따라서 범위를 신중 하 게 해야 합니다.

>[!IMPORTANT] 
> Azure AD Connect에서 구성 된 개체의 범위를 늘리지 않는 것이 좋습니다. 이렇게 하면 Microsoft 지원 팀이 사용자 지정을 이해 하기 어려워집니다. 개체의 범위를 늘려야 하는 경우 기존 규칙을 편집 하 고 복제 한 다음 원래 규칙을 사용 하지 않도록 설정 합니다. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 된 특성
Active Directory에서는이 특성을 설정할 수 없습니다. 새 인바운드 규칙을 추가 하 여이 특성의 값을 설정 합니다. 그런 다음 **변환** 및 **식을** 사용 하 여 메타 버스에서이 특성을 설정할 수 있습니다. 다음 예에서는 부서 이름이 **Hrd** (대/소문자 구분 안 함)으로 시작 하는 모든 사용자를 동기화 하지 않으려는 경우를 보여 줍니다.

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

먼저 부서를 원본 (Active Directory)에서 소문자로 변환 했습니다. 그런 다음 함수를 사용 하 여 `Left` 처음 세 자만 사용 하 고와 비교 했습니다 `hrd` . 일치 하면 값은로 설정 되 고 `True` , 그렇지 않으면로 설정 됩니다 `NULL` . 값을 null로 설정 하는 경우 우선 순위가 낮은 다른 규칙 (더 높은 숫자 값)은 다른 조건을 사용 하 여 쓸 수 있습니다. [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션에서 설명한 대로 한 개체에 대해 미리 보기를 실행 하 여 동기화 규칙의 유효성을 검사 합니다.

![인바운드 동기화 규칙 옵션 만들기](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>조직 구성 단위 필터링
하나 이상의 Ou (조직 구성 단위)를 만들고 동기화 하지 않으려는 개체를 이러한 Ou로 이동할 수 있습니다. 그런 다음 Azure AD Connect에서 OU 필터링을 구성 합니다. 바탕 화면 아이콘에서 **Azure AD Connect** 를 시작 하 고 다음 옵션을 선택 합니다. Azure AD Connect를 설치할 때 OU 필터링을 구성할 수도 있습니다. 

![추가 작업 Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

마법사의 지침에 따라 동기화 하지 않을 Ou를 선택 취소 합니다.

![Azure AD Connect 도메인 및 OU 필터링 옵션](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>조인 조건 변경
Azure AD Connect으로 구성 된 기본 조인 조건을 사용 합니다. 기본 조인 조건을 변경 하면 Microsoft 지원에서 사용자 지정을 이해 하 고 제품을 지 원하는 것이 어려워집니다.

## <a name="validate-sync-rule"></a>동기화 규칙 유효성 검사
전체 동기화 주기를 실행 하지 않고 미리 보기 기능을 사용 하 여 새로 추가 된 동기화 규칙의 유효성을 검사할 수 있습니다. Azure AD Connect에서 **동기화 서비스**를 선택 합니다.

![Azure AD Connect (동기화 서비스가 강조 표시 됨)](media/how-to-connect-fix-default-rules/default10.png)

**메타 버스 검색**을 선택 합니다. **사용자**로 범위 개체를 선택 하 고, **절 추가**를 선택 하 고, 검색 조건을 언급 합니다. 그런 다음 **검색**을 선택 하 고 검색 결과에서 개체를 두 번 클릭 합니다. 이 단계를 실행 하기 전에 포리스트에서 가져오기 및 동기화를 실행 하 여 Azure AD Connect의 데이터가 해당 개체에 대해 최신 상태 인지 확인 합니다.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

**메타 버스 개체 속성**에서 **커넥터**를 선택 하 고 해당 커넥터 (포리스트)에서 개체를 선택한 다음 **속성 ...** 을 선택 합니다.

![메타버스 개체 속성](media/how-to-connect-fix-default-rules/default12.png)

**미리 보기** 선택 ...

![커넥터 공간 개체 속성](media/how-to-connect-fix-default-rules/default13a.png)

미리 보기 창의 왼쪽 창에서 **미리 보기 생성** 및 **특성 흐름 가져오기** 를 선택 합니다.

!["특성 흐름 가져오기" 및 "미리 보기 생성"이 선택 된 "미리 보기" 창을 보여 주는 스크린샷](media/how-to-connect-fix-default-rules/default14.png)
 
여기서는 새로 추가 된 규칙이 개체에 대해 실행 되 고 특성을 true로 설정 했는지 확인 합니다 `cloudFiltered` .

![미리 보기](media/how-to-connect-fix-default-rules/default15a.png)
 
수정 된 규칙을 기본 규칙과 비교 하려면 두 규칙을 모두 개별적으로 텍스트 파일로 내보냅니다. 이러한 규칙을 PowerShell 스크립트 파일로 내보냅니다. 파일 비교 도구 (예: windiff)를 사용 하 여 변경 내용을 확인 하 여 비교할 수 있습니다. 
 
수정 된 규칙에서 `msExchMailboxGuid` 특성은 **직접**이 아닌 **식** 형식으로 변경 됩니다. 또한 값은 **NULL** 및 **executeonce** 옵션으로 변경 됩니다. 식별 된 우선 순위와 우선 순위 차이를 무시할 수 있습니다. 

![windiff 도구 출력](media/how-to-connect-fix-default-rules/default17.png)
 
규칙을 다시 기본 설정으로 변경 하는 규칙을 수정 하려면 수정 된 규칙을 삭제 하 고 기본 규칙을 사용 하도록 설정 합니다. 구현 하려는 사용자 지정이 손실 되지 않도록 합니다. 준비가 되 면 **전체 동기화**를 실행 합니다.

## <a name="next-steps"></a>다음 단계
- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)