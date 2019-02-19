---
title: Azure Maps 인증 | Microsoft Docs
description: Azure Maps 서비스 사용을 위한 인증입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118849"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps 인증

Azure Maps는 요청을 인증하는 두 가지 방법을 지원합니다. 공유 키 또는 Azure AD(Azure Active Directory)는 Azure Maps로 전송된 각 요청에 권한을 부여하는 고유한 메서드를 제공합니다. 이 문서의 목적은 두 인증 방법을 설명하여 인증 구현에 도움을 주는 것입니다.

## <a name="shared-key-authentication"></a>공유 키 인증

공유 키 인증은 각 요청과 관련하여 Azure Maps 계정에서 생성한 키를 Azure Maps로 전달하는 방법을 사용합니다.  두 키는 Azure Maps 계정을 만들 때 생성됩니다.  Azure Maps 서비스에 요청할 때마다 구독 키를 URL에 매개 변수로 추가해야 합니다.

> [!Tip]
> 키를 정기적으로 다시 생성하는 것이 좋습니다. 한 키를 다시 생성하는 동안 다른 키를 사용하여 연결을 유지할 수 있도록 두 개의 키가 제공됩니다. 키를 다시 생성하는 경우 이 계정에 액세스하는 모든 애플리케이션이 새 키를 사용하도록 업데이트해야 합니다.

키를 보려면 [인증 세부 정보](https://aka.ms/amauthdetails)를 확인하세요.

## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory 인증(미리 보기)

이제 Azure Maps는 [Azure AD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)와 통합되므로 Azure Maps 서비스 요청을 인증할 수 있습니다.  Azure AD는 Azure Maps 리소스에 대한 사용자 또는 애플리케이션 수준 액세스 권한을 부여할 수 있도록 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 비롯한 ID 기반 인증을 제공합니다. 이 문서의 목적은 Azure Maps Azure AD 통합의 개념과 구성 요소를 이해하도록 도와주는 것입니다.

## <a name="authentication-with-oauth-access-tokens"></a>OAuth 액세스 토큰 인증

Azure Maps는 Azure Maps 계정을 포함하고 있는 Azure 구독과 연결된 Azure AD 테넌트에 대한 **OAuth 2.0** 액세스 토큰을 허용합니다.  Azure Maps는 다음에 대한 토큰을 허용합니다.

* Azure AD 사용자 
* 사용자가 위임한 권한을 사용하는 타사 애플리케이션
* Azure 리소스에 대한 관리 ID

Azure Maps는 각 Azure Maps 계정의 `unique identifier (client ID)`를 생성합니다.  클라이언트 ID를 추가 매개 변수와 결합하면 아래 값을 지정하여 Azure AD에서 토큰을 요청할 수 있습니다.

```
https://login.microsoftonline.com
```
Azure AD를 구성하고 Azure Maps에 대한 토큰을 요청하는 방법에 대한 자세한 내용은 [인증 관리 방법](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)을 참조하세요.

Azure AD에서 토큰을 요청하는 것과 관련된 일반적인 내용은 [Azure Active Directory의 인증 기본 사항](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)을 참조하세요.

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>OAuth 토큰을 사용하여 Azure Map 리소스 요청

Azure AD에서 토큰을 획득했으면 다음 두 가지 필수 요청 헤더 세트를 사용하여 Azure Maps로 요청을 보낼 수 있습니다.

| 요청 헤더    |    값    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 권한 부여     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`는 Azure Maps 인증 페이지에 표시되는 Azure Maps 계층 기반 GUID입니다.

아래는 OAuth 토큰을 사용하는 Azure Maps 경로 요청 예제입니다.

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

클라이언트 ID를 보려면 [인증 세부 정보](https://aka.ms/amauthdetails)를 확인하세요.

## <a name="control-access-with-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)를 사용하여 액세스 제어

Azure AD의 핵심 기능은 RBAC를 통해 보안 리소스에 대한 액세스를 제어하는 것입니다. Azure Maps 계정을 만들고 Azure AD 테넌트 내에서 Azure Maps Azure AD 애플리케이션을 등록한 후에는 Azure Maps 계정 포털 페이지에서 사용자, 애플리케이션 또는 Azure 리소스에 대한 RBAC를 구성할 수 있습니다. 

Azure Maps는 현재 Azure 리소스에 대한 관리형 ID를 통해 개인 Azure AD 사용자, 애플리케이션 또는 Azure 서비스에 대한 읽기 액세스 제어를 지원합니다.

![개념](./media/azure-maps-authentication/concept.png)

RBAC 설정을 보려면 [Azure Maps에 대한 RBAC 구성 방법](https://aka.ms/amrbac)을 참조하세요.

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 리소스 및 Azure Maps에 대한 관리형 ID

[Azure 리소스에 대한 관리형 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)는 Azure 서비스(Azure App Service, Azure Functions, Virtual Machines 등)에 Azure Maps 서비스에 대한 액세스 권한을 부여할 수 있는 자동 관리형 ID를 제공합니다.  

## <a name="next-steps"></a>다음 단계

* Azure AD 및 Azure Maps를 사용하여 애플리케이션을 인증하는 방법을 자세히 알아보려면 [인증 관리 방법](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)을 참조하세요.

* Azure Maps, 맵 컨트롤 및 Azure AD 인증에 대한 자세한 내용은 [Azure AD 및 Azure Maps 맵 컨트롤](https://aka.ms/amaadmc)을 참조하세요.