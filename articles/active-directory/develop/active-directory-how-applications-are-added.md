---
title: Azure Active Directory에 애플리케이션을 추가하는 방법과 이유
description: Azure AD에 애플리케이션을 추가하는 것은 무슨 의미이며 어떻게 진행될까요?
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/04/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84069fb80ac751cbde53b0febdac451b54cd2b29
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688759"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>애플리케이션을 Azure AD에 추가하는 방법 및 이유

Azure AD에는 애플리케이션의 두 가지 표현이 있습니다. 
* [애플리케이션 개체](app-objects-and-service-principals.md#application-object) - 애플리케이션의 정의로 간주될 수 있습니다([예외](#notes-and-exceptions)가 있음).
* [서비스 주체](app-objects-and-service-principals.md#service-principal-object) - 애플리케이션의 인스턴스로 간주될 수 있습니다. 서비스 주체는 일반적으로 애플리케이션 개체를 참조하며, 하나의 애플리케이션 개체는 전체 디렉터리에서 여러 서비스 주체에 의해 참조될 수 있습니다.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>애플리케이션 개체란 무엇이며 어디에서 생겨날까요?
Azure Portal에서 [앱 등록](https://aka.ms/appregistrations) 환경을 통해 [애플리케이션 개체](app-objects-and-service-principals.md#application-object)를 관리할 수 있습니다. 애플리케이션 개체는 Azure AD에 대한 애플리케이션을 설명하며, 애플리케이션의 정의로 간주되어 서비스에서 해당 설정에 따라 애플리케이션에 토큰을 발급하는 방법을 인식할 수 있습니다. 다른 디렉터리의 서비스 주체를 지원하는 다중 테넌트 애플리케이션인 경우에도 애플리케이션 개체는 홈 디렉터리에만 있습니다. 애플리케이션 개체는 다음을 포함할 수 있습니다(여기 언급되지 않은 추가 정보도 있음).
* 이름, 로고 및 게시자
* 리디렉션 URI
* 비밀(애플리케이션을 인증하는 데 사용되는 대칭 및/또는 비대칭 키)
* API 종속성(OAuth)
* 게시된 API/리소스/범위(OAuth)
* 응용 프로그램 역할(RBAC)
* SSO 메타데이터 및 구성
* 사용자 프로비전 메타데이터 및 구성
* 프록시 메타데이터 및 구성

애플리케이션 개체는 다음을 포함하는 여러 경로 통해 를 만들 수 있습니다.
* Azure Portal에서 애플리케이션 등록
* Visual Studio를 사용하여 새 애플리케이션을 만들고, Azure AD 인증을 사용하도록 구성
* 관리자가 앱 갤러리에서 애플리케이션을 추가하는 경우(서비스 주체도 만듦)
* Microsoft Graph API, Azure AD Graph API 또는 PowerShell을 사용하여 새 애플리케이션 만들기
* Azure의 다양한 개발자 환경 및 개발자 센터의 API 탐색기 환경을 포함한 기타 여러 가지 방법

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>서비스 주체란 무엇이며 어디에서 생겨날까요?
Azure Portal에서 [엔터프라이즈 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) 환경을 통해 [서비스 주체](app-objects-and-service-principals.md#service-principal-object)를 관리할 수 있습니다. 서비스 주체는 Azure AD에 연결하는 애플리케이션을 관리하며, 디렉터리에 있는 애플리케이션의 인스턴스로 간주될 수 있습니다. 주어진 애플리케이션의 경우 “home” 디렉터리에 등록된 애플리케이션 개체는 하나만 가질 수 있고, 동작하는 모든 디렉터리에서 애플리케이션의 인스턴스를 의미하는 서비스 주체 개체는 둘 이상 가질 수 있습니다. 

서비스 주체는 다음을 포함할 수 있습니다.

* 애플리케이션 ID 속성을 통해 애플리케이션 개체에 대한 역참조
* 로컬 사용자 및 그룹 애플리케이션 역할 할당 기록
* 애플리케이션에 부여된 로컬 사용자 및 관리자 권한 기록
  * 예: 특정 사용자 이메일에 액세스하기 위한 애플리케이션 권한
* 조건부 액세스 정책을 포함한 로컬 정책 기록
* 애플리케이션의 대체 로컬 설정 기록
  * 클레임 변환 규칙
  * 특성 매핑(사용자 프로비전)
  * 디렉터리 특정 앱 역할(애플리케이션이 사용자 지정 역할을 지원하는 경우)
  * 디렉터리 특정 이름 또는 로고

서비스 주체는 애플리케이션 개체처럼 다음을 포함하는 여러 경로를 통해 만들 수 있습니다.

* 사용자가 Azure AD와 통합된 타사 애플리케이션에 로그인하는 경우
  * 로그인하는 동안 사용자는 자신의 프로필 및 다른 권한에 액세스할 수 있는 권한을 애플리케이션에 부여하도록 요청받습니다. 처음으로 동의하는 사람이 애플리케이션을 나타내는 서비스 주체를 디렉터리에 추가하게 됩니다.
* 사용자가 [Office 365](https://products.office.com/)와 같은 Microsoft 온라인 서비스에 로그인하는 경우
  * Office 365를 구독하거나 평가판을 시작하면 Office 365와 관련된 모든 기능을 전달하는 데 사용되는 다양한 서비스를 나타내는 디렉터리에 하나 이상의 서비스 주체가 만들어집니다.
  * SharePoint와 같은 일부 Office 365 서비스는 워크플로 등 구성 요소 간의 보안 통신을 허용하도록 지속적으로 서비스 주체를 만듭니다.
* 관리자가 앱 갤러리에서 애플리케이션을 추가하는 경우(이때 기본 앱 개체도 만듦)
* 애플리케이션을 추가하여 [Azure AD 애플리케이션 프록시](/azure/active-directory/manage-apps/application-proxy) 사용
* SAML 또는 암호 SSO(Single Sign-On)를 사용하여 Single-Sign-On용 애플리케이션 연결
* Azure AD Graph API 또는 PowerShell을 통해 프로그래밍 방식으로

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>애플리케이션 개체와 서비스 주체는 서로 어떻게 관련되어 있나요?
애플리케이션에는 각 애플리케이션 개체가 동작하는 각각의 디렉터리(애플리케이션의 홈 디렉터리를 포함) 내에 하나 이상의 서비스 주체에 의해 참조되는 홈 디렉터리에 하나의 애플리케이션 개체를 갖습니다.
![애플리케이션 개체와 서비스 주체가 서로, 또 Azure AD 인스턴스와 어떻게 관련되어 있는지 설명하는 다이어그램][apps_service_principals_directory]

위 다이어그램에서 Microsoft는 두 개의 디렉터리를 내부적으로 유지하며(왼쪽에 표시됨) 애플리케이션을 게시하는 데 사용합니다.

* Microsoft 응용 프로그램용 디렉터리(Microsoft 서비스 디렉터리)
* 사전 통합된 타사 애플리케이션용 디렉터리(앱 갤러리 디렉터리)

Azure AD와 통합하는 애플리케이션 게시자/공급업체에는 게시 디렉터리가 있어야 합니다(오른쪽에 “일부 SaaS 디렉터리”로 표시됨).

사용자가 직접 추가하는 애플리케이션(다이어그램에서 **앱(사용자)** 으로 표시됨)에는 다음이 포함됩니다.

* 직접 개발한 앱(Azure AD와 통합됨)
* Single-Sign-On용으로 연결된 응용 프로그램
* Azure AD 애플리케이션 프록시를 사용하여 게시된 앱

### <a name="notes-and-exceptions"></a>참고 사항 및 예외
* 일부 서비스 주체만 애플리케이션 개체를 다시 가리킵니다. Azure AD가 처음 빌드되었을 때 애플리케이션에 제공된 서비스는 더 제한적이었으며 서비스 주체로 충분히 애플리케이션 ID를 설정할 수 있었습니다. 원래 서비스 주체는 Windows Server Active Directory 서비스 계정의 형태에 더 가까웠습니다. 이러한 이유로 먼저 애플리케이션 개체를 만들지 않고도 Azure AD PowerShell을 사용하는 것처럼 다른 경로를 통해 서비스 주체를 만들 수 있는 것입니다. Azure AD Graph API는 애플리케이션 개체가 있어야 서비스 주체를 만들 수 있습니다.
* 위에서 설명한 정보 중 일부만 프로그래밍 방식으로 나타납니다. 다음은 UI에서만 사용할 수 있습니다.
  * 클레임 변환 규칙
  * 특성 매핑(사용자 프로비전)
* 서비스 주체 및 애플리케이션 개체에 대한 자세한 내용은 다음과 같은 Azure AD Graph REST API 참조 문서를 참조하세요.
  * [애플리케이션](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [서비스 주체](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>애플리케이션이 Azure AD와 통합되는 이유는 무엇일까요?
다음과 같이 Azure AD에서 제공하는 하나 이상의 서비스를 활용하기 위해 애플리케이션이 Azure AD에 추가됩니다.

* 애플리케이션 인증 및 권한 부여
* 사용자 인증 및 권한 부여
* 페더레이션 또는 암호를 사용한 SSO
* 사용자 프로비전 및 동기화
* 역할 기반 액세스 제어 - 애플리케이션 역할을 정의하는 디렉터리를 사용하여 애플리케이션에서 역할 기반 권한 부여 확인을 수행합니다.
* OAuth 인증 서비스 - API/리소스에 대한 액세스 권한을 부여하기 위해 Office 365 및 다른 Microsoft 애플리케이션에서 사용
* 애플리케이션 게시 및 프록시 - 프라이빗 네트워크의 애플리케이션을 인터넷에 게시

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>애플리케이션을 Azure AD 인스턴스에 추가할 권한이 있는 사용자는?
전역 관리자만 수행할 수 있는 몇 가지 작업이 있는 반면(예: 앱 갤러리에서 애플리케이션 추가하기 및 애플리케이션 프록시를 사용하도록 애플리케이션 구성하기), 기본적으로 디렉터리의 모든 사용자는 개발 중인 애플리케이션 개체를 등록할 수 있는 권한과 함께 동의를 통해 조직적 데이터에 대한 액세스를 공유/부여할 애플리케이션에 대한 재량권을 가집니다. 한 사람이 애플리케이션에 로그인하고 허용한 사용자 디렉터리의 첫 번째 사용자인 경우, 사용자 테넌트에서 서비스 주체를 만들게 됩니다. 그렇지 않으면 동의 부여 정보는 기존 서비스 주체에 저장됩니다.

사용자가 애플리케이션에 등록 및 동의하도록 허용하는 것이 초기에는 우려를 유발할 수도 있지만, 다음 사항을 염두에 두면 됩니다.


* 애플리케이션을 디렉터리에 등록 또는 기록하지 않고도 애플리케이션은 수년간 사용자 인증에 Windows Server Active Directory를 활용해왔습니다. 이제 조직에서 디렉터리를 사용하는 애플리케이션 수 및 용도를 정확하게 파악할 수 있습니다.
* 이러한 책임을 사용자에게 위임하면 관리자 기반 애플리케이션 등록 및 게시 프로세스가 필요하지 않게 됩니다. ADFS(Active Directory Federation Services)를 사용하면 관리자가 개발자를 대신하여 신뢰 당사자로서 애플리케이션을 추가해야 했습니다. 이제 개발자가 직접 할 수 있습니다.
* 비즈니스 목적의 경우 사용자는 조직 계정을 사용하여 애플리케이션에 로그인하는 것이 좋습니다. 이후에 사용자가 조직을 떠날 경우 자동으로 사용하던 애플리케이션에서 자신의 계정에 액세스할 수 없게 됩니다.
* 어떤 데이터를 어떤 애플리케이션과 공유했는지 기록하는 것이 좋습니다. 데이터는 더욱 전송하기 쉬워졌으며 어떤 데이터를 어떤 애플리케이션으로 누구와 공유했는지 기록해놓는 것이 유용합니다.
* OAuth용 Azure AD를 사용하는 API 소유자는 사용자가 애플리케이션에 부여할 수 있는 권한 및 관리자가 동의하는 데 필요한 권한을 정확하게 결정합니다. 관리자만 더 큰 범위와 더 중요한 사용 권한에 동의할 수 있으며, 사용자 동의는 사용자 소유의 데이터 및 기능으로 범위가 제한됩니다.
* 사용자가 애플리케이션을 추가하거나 애플리케이션이 자신의 데이터에 액세스하도록 허용하는 경우 이벤트를 감사할 수 있으므로 Azure Portal 내의 감사 보고서를 보고 디렉터리에 애플리케이션을 추가하는 방법을 결정할 수 있습니다.

계속해서 디렉터리 내 사용자가 관리자 동의 없이 애플리케이션에 로그인하거나 애플리케이션을 등록하지 못하도록 하려면 해당 기능을 해제하도록 변경할 수 있는 두 가지 설정이 있습니다.

* 사용자가 자신을 대신하여 애플리케이션을 승인하지 못하도록 하려면 다음을 수행합니다.
  1. Azure Portal의 엔터프라이즈 애플리케이션에서 [사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) 섹션으로 이동합니다.
  2. **사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음**을 **아니요**로 변경합니다. 
     
     > [!NOTE]
     > 사용자 동의를 해제하려는 경우 관리자는 사용자가 사용해야 하는 새 애플리케이션에 동의해야 합니다.    
* 사용자가 자신의 애플리케이션을 등록하지 못하도록 하려면 다음을 수행합니다.
  1. Azure Portal의 Azure Active Directory에서 [사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) 섹션으로 이동합니다.
  2. **사용자가 애플리케이션을 등록할 수 있음**을 **아니요**로 변경합니다.

> [!NOTE]
> Microsoft 자체는 사용자가 대신 애플리케이션을 등록하고 애플리케이션에 동의할 수 있는 기본 구성을 사용합니다.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

