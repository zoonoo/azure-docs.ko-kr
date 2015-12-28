<properties
	pageTitle="앱 모델 v2.0 | Microsoft Azure"
	description="로그인 사용 및 앱과 앱 모델 v2.0의 통합에 대한 Microsoft 앱을 등록하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: Microsoft에 앱을 등록하는 방법

MSA 와 Azure AD 로그인 모두를 허용하는 앱을 빌드하려면, 먼저 Microsoft에 앱을 등록해야 합니다. Azure AD나 MSA를 사용하여 가지고 있는 기존의 앱은 사용할 수 없습니다. - 새로운 앱을 만들 시간입니다.

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공용 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하십시오.

## Microsoft 앱 등록 포털 방문
가장 먼저 해야할 일입니다. - [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)로 이동합니다. 이곳은 Microsoft 앱에 대한 모든 항목을 관리할 수 있는 새로운 앱 등록 포털입니다.

개인, 직장 또는 학교 Microsoft 계정 중 하나로 로그인 합니다. 계정이 하나라도 없다면, 새로운 개인 계정을 등록합니다. 그렇게 오래 걸리지 않으니 등록하세요 - 기다리겠습니다.

등록했나요? 아마도 비어있을 Microsoft 앱의 목록을 보고 있을 것입니다. 변경을 시작해봅시다.

<!-- TODO: Verify strings here -->
**앱 추가**를 클릭하여 이름을 지정합니다. 포털은 코드에서 나중에 사용하는 전역적으로 고유한 응용 프로그램 ID를 앱에 할당합니다. 앱이 API(예: Office, Azure 또는 타사 도구)를 호출하는 액세스 토큰이 필요한 서버 측 구성 요소를 포함한 경우, ** 응용 프로그램 암호 ** 또한 만들어야 합니다.<!-- TODO: Link for app secrets -->

다음으로, 앱이 사용할 플랫폼을 추가합니다. - 웹 기반 앱은 로그인 메시지를 보낼 수 있는 **리디렉트 URI**를 제공합니다 - 모바일 앱은 리디렉트 URI 기본값을 자동으로 기록합니다.

필요에 따라 프로필 섹션에서 로그인 페이지의 디자인을 사용자 할당할 수 있습니다. 다음 단계로 넘어가기 전에 **저장**을 클릭하십시오.

## 빠른 시작 앱 빌드하기
Microsoft 앱이 있다면, 앱 모델 v2.0을 작동시키는 빠른 시작 자습서를 완료할 수 있습니다. 몇가지 권장 사항입니다.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_1217_2015-->