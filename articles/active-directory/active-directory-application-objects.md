<properties
pageTitle="Azure Active Directory 응용 프로그램 및 서비스 주체 개체 | Microsoft Azure"
description="Azure Active Directory의 응용 프로그램 및 서비스 주체 개체 간의 관계에 대한 설명"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="07/14/2016"
ms.author="bryanla;mbaldwin"/>

# Azure Active Directory의 응용 프로그램 및 서비스 주체 개체
Azure Active Directory (AD) "응용 프로그램"에 관해 읽을 때 작성자가 정확히 무엇을 나타내는지 항상 명확하지는 않습니다. 이 문서의 목표는 Azure AD 응용 프로그램 통합의 개념 및 구체적인 측면을 모두 정의하고, 뒤 이어 [다중 테넌트 응용 프로그램](active-directory-dev-glossary.md#multi-tenant-application)에 대한 등록 및 동의의 예를 보임으로써 더욱 명확히 하려는 것입니다.

## 개요
Azure AD 응용 프로그램은 단순 소프트웨어보다는 광범위합니다. 그것은 개념적 용어로 응용 프로그램 소프트웨어를 나타낼 뿐 아니라, 런타임 시 인증 및 권한 부여 “대화”에 참여할 수 있도록 하는 Azure AD를 사용한 등록 (즉, ID 구성)을 나타내기도 합니다. 기본적으로 응용 프로그램은 [클라이언트](active-directory-dev-glossary.md#client-application) 역할(리소스 사용)이나 [리소스 서버](active-directory-dev-glossary.md#resource-server) 역할(클라이언트에게 API 노출) 또는 두 역할 모두에서 작동할 수 있습니다. 대화 프로토콜은 클라이언트/리소스가 각기 리소스 데이터를 액세스/보호할 수 있도록 하려는 목표를 가지고 [OAuth 2.0 권한 부여 흐름](active-directory-dev-glossary.md#authorization-grant)에 의해 정의됩니다. 이제 한 단계 더 깊이 들어가, Azure AD 응용 프로그램 모델이 응용 프로그램을 내부적으로 나타내는 방법을 알아봅니다.

## 응용 프로그램 등록
응용 프로그램을 [Azure 클래식 포털](https://manage.windowsazure.com)에 등록할 때, Azure AD 테넌트에 응용 프로그램 개체와 서비스 주체 개체라는 두 개체가 만들어집니다.

#### 응용 프로그램 개체
Azure AD 응용 프로그램은 응용 프로그램의 “홈” 테넌트라고도 하는, 응용 프로그램이 등록된 Azure AD 테넌트에 상주하는 하나의 응용 프로그램 개체에 의해서만 *정의*됩니다. 응용 프로그램 개체는 응용 프로그램에 대한 ID 관련 정보를 제공하고 런타임에 해당 서비스 주체 개체가 사용을 위해 *파생*되는 템플릿입니다.

응용 프로그램을 (모든 테넌트에서 사용하기 위한) 응용 프로그램의 *글로벌* 표현으로 그리고 서비스 주체를 (특정 테넌트에서 사용하기 위한) *로컬* 표현으로 생각할 수 있습니다. Azure AD Graph [응용 프로그램 엔터티][AAD-Graph-App-Entity]는 응용 프로그램 개체에 대한 스키마를 정의합니다. 따라서 응용 프로그램 개체는 소프트웨어 응용 프로그램과 1:1 관계가 있으며 해당 *n* 서비스 주체 개체와 1:*n* 관계가 있습니다.

#### 서비스 주체 개체
서비스 주체 개체는 런타임에 리소스에 액세스할 때 보안 주체가 응용 프로그램을 나타내는 기초를 제공하여 응용 프로그램에 대한 정책 및 권한을 정의합니다. Azure AD Graph [ServicePrincipal 엔터티][AAD-Graph-Sp-Entity]는 서비스 주체 개체에 대한 스키마를 정의합니다.

서비스 주체 개체는 해당 테넌트에서 사용자 계정이 소유한 리소스에 안전하게 액세스할 수 있도록 응용 프로그램 사용의 인스턴스가 나타나야 하는 각 테넌트에서 필요합니다. 단일 테넌트 응용 프로그램은 (홈 테넌트에) 하나의 서비스 주체만이 있습니다. 다중 테넌트 [웹 응용 프로그램](active-directory-dev-glossary.md#web-client)은 같지만 관리자 또는 사용자가 리소스에 액세스하도록 승인한 응용 프로그램이 있는 각 테넌트의 서비스 주체가 더해집니다. 승인에 따라 향후 권한 부여 요청에 대해 서비스 주체 개체를 참조합니다.

> [AZURE.NOTE] 응용 프로그램 개체에 대해 이뤄진 모든 변경은 또한 응용 프로그램의 홈 테넌트(그것이 등록된 테넌트)에만 있는 해당 서비스 주체 개체에도 반영됩니다. 응용 프로그램이 다중 테넌트 액세스를 위해 구성된 경우, 소비자 테넌트가 액세스를 제거하고 다시 액세스 권한이 부여될 때까지 응용 프로그램 개체의 변경 내용은 모든 소비자 테넌트의 서비스 주체 개체에 반영되지 않습니다.

## 예
아래 다이어그램은 **HR 앱**이라는 샘플 다중 테넌트 응용 프로그램의 컨텍스트에서 응용 프로그램의 응용 프로그램 개체와 해당 서비스 주체 개체를 보여줍니다. 이 시나리오에는 세 가지 Azure AD 테넌트가 있습니다.

- **Adatum** -**HR 앱**을 개발한 회사에서 사용하는 테넌트.
- **Contoso** -**HR 앱**의 소비자인 Contoso 조직에서 사용하는 테넌트.
- **Fabrikam** -**HR 앱**의 또 다른 소비자인 Fabrikam 조직에서 사용하는 테넌트.

![응용 프로그램 개체 및 서비스 주체 개체 간의 관계](./media/active-directory-application-objects/application-objects-relationship.png)

위의 다이어그램에서 1 단계는 응용 프로그램의 홈 테넌트에서 응용 프로그램 및 서비스 주체 개체를 만드는 과정입니다.

2 단계에서 Contoso 관리자와 Fabrikam 관리자가 응용 프로그램에 대한 액세스에 전적으로 동의하고 액세스 권한을 부여할 때 서비스 주체 개체가 회사의 Azure AD 테넌트에서 만들어지고 회사 관리자가 부여한 사용 권한이 할당됩니다. 또한 사용자가 개별 사용에 대한 동의를 할 수 있게 HR 앱이 구성/설계될 수 있습니다.

3 단계에서 (Contoso나 Fabrikam과 같은) HR 응용 프로그램의 소비자 테넌트는 각기 런타임시 응용 프로그램의 인스턴스의 사용을 나타내는 관리자가 동의한 사용 권한에 의해 제어되는 자체적인 서비스 주체 개체를 갖고 있습니다.

## 다음 단계
응용 프로그램의 응용 프로그램 개체는 OData [응용 프로그램 엔터티](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ApplicationEntity)에 의해 표시된 대로 Azure AD Graph API를 통해 액세스할 수 있습니다.

응용 프로그램의 서비스 주체 개체는 OData [서비스 주체 엔터티](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ServicePrincipalEntity)에 의해 표시된 대로 Azure AD Graph API를 통해 액세스할 수 있습니다.



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipalentity
[AZURE-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0803_2016-->