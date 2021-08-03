---
title: Azure Active Directory를 사용하여 Azure App Configuration에 대한 액세스 권한 부여
description: Azure RBAC를 사용하여 Azure App Configuration 인스턴스에 대한 액세스 권한 부여
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: aad92516841be1fc8552bbea8fc6b256cd080160
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112028994"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure App Configuration에 대한 액세스 권한 부여
HMAC(해시 기반 메시지 인증 코드)를 사용하는 것 외에도 Azure App Configuration 지원은 Azure AD(Azure Active Directory)를 사용하여 App Configuration 인스턴스 요청에 권한을 부여할 수 있습니다.  Azure AD에서는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 보안 주체에 권한을 부여할 수 있습니다.  보안 주체는 사용자, [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 또는 [애플리케이션 서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)일 수 있습니다.  역할 및 역할 할당에 대한 자세한 내용은 [다양한 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="overview"></a>개요
App Configuration 리소스에 액세스하기 위한 보안 주체의 요청에는 권한이 부여되어야 합니다. Azure AD를 사용하는 경우 리소스에 액세스하는 과정은 다음의 2단계 프로세스로 진행됩니다.
1. 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다.  토큰을 요청하는 리소스 이름은 `{tenantID}`가 서비스 사용자의 Azure Active Directory 테넌트 ID와 일치하는 `https://login.microsoftonline.com/{tenantID}`입니다.
2. 토큰은 지정된 리소스에 대한 액세스 권한을 부여하기 위해 App Configuration 서비스에 요청의 일부로 전달됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰을 포함해야 합니다.  애플리케이션이 Azure Functions 앱, Azure 웹앱 또는 Azure VM과 같은 Azure 엔터티 내에서 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다.  Azure App Configuration에 대한 관리 ID의 요청을 인증하는 방법에 대한 자세한 내용은 [Azure Active Directory 및 Azure 리소스 관리 ID를 사용하여 Azure App Configuration 리소스에 대한 액세스 권한 부여](howto-integrate-azure-managed-service-identity.md)를 참조하세요.

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure App Configuration은 App Configuration 리소스에 대한 권한 세트를 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체에 제공되는 권한이 결정됩니다. Azure 역할에 대한 자세한 내용은 [Azure App Configuration에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-app-configuration)을 참조하세요. 

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한에 대한 Azure 역할 할당
Azure AD(Azure Active Directory)는 [Azure RBAC](../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다.

Azure AD 보안 주체에 Azure 역할을 할당하는 경우 Azure는 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 범위는 App Configuration 리소스에 지정됩니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Azure App Configuration에 대한 Azure 기본 제공 역할
Azure는 Azure AD를 사용하여 App Configuration 데이터에 대한 액세스 권한을 부여하기 위해 다음과 같은 Azure 기본 제공 역할을 제공합니다.

- **App Configuration Data 소유자**: 이 역할을 사용하여 App Configuration 데이터에 대한 읽기/쓰기/삭제 액세스 권한을 부여합니다. App Configuration 리소스에 대한 액세스 권한은 부여하지 않습니다.
- **App Configuration Data 읽기 권한자**: 이 역할을 사용하여 App Configuration 데이터에 대한 읽기 권한을 부여합니다. App Configuration 리소스에 대한 액세스 권한은 부여하지 않습니다.
- **참가자** 또는 **소유자**: 이 역할을 사용하여 App Configuration 리소스를 관리합니다. 리소스의 액세스 키에 대한 액세스 권한을 부여합니다. 액세스 키를 사용하여 App Configuration 데이터에 액세스할 수 있지만 이 역할은 Azure AD를 사용하여 데이터에 직접 액세스하는 것을 허용하지 않습니다.
- **읽기 권한자**: 이 역할을 사용하여 App Configuration 리소스에 대한 읽기 권한을 부여합니다. 리소스의 액세스 키와 App Configuration에 저장된 데이터에 대한 액세스 권한은 부여하지 않습니다.

> [!NOTE]
> ID에 역할이 할당되면 이 ID를 사용하여 App Configuration에 저장된 데이터에 액세스하기 전 권한이 전파되는 데 최대 15분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
[관리 ID](howto-integrate-azure-managed-service-identity.md)를 사용하여 App Configuration 서비스를 관리하는 방법에 대해 자세히 알아보세요.
