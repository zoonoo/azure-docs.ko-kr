<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 셀프 서비스 암호 재설정 | Microsoft Azure"
	description="Azure Active Directory B2C 미리 보기에서 소비자를 위해 셀프 서비스 암호 재설정을 설정하는 방법을 보여주는 항목"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 소비자를 위해 셀프 서비스 암호 재설정 설정

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

이 기능을 사용하면 (로컬 계정에 등록된)소비자는 자체적으로 암호를 재설정할 수 있습니다. 특히 정기적으로 응용 프로그램을 사용하는 수 백만 명의 소비자가 있는 경우 지원 담당자의 부담을 크게 줄여줍니다. 현재 검증된 전자 메일 주소를 지원 복구 방법으로 사용하여 지원합니다. 추가 복구 방법을 나중에 추가합니다.(확인된 전화번호, 보안 질문 등) 기본적으로 디렉터리는 셀프 서비스 암호 재설정을 설정하지 않습니다. 설정하려면 다음 단계를 사용합니다.

1. [Azure 포털](https://manage.windowsazure.com/)에 구독 관리자로 로그인합니다. 이것은 동일한 작업이거나 학교 계정 또는 디렉터리를 만드는 데 사용한 동일한 Microsoft 계정입니다.
2. 왼쪽의 탐색 모음에서 Active Directory 확장으로 이동합니다.
3. **디렉터리** 탭에서 디렉터리를 찾고 클릭합니다.
4. **구성** 탭을 클릭합니다.
5. **사용자 암호 재설정 정책** 섹션으로 스크롤하여 **암호 재설정을 위해 사용할 수 있는 사용자** 옵션을 **예**로 설정/해제합니다. **대체 전자 메일 주소** 옵션을 선택하고 그대로 둡니다.

    ![셀프 서비스 암호 재설정](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. 페이지 맨 아래에서 **저장**을 클릭합니다. 완료되었습니다!

테스트하려면 (로컬 계정을 ID 공급자로 가진)로그인 정책에서 "지금 실행" 기능을 사용합니다. (전자 메일 주소 및 암호 또는 사용자 이름 및 암호를 입력하는)로컬 계정 로그인 페이지에서 **계정에 액세스할 수 없습니까?**를 클릭하여 소비자 환경을 확인합니다.

> [AZURE.NOTE]셀프 서비스 암호 재설정 페이지는 [회사 브랜딩 기능](active-directory-add-company-branding.md)을 사용하여 사용자 지정할 수 있습니다.

<!---HONumber=Oct15_HO3-->