<properties
   pageTitle="Azure AD Connect: 설계 개념 | Microsoft Azure"
   description="이 항목에서는 특정 구현 설계 영역을 자세히 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: 설계 개념
이 토픽에서는 Azure AD Connect의 설계를 구현하는 중에 고려해야할 영역들을 설명합니다. 이 토픽은 특정 영역들을 심층 분석하고 이 개념들을 다른 토픽에서처럼 간단히 설명합니다.

## sourceAnchor
sourceAnchor 특성은 *개체의 수명 동안 변경할 수 없는 속성*으로 정의됩니다. 온-프레미스와 Azure AD의 동일한 개체처럼 개체를 고유하게 식별합니다. 이 특성은 **immutableId**라고도 하며 두 이름을 서로 바꿔 사용할 수 있습니다.

변경할 수 없음과 같은 단어, 즉 변경하지 못한다는말은 이 토픽에서 중요합니다. 이 특성의 값은 한번 설정된 후에는 변경할 수 없기 때문에 시나리오를 지원하는 설계를 선택하는 것이 중요합니다.

이 특성은 다음 시나리오에서 사용됩니다.

- 새 동기화 엔진 서버를 구축하거나 재해 복구 시나리오를 다시 구축할 때,이 특성은 개체 온-프레미스의 Azure AD 내의 기존 개체에 연결됩니다.
- 클라우드 전용 ID에서 동기화 ID 모델로 이동할 경우, 이 특성은 온-프레미스 개체를 포함한 Azure AD의 “완전 일치”하는 기존 개체에 개체를 허용합니다.
- 페더레이션을 사용할 경우, **userPrincipalName**을 포함한 특성은 사용자를 고유하게 식별하는 클레임에서 사용됩니다.

이 토픽에서는 사용자와 관련이 있는 sourceAnchor만 설명합니다. 동일한 규칙이 모든 개체 형식에 적용되나, 주로 관련이 있는 사용자에게만 해당됩니다.

### 알맞은 sourceAnchor 특성을 선택합니다.
특성 값은 다음 규칙을 따라야 합니다.

- 길이는 60자 미만이어야 합니다.
- &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ \_ 기호와 같은 특수 문자는 포함할 수 없습니다.
- 전역적으로 고유해야 합니다.
- 문자열, 정수 또는 이진이어야 합니다.
- 사용자 이름을 기반으로 할 수 없습니다. 사용자 이름은 변경됩니다.
- 대/소문자를 구분할 수 없으며 대/소문자에 따라 달라질 수 있는 값을 사용하지 않도록 해야 합니다.
- 개체를 만들 때 할당되어야 합니다.

선택된 sourceAnchor가 문자열 형식이 아닌 경우, Azure AD Connect는 특성 값에 특수 문자가 나타나지 않도록 Base64로 인코딩합니다. ADFS 이외의 다른 페더레이션 서버를 사용할 경우, 그 서버가 특성을 Base64로 인코딩할 수 있는지 확인하세요.

SourceAnchor 특성은 대소문자를 구분합니다. "JohnDoe"의 값은 "johndoe"와 다릅니다.

단일 포리스트 온-프레미스가 있는 경우 사용해야 할 특성은 **objectGUID**입니다. Azure AD Connect에서 사용되는 express 설정을 사용할 때 사용되는 특성이기도 하며 DirSync가 사용하는 특성이기도 합니다.

여러 포리스트가 있고 사용자를 포리스트 간 및 동일한 포리스트 내의 도메인 간에 이동하지 않는 경우에도 **objectGUID** 특성을 사용하는 것이 좋습니다.

포리스트와 도메인 간에 사용자를 이동하지 않는 경우 변경되지 않거나 이동 중에 사용자와 함께 이동할 수 있는 특성을 찾아야 합니다. 가상 특성을 도입하는 것이 좋습니다. GUID처럼 보이는 것을 포함할 수 있는 특성이 적합합니다. 개체가 생성되는 동안 새 GUID는 생성되고 사용자에게 표시됩니다. 동기화 엔진 서버에서 사용자 지정 규칙을 만들어 **objectGUID**에 따라 이 값을 만들고 ADDS에서 선택한 특성을 업데이트할 수 있습니다. 개체를 이동할 때, 이 값의 콘텐츠도 복사했는지 확인합니다.

다른 솔루션은 변경되지 않는 것을 알고 있는 기존 특성을 선택하는 것입니다. 일반적으로 사용되는 특성에는 **employeeID**가 있습니다. 문자를 포함하는 특성을 고려하는 경우 특성 값의 대/소문자가 변경될 수 있는 가능성이 없는지 확인합니다. 나쁜 속성은 사용자의 이름의 해당 속성을 포함하여 사용할 수 없습니다. 결혼 또는 이혼 해서 이름을 바꿔야하는 경우에도 이 특성에서는 허용되지 않습니다. **userPrincipalName**, **mail** 및 **targetAddress**와 같은 특성을 Azure AD Connect 설치 마법사에서 선택할 수 없는 이유이기도 합니다. 이러한 특성은 sourceAnchor에서 허용하지 않는 @-문자도 포함하고 있습니다.

### SourceAnchor 특성 변경
sourceAnchor 특성값은 개체가 Azure AD에 생성되고 ID가 동기화된 후에는 값을 변경할 수 없습니다.

이러한 이유로 Azure AD Connect에는 다음과 같은 제한사항이 적용됩니다.

- SourceAnchor 특성은 오직 초기 설치 동안에만 설정할 수 있습니다. 설치 마법사를 다시 실행하는 경우, 이 옵션은 읽기 전용입니다. 변경해야 할 경우, 제거하고 다시 설치합니다.
- 다른 Azure AD Connect 서버를 설치한 경우 이전에 사용했던 동일한 sourceAnchor 특성을 선택해야만 합니다. 이전부터 DirSync를 사용했으며 Azure AD Connect로 이동한 경우 DirSync에서 사용했던 특성이므로 **objectGUID**를 사용해야 합니다.
- 개체를 Azure AD로 내보낸 후 sourceAnchor 값이 변경된 경우, Azure AD Connect 동기화는 오류를 표시하고 문제가 해결되거나 sourceAnchor를 source 디렉터리에 원상 복귀시키기 전까지는 어떠한 변경도 허용하지 않습니다.

## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0218_2016-->