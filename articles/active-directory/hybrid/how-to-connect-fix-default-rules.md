---
title: 수정된 기본 규칙을 수정하는 방법 - Azure AD Connect | 마이크로 소프트 문서
description: Azure AD Connect와 함께 제공되는 수정된 기본 규칙을 수정하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036985"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD 연결에서 수정된 기본 규칙 수정

Azure Active Directory(Azure AD) 연결은 동기화를 위해 기본 규칙을 사용합니다.  안타깝게도 이러한 규칙은 모든 조직에 보편적으로 적용되지는 않습니다. 요구 사항에 따라 수정해야 할 수 있습니다. 이 문서에서는 가장 일반적인 사용자 지정의 두 가지 예제에 대해 설명하고 이러한 사용자 지정을 달성하는 올바른 방법을 설명합니다.

>[!NOTE] 
> 필요한 사용자 지정을 달성하기 위해 기존 기본 규칙을 수정하는 것은 지원되지 않습니다. 이렇게 하면 이후 릴리스의 최신 버전으로 이러한 규칙을 업데이트할 수 없습니다. 필요한 버그 수정 또는 새로운 기능을 얻을 수 없습니다. 이 문서에서는 기존 기본 규칙을 수정하지 않고 동일한 결과를 얻는 방법을 설명합니다. 

## <a name="how-to-identify-modified-default-rules"></a>수정된 기본 규칙을 식별하는 방법
Azure AD Connect의 버전 1.3.7.0부터 수정된 기본 규칙을 쉽게 식별할 수 있습니다. **데스크톱의 앱으로**이동하여 **동기화 규칙 편집기를**선택합니다.

![동기화 규칙 편집기 강조 표시된 Azure AD 연결](media/how-to-connect-fix-default-rules/default1.png)

편집기에서 수정된 기본 규칙은 이름 앞에 경고 아이콘으로 표시됩니다.

![경고 아이콘](media/how-to-connect-fix-default-rules/default2.png)

 옆에 이름이 같은 비활성화 된 규칙도 나타납니다 (표준 기본 규칙입니다).

