---
title: Azure Active Directory를 사용하여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한을 부여하는 방법을 설명합니다.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797517"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한 부여
Azure SignalR Service는 Azure AD(Azure Active Directory)를 사용하여 Azure SignalR Service 리소스에 대한 요청에 권한을 부여할 수 있도록 지원합니다. Azure AD를 사용하면 RBAC(역할 기반 액세스 제어)를 통해 사용자 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대한 자세한 내용은 [다양한 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="overview"></a>개요
보안 주체(애플리케이션)가 Azure SignalR Service 리소스에 액세스하려고 시도하는 경우 요청에 권한이 부여되어야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 

 1. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 토큰을 요청하는 리소스 이름은 `https://signalr.azure.com/`입니다.
 2. 그 다음, 토큰은 지정된 리소스에 대한 액세스 권한을 부여하기 위해 Azure SignalR Service에 요청의 일부로 전달됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰을 포함해야 합니다. Azure VM, 가상 머신 확장 집합 또는 Azure 함수 앱과 같은 Azure 엔터티 내에서 허브 서버가 실행되는 경우 관리 ID를 사용하여 리소스에 액세스할 수 있습니다. Azure SignalR Service에 대한 관리 ID의 요청을 인증하는 방법에 대한 자세한 내용은 [Azure Active Directory 및 Azure 리소스 관리 ID를 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한 부여](authenticate-managed-identity.md)를 참조하세요. 

권한 부여 단계를 수행하려면 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure SignalR Service는 Azure SignalR 리소스에 대한 권한 세트를 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가지는 권한이 결정됩니다. BAC 역할에 대한 자세한 내용은 [Azure SignalR Service에 대한 Azure 기본 제공 역할](#azure-built-in-roles-for-azure-signalr-service)을 참조하세요. 

Azure 엔터티 내에서 실행되지 않는 SignalR Hub Server는 Azure AD를 사용하여 권한을 부여할 수도 있습니다. 액세스 토큰을 요청하고 Azure SignalR Service 리소스에 대한 요청에 권한을 부여하는 데 사용하는 방법을 알아보려면 [애플리케이션에서 Azure AD를 사용하여 Azure SignalR Service에 대한 액세스 인증](authenticate-application.md)을 참조하세요. 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service에 대한 Azure 기본 제공 역할

- [SignalR 앱 서버]
- [SignalR Service 읽기 권한자]
- [SignalR Service 소유자]

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 대한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure SignalR Service는 Azure SignalR Service에 액세스하는 데 사용되는 일반 권한 집합이 포함된 Azure 기본 제공 역할 집합을 정의하며 리소스에 액세스하기 위한 사용자 지정 역할도 정의할 수 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당되면 Azure에서 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 범위는 구독, 리소스 그룹 또는 Azure SignalR Service 리소스 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 애플리케이션 또는 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service에 대한 기본 제공 역할
Azure는 Azure AD 및 OAuth를 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한을 부여하기 위해 다음과 같은 Azure 기본 제공 역할을 제공합니다.

### <a name="signalr-app-server"></a>SignalR 앱 서버

이 역할을 사용하여 클라이언트 토큰 서명을 위한 임시 액세스 키 가져오기 액세스 권한을 부여합니다.

### <a name="signalr-serverless-contributor"></a>SignalR 서버리스 기여자

이 역할을 사용하여 Azure SignalR Service에서 직접 클라이언트 토큰을 가져올 수 있는 액세스 권한을 부여합니다.

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.

- [Azure AD를 사용하여 Azure SignalR Service에 액세스하는 애플리케이션 인증](authenticate-application.md)
- [Azure AD를 사용하여 Azure SignalR Service에 액세스하는 관리 ID 인증](authenticate-managed-identity.md)