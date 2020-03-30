---
title: Azure Active 디렉터리를 사용하여 Azure 앱 구성에 대한 액세스 권한 부여
description: RBAC에서 Azure 앱 구성 인스턴스에 대한 액세스 권한을 부여할 수 있습니다.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472623"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Azure Active 디렉터리를 사용하여 Azure 앱 구성에 대한 액세스 권한 부여
Azure 앱 구성은 Azure Active Directory(Azure AD)를 사용하여 앱 구성 인스턴스에 대한 요청을 승인합니다.  Azure AD를 사용하면 RBAC(역할 기반 액세스 제어)를 사용하여 보안 주체에게 권한을 부여할 수 있습니다.  보안 주체는 사용자 또는 [응용 프로그램 서비스 주체일](../active-directory/develop/app-objects-and-service-principals.md)수 있습니다.  역할 및 역할 할당에 대해 자세히 알아보려면 [다른 역할 이해를](../role-based-access-control/overview.md)참조하십시오.

## <a name="overview"></a>개요
앱 구성 리소스에 액세스하기 위해 보안 주체(사용자 또는 응용 프로그램)가 요청한 권한이 있어야 합니다.  Azure AD를 사용하면 리소스에 대한 액세스는 2단계 프로세스입니다.
1. 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다.  토큰을 요청하는 리소스 이름은 `https://login.microsoftonline.com/{tenantID}` `{tenantID}` 서비스 주체가 속한 Azure Active Directory 테넌트 ID와 일치하는 위치입니다.
2. 토큰은 지정된 리소스에 대한 액세스를 승인하기 위한 앱 구성 서비스에 대한 요청의 일부로 전달됩니다.

인증 단계에서는 응용 프로그램 요청에 런타임시 OAuth 2.0 액세스 토큰이 포함되어 야 합니다.  응용 프로그램이 Azure Functions 앱, Azure Web App 또는 Azure VM과 같은 Azure 엔터티 내에서 실행 중인 경우 관리되는 ID를 사용하여 리소스에 액세스할 수 있습니다.  관리되는 ID에서 Azure 앱 구성으로 요청을 인증하는 방법을 알아보려면 [Azure Active Directory를 사용하여 Azure 앱 구성 리소스에 대한 액세스 인증 및 Azure 리소스에 대한 관리되는 ID를](howto-integrate-azure-managed-service-identity.md)참조하세요.

권한 부여 단계에서는 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure 앱 구성은 앱 구성 리소스에 대한 사용 권한 집합을 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체에 제공된 사용 권한이 결정됩니다. RBAC 역할에 대한 자세한 내용은 [Azure 앱 구성에 대한 기본 제공 RBAC 역할을](#built-in-rbac-roles-for-azure-app-configuration)참조하십시오. 

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스는 앱 구성 리소스로 범위가 조정됩니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자 또는 응용 프로그램 서비스 보안 주체 또는 [관리되는 ID일](../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 앱 구성을 위한 기본 제공 RBAC 역할
Azure는 Azure AD 및 OAuth를 사용하여 앱 구성 데이터에 대한 액세스를 승인하기 위한 다음과 같은 기본 제공 RBAC 역할을 제공합니다.

- Azure 앱 구성 데이터 소유자: 이 역할을 사용하여 앱 구성 리소스에 대한 읽기/쓰기 액세스 권한을 부여합니다.
- Azure 앱 구성 데이터 판독기: 이 역할을 사용하여 앱 구성 리소스에 대한 읽기 액세스 권한을 부여합니다.
- 기여자: 이 역할을 사용하여 앱 구성 인스턴스에 저장된 데이터에 대한 액세스 권한을 부여하지 않고 관리자에게 서비스에 대한 액세스 권한을 부여합니다.

## <a name="next-steps"></a>다음 단계
[관리되는 ID를](howto-integrate-azure-managed-service-identity.md) 사용하여 앱 구성 서비스를 관리하는 방법에 대해 자세히 알아보세요.