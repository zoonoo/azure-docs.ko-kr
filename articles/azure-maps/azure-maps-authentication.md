---
title: 인증 방법 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Azure Active Directory(Azure AD) 및 공유 키 인증에 대해 알아봅니다. 둘 다 Microsoft Azure 지도 서비스에 사용됩니다. Azure Maps 구독 키를 받는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335694"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps 인증

Azure Maps는 요청을 인증하는 두 가지 방법, 즉 공유 키 인증 및 Azure Active Directory 인증을 지원합니다. 이 문서에서는 Azure Maps 서비스의 구현을 안내하는 데 도움이 되는 이러한 인증 방법을 설명합니다.

> [!NOTE]
> Azure Maps와의 보안 통신을 개선하기 위해 TLS(전송 계층 보안) 1.2를 지원하며 TLS 1.0 및 1.1에 대한 지원을 폐기합니다. 서비스 중단을 방지하려면 **2020년 4월 2일 이전에 TLS 1.2를 사용하도록 서버 및 응용 프로그램을 업데이트합니다.**  현재 TLS 1.x를 사용하는 경우 TLS 1.2 준비 상태를 평가하고 [TLS 1.0 문제 해결에](https://docs.microsoft.com/security/solving-tls1-problem)설명된 테스트를 통해 마이그레이션 계획을 수립합니다.

## <a name="shared-key-authentication"></a>공유 키 인증

 Azure Maps 계정을 만든 후 기본 및 보조 키가 생성됩니다. 공유 키 인증을 사용하여 Azure Maps를 호출할 때 기본 키를 구독 키로 사용하는 것이 좋습니다. 공유 키 인증은 Azure Maps 계정에서 생성된 키를 Azure Maps 서비스에 전달합니다. Azure Maps 서비스에 대한 각 요청에 대해 *구독 키를* URL에 매개 변수로 추가합니다. 키 를 롤링 하는 등의 시나리오에서 두 번째 키를 사용할 수 있습니다.  

Azure 포털에서 키를 보는 자세한 내용은 [인증 관리를](https://aka.ms/amauthdetails)참조하십시오.

> [!Tip]
> 키를 정기적으로 다시 생성하는 것이 좋습니다. 두 개의 키가 제공되므로 다른 키를 다시 생성하는 동안 한 키로 연결을 유지할 수 있습니다. 키를 다시 생성할 때 새 키로 계정에 액세스하는 모든 응용 프로그램을 업데이트해야 합니다.

## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory 인증(미리 보기)

이제 Azure Maps는 Azure Active [Directory(Azure AD)를](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)사용하여 Azure Maps 서비스에 대한 요청 인증을 제공합니다. Azure AD는 [RBAC(역할 기반 액세스 제어)를](https://docs.microsoft.com/azure/role-based-access-control/overview)포함하여 ID 기반 인증을 제공합니다. RBAC는 Azure Maps 리소스에 대한 사용자 수준, 그룹 수준 또는 응용 프로그램 수준 액세스를 부여하는 데 사용됩니다. 다음 섹션에서는 Azure AD와의 Azure Maps 통합의 개념 및 구성 요소에 대해 설명합니다.

## <a name="authentication-with-oauth-access-tokens"></a>OAuth 액세스 토큰 인증

Azure Maps는 Azure Maps 계정을 포함하고 있는 Azure 구독과 연결된 Azure AD 테넌트에 대한 **OAuth 2.0** 액세스 토큰을 허용합니다. Azure Maps는 다음에 대한 토큰도 허용합니다.

* Azure AD 사용자
* 사용자가 위임한 권한을 사용하는 파트너 응용 프로그램
* Azure 리소스에 대한 관리 ID

Azure Maps는 각 Azure Maps 계정에 대해 *고유 식별자(클라이언트 ID)* 를 생성합니다. 이 클라이언트 ID를 추가 매개 변수와 결합하면 Azure AD에서 토큰을 요청할 수 있습니다. 토큰을 요청하려면 Azure 환경에 따라 다음 표의 값을 지정합니다.

| Azure 환경   | Azure AD 토큰 끝점 |
| --------------------|-------------------------|
| Azure 공용        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Azure AD를 구성하고 Azure Maps에 대한 토큰을 요청하는 방법에 대한 자세한 내용은 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

Azure AD에 토큰을 요청하는 방법에 대한 일반적인 정보를 보려면 [인증이란?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)을 참조하세요.

## <a name="request-azure-map-resources-with-oauth-tokens"></a>OAuth 토큰을 사용하여 Azure Map 리소스 요청

Azure AD가 토큰을 받으면 Azure Maps는 다음과 같은 필수 요청 헤더 집합을 통해 요청을 보냅니다.

| 요청 헤더    |    값    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 권한 부여     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`는 Azure Maps 인증 페이지에 표시되는 Azure Maps 계정 기반 GUID입니다.

OAuth 토큰을 사용하는 Azure Maps 경로 요청의 예는 다음과 같습니다.

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

클라이언트 ID를 보는 방법에 대한 내용은 [인증 세부 정보 보기](https://aka.ms/amauthdetails)를 참조하세요.

## <a name="control-access-with-rbac"></a>RBAC를 사용하여 액세스 제어

Azure AD에서 RBAC를 사용하여 보안 된 리소스에 대한 액세스를 제어합니다. Azure Maps 계정을 설정하고 Azure Maps Azure AD 테넌트를 등록합니다. Azure Maps는 Azure 리소스에 대한 관리되는 ID를 통해 개별 Azure AD 사용자, 그룹, 응용 프로그램, Azure 리소스 및 Azure 서비스에 대한 읽기 액세스 제어를 지원합니다. Azure Maps 포털 페이지에서 선택한 역할에 대해 RBAC를 설정할 수 있습니다.

![Azure Maps 데이터 판독기(미리 보기)](./media/azure-maps-authentication/concept.png)

RBAC 설정을 보는 방법에 대한 내용은 [Azure Maps에 대한 RBAC 구성 방법](https://aka.ms/amrbac)을 참조하세요.

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 리소스 및 Azure Maps에 대한 관리형 ID

[Azure 리소스에 대한 관리되는 ID는](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure 서비스에 Azure Maps 서비스에 액세스하도록 승인할 수 있는 자동으로 관리되는 ID를 제공합니다. 관리되는 ID의 몇 가지 예로는 Azure 앱 서비스, Azure 함수 및 Azure 가상 컴퓨터가 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure AD 및 Azure Maps를 사용하여 애플리케이션을 인증하는 방법을 자세히 알아보려면 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

* Azure Maps, 맵 컨트롤 및 Azure AD 인증에 대한 자세한 내용은 [Azure Maps 맵 컨트롤 사용](https://aka.ms/amaadmc)을 참조하세요.
