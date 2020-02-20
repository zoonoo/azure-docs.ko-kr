---
title: Azure Active Directory를 사용 하 여 Azure 앱 구성에 대 한 액세스 권한 부여
description: RBAC를 사용 하 여 Azure 앱 구성 인스턴스에 대 한 액세스 권한 부여
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472623"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 Azure 앱 구성에 대 한 액세스 권한 부여
Azure 앱 구성은 Azure Active Directory (Azure AD)를 사용 하 여 앱 구성 인스턴스에 요청을 권한 부여 하도록 지원 합니다.  Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 보안 주체에 권한을 부여할 수 있습니다.  보안 주체는 사용자 또는 [응용 프로그램 서비스](../active-directory/develop/app-objects-and-service-principals.md)사용자 일 수 있습니다.  역할 및 역할 할당에 대해 자세히 알아보려면 [다른 역할 이해](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="overview"></a>개요
앱 구성 리소스에 액세스 하기 위해 보안 주체 (사용자 또는 응용 프로그램)에 의해 수행 된 요청에는 권한이 있어야 합니다.  Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다.
1. 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다.  토큰을 요청 하는 리소스 이름은 `{tenantID}` 서비스 주체가 속한 Azure Active Directory 테 넌 트 ID와 일치 하는 `https://login.microsoftonline.com/{tenantID}` 됩니다.
2. 토큰은 지정 된 리소스에 대 한 액세스 권한을 부여 하기 위해 앱 구성 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계를 수행 하려면 응용 프로그램 요청이 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다.  응용 프로그램이 Azure Functions 앱, Azure 웹 앱 또는 Azure VM과 같은 Azure 엔터티 내에서 실행 되는 경우 관리 id를 사용 하 여 리소스에 액세스할 수 있습니다.  관리 id에서 구성 Azure 앱 구성에 대 한 요청을 인증 하는 방법을 알아보려면 [Azure 리소스에 대 한 Azure Active Directory 및 관리 되는 id를 사용 하 여 Azure 앱 구성 리소스에 대 한 액세스 인증](howto-integrate-azure-managed-service-identity.md)을 참조 하세요.

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure 앱 구성은 앱 구성 리소스에 대 한 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 제공 되는 사용 권한이 결정 됩니다. RBAC 역할에 대 한 자세한 내용은 [Azure 앱 구성에 대 한 기본 제공 rbac 역할](#built-in-rbac-roles-for-azure-app-configuration)을 참조 하세요. 

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스는 앱 구성 리소스로 범위가 지정 됩니다. Azure AD 보안 주체는 사용자 또는 응용 프로그램 서비스 사용자 이거나 [azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 앱 구성에 대 한 기본 제공 RBAC 역할
Azure는 Azure AD 및 OAuth를 사용 하 여 앱 구성 데이터에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- Azure 앱 구성 데이터 소유자:이 역할을 사용 하 여 앱 구성 리소스에 대 한 읽기/쓰기 권한을 부여할 수 있습니다.
- Azure 앱 구성 데이터 판독기:이 역할을 사용 하 여 앱 구성 리소스에 대 한 읽기 액세스 권한을 부여할 수 있습니다.
- 참가자:이 역할을 사용 하 여 앱 구성 인스턴스에 저장 된 데이터에 대 한 액세스 권한을 부여 하지 않고 서비스에 대 한 관리자 액세스 권한을 부여할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[관리 id](howto-integrate-azure-managed-service-identity.md) 를 사용 하 여 앱 구성 서비스를 관리 하는 방법에 대해 자세히 알아보세요.