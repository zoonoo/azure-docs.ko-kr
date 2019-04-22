---
title: 수정 된 기본 규칙-Azure AD Connect를 해결 하는 방법 | Microsoft Docs
description: Azure AD Connect를 사용 하 여 제공 되는 수정 된 기본 규칙을 수정 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698166"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect에서 수정 된 기본 규칙 수정

Azure AD Connect 동기화에 대 한 기본 규칙을 사용 하 여 제공 됩니다.  안타깝게도 이러한 규칙이 모든 조직에 보편적으로 적용 되지 않습니다 하 고 수정 해야 할 때 요구 사항에 따라 시간대 있을 수 있습니다.

 수 있는 기본 규칙을 수정 하거나 수정 하려는 경우 다음 잠시이 문서를 읽을 수 있습니다.

이 문서는 사용자가 수행한 가장 일반적인 사용자 지정을 2 예가 하 고 이러한 사용자 지정을 달성 하는 올바른 방법은 설명 합니다.

>[!NOTE] 
> 필요한 사용자 지정을 위해 기존 기본 규칙을 수정 지원 되지 않습니다-이렇게 이후 버전에서 최신 버전으로 이러한 규칙을 업데이트 하는 중입니다. 사용 되지 않습니다. 이렇게 필요한 버그 픽스와 새로운 기능은 시작 되지 것입니다.  이 문서에서는 기존 기본 규칙을 수정 하지 않고 동일한 결과 달성 하는 방법을 설명 합니다. 

## <a name="how-to-identify-modified-default-rules"></a>수정 된 기본 규칙을 식별 하는 방법
1.3.7.0 버전부터 **Azure AD Connect**, 이제 쉽게 수정 된 기본 규칙을 식별 하는 것입니다. 바탕 화면에서 앱으로 이동를 클릭 **동기화 규칙 편집기**합니다.

![편집기](media/how-to-connect-fix-default-rules/default1.png)

아래와 같이 모든 수정 된 기본 규칙 편집기에서 이름 앞에 아이콘으로 표시 됩니다.

![icon](media/how-to-connect-fix-default-rules/default2.png)

 또한 동일한 이름 옆에 있는 표준 기본 규칙을 사용 하 여 비활성화 된 규칙을 볼 수 있습니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>일반적인 사용자 지정
다음은 일반적인 사용자 지정 기본 규칙입니다.