![표준 기본 규칙 및 수정된 기본 규칙을 표시하는 동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>일반적인 사용자 지정
다음은 기본 규칙에 대한 일반적인 사용자 지정입니다.

- 특성 흐름 변경
- 범위 지정 필터 변경
- 조인 조건 변경

규칙을 변경하기 전에 다음을 수행합니다.

- 동기화 스케줄러를 사용하지 않도록 설정합니다. 스케줄러 는 기본적으로 30분마다 실행됩니다. 새 규칙을 변경하고 문제를 해결하는 동안 시작되지 않았는지 확인합니다. 스케줄러를 일시적으로 비활성화하려면 PowerShell을 `Set-ADSyncScheduler -SyncCycleEnabled $false`시작하고 실행합니다.
 ![PowerShell 명령으로 동기화 스케줄러를 사용하지 않도록 설정합니다.](media/how-to-connect-fix-default-rules/default3.png)

- 범위 지정 필터가 변경되면 대상 디렉터리에서 개체가 삭제될 수 있습니다. 개체 의 범위 지정을 변경하기 전에 주의하십시오. 활성 서버를 변경하기 전에 스테이징 서버를 변경하는 것이 좋습니다.
- 새 규칙을 추가한 후 [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션에서 설명한 대로 단일 개체에서 미리 보기를 실행합니다.
- 새 규칙을 추가하거나 사용자 지정 동기화 규칙을 수정한 후 전체 동기화를 실행합니다. 이 동기화는 모든 개체에 새 규칙을 적용합니다.

## <a name="change-attribute-flow"></a>특성 흐름 변경
특성 흐름을 변경하기 위한 세 가지 시나리오가 있습니다.
- 새 특성 추가.
- 기존 특성의 값을 재정의합니다.
- 기존 특성을 동기화하지 않도록 선택합니다.

표준 기본 규칙을 변경하지 않고 이러한 작업을 수행할 수 있습니다.

### <a name="add-a-new-attribute"></a>새 특성 추가
특성이 원본 디렉터리에서 대상 디렉터리로 흐르지 않는 경우 [Azure AD Connect 동기화: 디렉터리 확장을](how-to-connect-sync-feature-directory-extensions.md) 사용하여 이 문제를 해결합니다.

확장이 작동하지 않는 경우 다음 섹션에 설명된 두 개의 새 동기화 규칙을 추가해 보세요.


#### <a name="add-an-inbound-sync-rule"></a>인바운드 동기화 규칙 추가
인바운드 동기화 규칙은 특성의 소스가 커넥터 공간이고 대상이 메타버스임을 의미합니다. 예를 들어 온-프레미스 Active Directory에서 Azure Active Directory로 새 특성 흐름을 만들려면 새 인바운드 동기화 규칙을 만듭니다. 동기화 **규칙 편집기를**시작하고 **인바운드를** 방향으로 선택하고 **새 규칙 추가를**선택합니다. 

 ![동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default3a.png)

고유한 명명 규칙을 따라 규칙의 이름을 지정합니다. 여기에서는 **AD - 사용자 에서 사용자 지정 을**사용합니다. 즉, 규칙은 사용자 지정 규칙이며 Active Directory 커넥터 공간에서 메타버스까지의 인바운드 규칙입니다.   

 ![인바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3b.png)

나중에 규칙의 유지 관리가 쉬워지므로 규칙에 대한 고유한 설명을 제공합니다. 예를 들어 설명은 규칙의 목적이 무엇이며 왜 필요한지에 따라 설명될 수 있습니다.

**연결된 시스템,** 연결된 시스템 **개체 유형**및 **메타버스 개체 유형** 필드에 대해 선택합니다.

우선 순위 값을 0부터 99까지 지정합니다(숫자가 낮을수록 우선 순위가 높함). **태그**, **암호 동기화 활성화**및 **비활성화필드의** 경우 기본 선택을 사용합니다.

범위 지정 필터를 비워 **두십시오.** 즉, 이 규칙은 Active Directory 연결 시스템과 메타버스 사이에 결합된 모든 개체에 적용됩니다.

**조인 규칙을** 비워 두십시오. 즉, 이 규칙은 표준 기본 규칙에 정의된 조인 조건을 사용합니다. 이는 표준 기본 규칙을 사용하지 않도록 설정하거나 삭제하지 않는 또 다른 이유입니다. 조인 조건이 없는 경우 특성이 흐르지 않습니다. 

특성에 적합한 변환을 추가합니다. 상수를 할당하여 대상 특성에 상수 값 흐름을 만들 수 있습니다. 소스 또는 대상 특성 간에 직접 매핑을 사용할 수 있습니다. 또는 특성에 대한 식을 사용할 수 있습니다. 다음은 사용할 수 있는 다양한 [표현식 함수입니다.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)

#### <a name="add-an-outbound-sync-rule"></a>아웃바운드 동기화 규칙 추가
특성을 대상 디렉터리에 연결하려면 아웃바운드 규칙을 만들어야 합니다. 즉, 소스는 메타버스이고 대상은 연결된 시스템입니다. 아웃바운드 규칙을 만들려면 **동기화 규칙 편집기를**시작하고 **아웃바운드로** **방향을** 변경하고 **새 규칙 추가를**선택합니다. 

![동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default3c.png)

인바운드 규칙과 마찬가지로 고유한 이름 지정 규칙을 사용하여 규칙의 이름을 지정할 수 있습니다. 연결된 **시스템을** Azure AD 테넌트로 선택하고 특성 값을 설정할 연결된 시스템 개체를 선택합니다. 우선 순위를 0에서 99까지 설정합니다. 

![아웃바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3d.png)

**범위 지정 필터** 및 **조인 규칙을** 비워 두십시오. 변환을 상수, 직접 또는 식으로 채웁니다. 

이제 Active Directory에서 Azure Active Directory로 사용자 개체 흐름에 대한 새 특성을 만드는 방법을 알 수 있습니다. 이러한 단계를 사용하여 모든 개체의 특성을 원본 및 대상으로 매핑할 수 있습니다. 자세한 내용은 [사용자 지정 동기화 규칙 만들기](how-to-connect-create-custom-sync-rule.md) 및 사용자 [프로비전 준비를](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)참조하십시오.

### <a name="override-the-value-of-an-existing-attribute"></a>기존 특성의 값 재정의
이미 매핑된 특성의 값을 재정의할 수 있습니다. 예를 들어 Azure AD의 특성에 null 값을 항상 설정하려는 경우 인바운드 규칙만 만 만듭니다. 대상 특성에 `AuthoritativeNull`대한 상수 값 , " 흐름을 확인합니다. 

>[!NOTE] 
> 이 `AuthoritativeNull` 경우 `Null` 대신 사용합니다. 이는 null이 아닌 값이 우선 순위가 낮은 경우에도 null 값을 대체하기 때문입니다(규칙에서 숫자 값이 높음). `AuthoritativeNull`반면에 다른 규칙에 의해 null이 아닌 값으로 대체되지 않습니다. 

### <a name="dont-sync-existing-attribute"></a>기존 특성을 동기화하지 마십시오.
특성을 동기화에서 제외하려면 Azure AD Connect에 제공된 특성 필터링 기능을 사용합니다. 데스크톱 아이콘에서 **Azure AD 연결을** 실행한 다음 **동기화 사용자 지정 옵션을**선택합니다.

![Azure AD 추가 작업 옵션 연결](media/how-to-connect-fix-default-rules/default4.png)

 Azure **AD 앱 및 특성 필터링이** 선택되어 있는지 확인하고 다음 을 **선택합니다.**

![Azure AD 연결 옵션 기능](media/how-to-connect-fix-default-rules/default5.png)

동기화에서 제외할 특성을 지웁울 수 있습니다.

![Azure AD 연결 특성](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>범위 지정 필터 변경
Azure AD Sync는 대부분의 개체를 처리합니다. 표준 기본 동기화 규칙을 변경하지 않고 개체 의 범위를 줄이고 내보낼 개체 수를 줄일 수 있습니다. 

다음 방법 중 하나를 사용하여 동기화하는 개체의 범위를 줄입니다.

- 클라우드필터링 속성
- 조직 단위 필터링

동기화되는 사용자의 범위를 줄이면 필터링된 사용자에 대한 암호 해시 동기화도 중지됩니다. 개체가 이미 동기화중인 경우 범위를 줄인 후 필터링된 개체가 대상 디렉터리에서 삭제됩니다. 따라서 매우 신중하게 범위를 정해야 합니다.

>[!IMPORTANT] 
> Azure AD Connect에서 구성한 개체의 범위를 늘리는 것은 권장되지 않습니다. 이렇게 하면 Microsoft 지원 팀에서 사용자 지정을 이해하기가 어렵습니다. 객체의 범위를 늘려야 하는 경우 기존 규칙을 편집하고 복제한 다음 원래 규칙을 사용하지 않도록 설정합니다. 

### <a name="cloudfiltered-attribute"></a>클라우드필터링 속성
Active Directory에서는 이 특성을 설정할 수 없습니다. 새 인바운드 규칙을 추가하여 이 특성의 값을 설정합니다. 그런 다음 **변환** 및 **표현식을** 사용하여 메타버스에서 이 특성을 설정할 수 있습니다. 다음 예제에서는 부서 이름이 HRD(대/소문자 구분)로 **HRD** 시작하는 모든 사용자를 동기화하지 않으려는 것을 보여 주며 다음과 같은 방법을 보여 주십니다.

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

먼저 부서를 소스(Active Directory)에서 소문자로 전환했습니다. 그런 다음 `Left` 함수를 사용하여 처음 세 문자만 가져와서 와 `hrd`비교했습니다. 일치하는 경우 값은 `True`로 `NULL`설정됩니다. 값을 null로 설정할 때 우선 순위가 낮은 다른 규칙(숫자 값이 높을수록)은 다른 조건으로 쓸 수 있습니다. 동기화 규칙 유효성 검사 섹션에서 설명한 대로 한 개체에서 미리 보기를 실행하여 동기화 규칙의 유효성을 [검사합니다.](#validate-sync-rule)

![인바운드 동기화 규칙 옵션 만들기](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>조직 단위 필터링
하나 이상의 조직 단위(OUs)를 만들고 동기화하지 않으려는 개체를 이러한 OUs에 이동할 수 있습니다. 그런 다음 Azure AD Connect에서 OU 필터링을 구성합니다. 데스크톱 아이콘에서 **Azure AD 연결을** 시작하고 다음 옵션을 선택합니다. Azure AD Connect를 설치할 때 OU 필터링을 구성할 수도 있습니다. 

![Azure AD 연결 추가 작업](media/how-to-connect-fix-default-rules/default8.png)

마법사를 따라 동기화하지 않으려는 US를 지웁습니다.

![Azure AD 연결 도메인 및 OU 필터링 옵션](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>조인 조건 변경
Azure AD Connect에서 구성한 기본 조인 조건을 사용합니다. 기본 조인 조건을 변경하면 Microsoft 지원에서 사용자 지정을 이해하고 제품을 지원하기가 어렵습니다.

## <a name="validate-sync-rule"></a>동기화 규칙 유효성 검사
전체 동기화 주기를 실행하지 않고 미리 보기 기능을 사용하여 새로 추가된 동기화 규칙의 유효성을 검사할 수 있습니다. Azure AD 연결에서 **동기화 서비스를**선택합니다.

![동기화 서비스가 강조 표시된 Azure AD 연결](media/how-to-connect-fix-default-rules/default10.png)

**메타버스 검색을 선택합니다.** 범위 개체를 **사람으로**선택하고 **절 추가를**선택하고 검색 조건을 언급합니다. 다음으로 **검색을**선택하고 검색 결과에서 개체를 두 번 클릭합니다. 이 단계를 실행하기 전에 포리스트에서 가져오기 및 동기화를 실행하여 Azure AD Connect의 데이터가 해당 개체에 대한 최신 상태인지 확인합니다.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

**메타버스 개체 속성에서** **커넥터를**선택하고 해당 커넥터(포리스트)에서 개체를 선택하고 **속성을 선택합니다.**

![메타버스 개체 속성](media/how-to-connect-fix-default-rules/default12.png)

**미리 보기 선택...**

![커넥터 공간 개체 속성](media/how-to-connect-fix-default-rules/default13a.png)

미리 보기 창에서 왼쪽 창에서 미리 보기 및 **특성 흐름 가져오기** **생성을** 선택합니다.

![미리 보기](media/how-to-connect-fix-default-rules/default14.png)
 
여기서 새로 추가된 규칙은 개체에서 실행되고 `cloudFiltered` 속성을 true로 설정했습니다.

![미리 보기](media/how-to-connect-fix-default-rules/default15a.png)
 
수정된 규칙을 기본 규칙과 비교하려면 두 규칙을 모두 텍스트 파일로 별도로 내보냅니다. 이러한 규칙은 PowerShell 스크립트 파일로 내보내지습니다. 파일 비교 도구(예: windiff)를 사용하여 변경 내용을 확인하여 비교할 수 있습니다. 
 
수정된 규칙에서 `msExchMailboxGuid` 특성은 **Direct**가 아닌 **Expression** 유형으로 변경됩니다. 또한 값이 **NULL** 및 **ExecuteOnce** 옵션으로 변경됩니다. 식별된 점과 우선 순위 차이를 무시할 수 있습니다. 

![윈디프 공구 출력](media/how-to-connect-fix-default-rules/default17.png)
 
규칙을 기본 설정으로 다시 변경하려면 수정된 규칙을 삭제하고 기본 규칙을 사용하도록 설정합니다. 달성하려는 사용자 지정을 잃지 않도록 합니다. 준비가 되면 **전체 동기화**를 실행합니다.

## <a name="next-steps"></a>다음 단계
- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)



