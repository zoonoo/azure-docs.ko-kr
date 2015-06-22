<properties 
	pageTitle="Azure AD 서비스 제한 및 제한 사항" 
	description="Azure Active Directory 서비스의 사용 제약 및 기타 서비스 제한입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD 서비스 제한 및 제한 사항

다음은 Azure Active Directory 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다. Microsoft Azure 서비스 제한을 모두 보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## 디렉터리

사용자 한 명만 최대 20개의 Azure Active Directory 디렉터리에 연결될 수 있습니다. 다음 예 중 하나의 경우 이 제한에 도달할 수 있습니다.

- 사용자 한 명이 20개의 디렉터리를 만드는 경우
- 사용자 한 명이 20개의 디렉터리에 멤버로 추가된 경우
- 사용자 한 명이 10개의 디렉터리를 만들고 나중에 다른 사용자가 다른 10개의 디렉터리에 이 사용자를 추가한 경우

## 개체

- Azure Active Directory Premium 또는 Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Windows Intune 또는 디렉터리 서비스에 대해 Azure Active Directory를 사용하는 기타 Microsoft 온라인 서비스의 구독자는 제한이 없습니다.
- Azure Active Directory의 무료 버전으로 단일 디렉터리에 최대 50만 개의 개체를 사용할 수 있습니다.
- 관리자가 아닌 사용자는 최대 250개의 개체를 만들 수 있습니다.

##스키마 확장

현재 “User”, “Group”, “TenantDetail”, “Device”, “Application” 및 “ServicePrincipal” 엔터티는 “String” 형식 또는 “Binary” 형식의 단일값 특성으로 확장할 수 있습니다. 여기에는 다음과 같은 제한 사항이 포함됩니다.

- 문자열 형식 확장은 최대 256자까지 가능합니다.
- 이진 형식 확장은 256바이트로 제한됩니다.
- 단일 개체에 100개의 확장 값(모든 형식 및 모든 응용 프로그램)을 작성할 수 있습니다.
- 스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 응용 프로그램에 쓰기 권한이 있어야 합니다.

## 응용 프로그램

최대 10 명의 사용자가 단일 응용 프로그램의 소유자가 될 수 있습니다.

## 그룹 

- 최대 10 명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.
- Azure Active Directory에서 단일 그룹의 멤버가 될 수 있는 개체의 수는 제한이 없습니다.


> [AZURE.NOTE]
> 
온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 개체 수에는 제한이 있습니다. - 디렉터리 동기화를 사용하는 경우 사용자 15,000명으로 제한됩니다. - Azure AD Connect를 사용하는 경우 사용자 50,000명으로 제한됩니다.

## 액세스 패널

- Azure AD Premium, Azure AD Basic 또는 Enterprise Mobility Suite 구독자에 대해 각 최종 사용자의 액세스 패널에 표시될 수 있는 응용 프로그램의 수는 제한이 없습니다.
- Azure Active Directory의 무료 버전으로 각 최종 사용자의 액세스 패널에 표시될 수 있는 사전 통합 SaaS 앱(예: Box, Salesforce 또는 Dropbox)은 최대 10개입니다. 조직에서 나중에 Azure Active Directory와 통합된 응용 프로그램을 개발한 경우 최종 사용자에게 응용 프로그램이 10개 이상 표시될 수 있습니다. 관리자 계정에는 이 제한이 적용되지 않습니다.

## 보고서

최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다.

## 다음 단계
- [조직으로 Azure에 등록](sign-up-organization.md)
- [Azure 구독과 Azure AD의 연관 관계](active-directory-how-subscriptions-associated-directory.md)
- [Azure AD 용어](active-directory-terminology.md)

<!---HONumber=58--> 