- [특성 흐름 변경](#changing-attribute-flow)
- [범위 지정 필터를 변경합니다.](#changing-scoping-filter)
- [조인 조건 변경](#changing-join-condition)

## <a name="before-changing-any-rules"></a>모든 규칙을 변경 하기 전에
- 동기화 스케줄러를 사용 하지 않도록 설정 합니다.  스케줄러는 기본적으로 30 분 마다 실행 됩니다. 변경을 수행하고 새 규칙의 문제를 해결하는 동안 스케줄러가 시작되지 않도록 합니다. 스케줄러를 일시적으로 사용하지 않으려면 PowerShell을 시작하고 `Set-ADSyncScheduler -SyncCycleEnabled $false`를 실행합니다.
 ![기본 규칙](media/how-to-connect-fix-default-rules/default3.png)

- 범위 지정 필터 변경 대상 디렉터리 개체의 삭제 될 수 있습니다. 주의 하세요 개체의 범위에서 변경 하기 전에 합니다. 것이 좋습니다 활성 서버에서 변경 하기 전에 스테이징 서버로 변경 합니다.
- 설명한 것 처럼 단일 개체에서 미리 보기를 실행 하세요 [동기화 규칙의 유효성을 검사](#validate-sync-rule) 섹션에서는 모든 새 규칙을 추가한 후 합니다.
- 새 규칙을 추가 하거나 사용자 지정 동기화 규칙이 수정 후 전체 동기화를 실행 하십시오. 이 동기화는 모든 개체에 새 규칙을 적용 됩니다.

## <a name="changing-attribute-flow"></a>특성 흐름 변경
특성 흐름에 3 개의 다양 한 시나리오를 표준 기본 규칙을 변경 하지 않고 수행 하는 방법을 살펴보겠습니다.
- 새 특성 추가
- 기존 특성의 값을 재정의
- 기존 특성을 동기화 하지 않음

### <a name="adding-new-attribute"></a>새 특성을 추가 합니다.
대상 디렉터리에 소스 디렉터리에서 특성 흐르지 않는 사용할 수 있다면는 [Azure AD Connect 동기화: 디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md) 새 특성을 전달 합니다.

첫 번째 선택에 사용 되어야 함을 유의 하십시오 [Azure AD Connect 동기화: 디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md), Azure AD Connect에서 제공 하는 상자 기능을 옵트아웃 합니다. 그러나 적합 한 다음 기존 표준 기본 동기화 규칙을 수정 하지 않고 특성을 전달 하는 단계를 진행 하지 않은 것을 하면이 두 개의 새 동기화 규칙을 추가 합니다.


#### <a name="add-an-inbound-sync-rule"></a>인바운드 동기화 규칙을 추가 합니다.
인바운드 동기화 규칙 특성에 대 한 원본이 커넥터 공간의 메타 버스 대상이 의미 합니다. 예를 들어, Azure Active Directory에 온-프레미스 Active Directory에서 새 특성 흐름을 시작 하 여 새 인바운드 동기화 규칙을 만듭니다는 **동기화 규칙 편집기**, 선택한 방향으로 **인바운드** 누릅니다 **새 규칙 추가**합니다. 

 ![기본 규칙](media/how-to-connect-fix-default-rules/default3a.png)

규칙 이름을 고유한 명명 규칙에 따라, 여기에서 사용 했습니다 **사용자 지정 In from AD-사용자**, 즉 규칙은 사용자 지정 규칙은 메타 버스에 AD 커넥터 공간에서 인바운드 규칙입니다.   

 ![기본 규칙](media/how-to-connect-fix-default-rules/default3b.png)

규칙의 향후 유지 관리는이 규칙의 목표 등이 필요한 이유는 쉽게 규칙의 고유한 설명을 제공 합니다.
연결 된 시스템 (포리스트)-특성 원본을 선택 합니다. 그런 다음 연결 된 시스템 개체 유형 및 메타 버스 개체 형식을 선택 합니다.

0 – 사이의 우선 순위 값 지정 99 (수를 줄이려면, 더 높은 우선 순위). 과 같은 다른 필드 'Tag', ' 암호 동기화 사용 ' 및 'Disabled' 기본값으로 유지 합니다.

유지 '범위 지정 필터' 비어 있거나, 즉, AD 연결 된 시스템에서 메타 버스 사이 가입 된 모든 개체에는 규칙을 적용 합니다.

유지 '조인 규칙' 빈 즉,이 규칙은 표준 기본 규칙에 정의 된 조인 조건에서 도움을 받습니다. 이 다른 이유가 없습니다 사용 안 함/삭제 표준 기본 규칙이 있으므로 도움을 받는 조인 조건이 있는 경우 다음 특성을 이동 하지 것입니다. 

적절 한 변환을 추가 사용자 특성에 대 한 상수를 상수 값, 대상 특성을 할당 하거나 직접 원본 또는 대상 특성 또는 특성에 대 한 식을 간의 매핑 수 있습니다. 다양 한 같습니다 [식 함수](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) 사용할 수 있습니다.

#### <a name="add-an-outbound-sync-rule"></a>아웃 바운드 동기화 규칙을 추가 합니다.
지금 인바운드 동기화 규칙을 방금 추가 하 여 수행한 작업을 절반 특성 대상 디렉터리에 아직 연결 되지 않은 때문에 합니다. 특성 대상 director 연결할 원본이 메타 버스 및 대상이 연결 된 시스템 즉 아웃 바운드 규칙을 만드는 해야 합니다. 아웃 바운드 규칙을 만들려면 시작 **동기화 규칙 편집기**를 변경 합니다 **방향** 에 **아웃 바운드** 클릭 **새 규칙 추가**. 

![기본 규칙](media/how-to-connect-fix-default-rules/default3c.png)

인바운드 규칙에서와 같이 고유한 명명 규칙을 사용할 수 있습니다 **이름을** 규칙입니다. 선택 된 **연결 된 시스템** Azure AD 테 넌 트로 특성 값을 설정 하려면 연결 된 시스템 개체를 선택 합니다. 0-간에 우선 순위를 설정할 99입니다. 

![기본 규칙](media/how-to-connect-fix-default-rules/default3d.png)

유지할 **범위 지정 필터** 비어 있는 경우 유지 **조인 규칙** 빈, 상수, 직접 또는 식으로 변환에서 입력 합니다. 

이 예제에서는 Azure Active Directory에 Active Directory에서 사용자 개체에 대해 새 특성 흐름 하는 방법을 알아보았습니다. 원본 및 대상 개체에서 모든 특성을 매핑할 다음이 단계를 사용할 수 있습니다.  자세한 내용은 참조 [사용자 지정 동기화 규칙을 만드는](how-to-connect-create-custom-sync-rule.md) 하 고 [사용자를 프로 비전 하기 위한 준비](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)합니다.

### <a name="overriding-value-of-existing-attribute"></a>기존 특성의 값을 재정의
이미 매핑된 특성의 값을 재정의 하려면, 예를 들어 항상 하려는 Azure AD의 특성에 Null 값을 설정, 흐름 및 이전 단계에서 설명한 것 처럼 인바운드 규칙을 간단히 만들어 이렇게 수  **AuthoritativeNull** 대상 특성에 대 한 상수 값입니다. 사용 했습니다 AuthoritativeNulll Null 대신이 경우 note 하십시오. 즉, 더 낮은 우선 순위 (규칙에 더 높은 숫자 값)는 경우에 null이 아닌 값 Null 값으로 대체 됩니다. 그러나는 AuthoritativeNull Null로 처리 되 고 다른 규칙에 의해 null이 아닌 값으로 대체 됩니다. 

### <a name="dont-sync-existing-attribute"></a>기존 특성을 동기화 하지 않음
동기화 할 특성을 제외 하려는 경우 Azure AD Connect에서 제공 하는 기능을 필터링 하는 특성을 사용할 수 있습니다. 시작할 **Azure AD Connect** 에서 바탕 화면 아이콘을 선택한 후 **동기화 옵션 사용자 지정**합니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default4.png)

 했는지 **Azure AD 앱 및 특성 필터링** 선택한 클릭 **다음**합니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default5.png)

동기화에서 제외 하려는 특성을 선택 취소 합니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>범위 지정 필터를 변경합니다.
Azure AD Sync 대부분의 개체 처리, 개체의 범위를 줄이고 더 적은 개체 표준 기본 동기화 규칙을 변경 하지 않고 지원 되는 방식으로 내보낼 수를 줄일 수 있습니다. 할 수 있습니다 개체의 범위를 향상 시키려는 경우를 대비 **편집** 기존 규칙을 복제 하 고 원래 규칙을 사용 하지 않도록 설정 합니다. Microsoft는 Azure AD Connect에서 구성 된 범위를 늘릴 필요가 것이 좋습니다. 개체의 범위에서 증가 하 게 사용자 지정 항목을 이해 하 고 제품을 지원 하려면 지원 팀에 대 한 하드 합니다.

Azure AD와 동기화 하는 개체의 범위를 절감 하는 방법을 다음과 같습니다. 범위를 줄일 경우는 **사용자** 필터링 스케일 아웃 사용자에 대 한 암호 해시 동기화는 중지도 동기화 되 고 있습니다. 개체는 이미 동기화 한 후 범위를 줄인 후 필터링 스케일 아웃 개체 대상 디렉터리에서 삭제 되는 경우 매우 신중 하 게 범위에 문의 하세요.
동기화 하는 개체의 범위를 줄이기 위해 지원 되는 방법은 다음과 같습니다.

- [cloudFiltered 특성](#cloudfiltered-attribute)
- [OU 필터링](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered 특성
이 Active Directory에서 설정할 수 있는 특성 note 하십시오. 에 설명 된 대로 새 인바운드 규칙을 추가 하 여이 특성의 값을 설정할 필요가 **기존 특성의 값을 재정의** 섹션입니다. 사용할 수는 **변환** 사용 하 여 **식** 메타 버스에서이 특성을 설정 하려면. 사용자의 부서 이름이 대/소문자 구분을 사용 하 여 시작 하는 모든 동기화 하지 않으려는 예로 **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

원본 (Active Directory)에서 소문자로 부서를 변환 먼저 합니다. Left 함수를 사용 하 여 다음 처음 3 개의 문자를 수행 하 고 hrd를 사용 하 여 비교 합니다. 이 일치 하면 True이 고 그렇지 NULL로 값을 설정 합니다. 하세요 몇 가지 다른 규칙이 더 낮은 우선 순위 (더 높은 숫자 값)를 사용 하 여 다른 조건을 사용 하 여 여기에 쓸 수 있도록 NULL 값을 설정 하는 것 note 합니다. 에 설명 된 대로 동기화 규칙의 유효성을 검사 한 개체에서 미리 보기를 실행 하세요 [동기화 규칙의 유효성을 검사](#validate-sync-rule) 섹션입니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU 필터링
하나 이상의 Ou를 만들 수 있으며 이러한 Ou를 동기화 하지 않을 개체를 이동할 수 있습니다. OU를 실행 하 여 Azure AD Connect에서 필터링을 구성한 **Azure AD Connect** 바탕 화면 아이콘에서 아래와 같이 옵션을 선택 합니다. 또한 Azure AD Connect 설치 시 필터링 OU를 구성할 수 있습니다. 

![기본 규칙](media/how-to-connect-fix-default-rules/default8.png)

마법사를 따르고 동기화 하지 않을 Ou의 선택을 취소 합니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>조인 조건 변경
Azure AD Connect에서 구성 하는 조건을 조인 기본값을 사용 하는 것이 좋습니다. 기본 조인 조건을 변경 하 게 사용자 지정 항목을 이해 하 고 제품을 지원 하려면 지원 팀에 대 한 하드 합니다.

## <a name="validate-sync-rule"></a>동기화 규칙 유효성 검사
전체 동기화 주기를 실행 하지 않고 미리 보기 기능을 사용 하 여 새로 추가 된 동기화 규칙을 확인할 수 있습니다. 시작할 **동기화 서비스** UI입니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default10.png)

클릭 합니다 **메타 버스 검색**를 선택 범위 개체로 **person**, **절 추가** 검색 조건에 언급 한. 클릭할 **검색** 개체에서 두 번 클릭 하 고 단추를 **검색 결과** 을이 단계를 실행 하기 전에 포리스트 가져오기 및 동기화를 실행 하면이 Azure AD Connect에서 데이터 확인 참고 해당 개체에 대 한 최신 상태입니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default11.png)



선택 **커넥터**해당 connector(forest)에서 개체를 선택, 클릭 **속성...** .

![기본 규칙](media/how-to-connect-fix-default-rules/default12.png)

클릭 된 **미리 보기...**

![기본 규칙](media/how-to-connect-fix-default-rules/default13a.png)

클릭할 **미리 보기 생성** 하 고 **가져오기 특성 흐름** 왼쪽된 창에서.

![기본 규칙](media/how-to-connect-fix-default-rules/default14.png)
 
여기서 새로 추가 된 규칙 개체에서 실행 되 고 cloudFiltered 특성을 True로 설정한는 확인할 수 있습니다.

![기본 규칙](media/how-to-connect-fix-default-rules/default15a.png)
 
기본 규칙을 사용 하 여 수정 된 규칙을 비교 하는 방법
텍스트 파일로 두 규칙을 개별적으로 내보낼 수 있습니다. 이러한 규칙은 powershell 스크립트 파일로 내보내지지 것입니다. 어떤 종류의 변경 완료를 확인 하려면 파일 비교 도구를 사용 하 여 비교할 수 있습니다. 여기이 예제에서는 사용 했습니다 windiff 두 파일을 비교 합니다.
 
규칙 수정 사용자에서는 확인할 수 있습니다, msExchMailboxGuid 특성으로 변경 됩니다 **식을** 형식 대신 **Direct** 값을 갖는 **NULL** 및  **ExecuteOnce** 옵션입니다. 식별 된 및 우선 순위 차이 무시할 수 있습니다. 

![기본 규칙](media/how-to-connect-fix-default-rules/default17.png)
 
수정 된 기본 규칙을 수정 하는 방법
기본 설정으로 규칙을 해결 하려면 수정 된 규칙을 삭제 하 고 아래와 같이 기본 규칙을 사용 하도록 설정 하 고 다음 실행할 수는 **전체 동기화**합니다. 달성 하려는 전에 사용자 지정을 손실 하지 않도록 위에서 언급 한 대로 정정 작업을 수행 하세요 # # 다음 단계

## <a name="next-steps"></a>다음 단계
- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)

