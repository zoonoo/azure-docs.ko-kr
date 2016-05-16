<properties
	pageTitle="특성 매핑 사용자 지정 | Microsoft Azure"
	description="Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2016"
	ms.author="markusvi"/>


# 특성 매핑 사용자 지정


Microsoft Azure AD는 Salesforce, Google Apps 등과 같은 타사 SaaS 응용 프로그램에 프로비전을 하는 사용자에 대한 지원을 제공합니다. 타사 Saas 응용 프로그램을 프로비전하는 사용자가 있을 경우 Azure 관리 포털은 그 특성 값을 “특성 매핑”이라고 하는 구성 형태로 제어합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 세트가 있습니다. 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. <br> 비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 즉, 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.

Azure AD 포털에서 SaaS 응용 프로그램의 도구 모음에 있는 특성을 클릭하여 이 기능에 액세스 할 수 있습니다.

> [AZURE.NOTE] **특성** 링크는 SaaS 응용 프로그램에 사용할 수 있는 사용자 프로비전을 가지고 있을 때에만 사용 가능합니다.


![Salesforce][1]


도구 모음에서 특성을 클릭하면 현재의 매핑 목록이 SaaS 응용 프로그램에 구성됩니다.

다음 스크린샷은 이에 대한 예제를 보여 줍니다.



![Salesforce][2]


위의 예제를 통해 Salesforce에서 관리된 개체의 **firstName** 특성이 그 연결된 Azure AD 개체의 **givenName**값으로 채워지는 것을 볼 수 있습니다.

특성 매핑의 사용자 지정을 원하거나 사용자 지정된 설정을 기본 구성으로 되돌리고 싶을 경우 응용 프로그램 하단의 도구 모음에 있는 관련 단추를 클릭하여 진행할 수 있습니다.


![Salesforce][3]


SaaS 응용 프로그램에서 올바르게 작동하기 위해 요구되는 특성 매핑이 있습니다. 특성 테이블에서 관련 특성 매핑은**예**를 **필수** 특성에 대한 값으로 가집니다. 특성 매핑이 필수인 경우 삭제할 수 없습니다. 이 경우 **삭제** 기능은 사용할 수 없습니다.

기존 특성 매핑을 수정하려면 매핑을 선택한 후 **편집**을 클릭합니다. 그러면 선택한 특성 매핑을 수정할 수 있는 대화 상자 페이지를 불러옵니다.


![특성 매핑 편집][4]



## 특성 매핑 유형 이해하기


특성 매핑으로 타사 SaaS 응용 프로그램에서 특성이 채워지는 법을 제어합니다. 4 가지의 다른 매핑 형식이 지원됩니다.

- **직접**– 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.


- **상수** – 대상 특성이 지정된 특정 문자열로 채워집니다.


- **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)을 참조하세요.


- **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정된 기본 값으로 채워집니다.



이 네 가지 기본 특성 맵 형식 외에도 사용자 지정 특성 매핑은 **기본** 값 할당의 개념을 지원합니다. 기본 값 할당은 Azure AD에나 대상 개체에 값이 없을 경우 대상 특성이 값으로 채워지는지 확인해줍니다.

Microsoft Azure AD는 동기화 프로세스의 매우 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다. 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 이는 특성 매핑 구성의 업데이트가 모든 관리된 개체를 다시 평가하는 것을 요구하기 때문입니다. 이 때문에 특성 매핑에 대한 연속 변경 횟수를 최소로 유지하는 것이 가장 좋은 방법으로 권장됩니다.


##관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
- [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
- [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=AcomDC_0504_2016-->