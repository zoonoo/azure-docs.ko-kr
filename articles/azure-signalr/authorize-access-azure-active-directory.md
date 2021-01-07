---
title: Azure Active Directory를 사용하여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용 하 여 Azure SignalR Service 리소스에 대 한 액세스 권한을 부여 하는 방법을 설명 합니다.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797517"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 Azure SignalR Service 리소스에 대 한 액세스 권한 부여
Azure SignalR Service는 azure AD (Azure Active Directory)를 사용 하 여 Azure SignalR Service 리소스에 대 한 요청에 권한을 부여할 수 있도록 지원 합니다. Azure AD를 사용 하면 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대해 자세히 알아보려면 [다른 역할 이해](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="overview"></a>개요
보안 주체 (응용 프로그램)가 Azure SignalR Service 리소스에 액세스 하려고 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다. 

 1. 먼저, 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다. 토큰을 요청 하는 리소스 이름은 `https://signalr.azure.com/` 입니다.
 2. 다음으로 토큰은 지정 된 리소스에 대 한 액세스 권한을 부여 하기 위해 Azure SignalR 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계를 수행 하려면 응용 프로그램 요청이 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다. Azure VM, 가상 머신 확장 집합 또는 azure 함수 앱과 같은 Azure 엔터티 내에서 허브 서버를 실행 하는 경우 관리 되는 id를 사용 하 여 리소스에 액세스할 수 있습니다. Azure SignalR Service에 대 한 관리 id의 요청을 인증 하는 방법을 알아보려면 azure [리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 Azure SignalR Service 리소스에 대 한 액세스 인증](authenticate-managed-identity.md)을 참조 하세요. 

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure SignalR Service는 Azure SignalR 리소스에 대 한 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. RBAC 역할에 대 한 자세한 내용은 azure [SignalR Service에 대 한 azure 기본 제공 역할](#azure-built-in-roles-for-azure-signalr-service)을 참조 하세요. 

Azure 엔터티 내에서 실행 되지 않는 SignalR Hub 서버는 Azure AD를 사용 하 여 권한을 부여할 수도 있습니다. 액세스 토큰을 요청 하 고이를 사용 하 여 Azure SignalR Service 리소스에 대 한 요청을 인증 하는 방법을 알아보려면 [응용 프로그램에서 AZURE AD를 사용 하 여 Azure SignalR service에 대 한 액세스 인증](authenticate-application.md)을 참조 하세요. 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 azure 기본 제공 역할

- [SignalR App Server]
- [SignalR Service Reader]
- [SignalR Service Owner]

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure SignalR Service는 azure SignalR Service에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 하 고 리소스에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스의 범위는 구독, 리소스 그룹 또는 Azure SignalR 서비스 리소스의 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 응용 프로그램 또는 [azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 기본 제공 역할
Azure는 azure AD 및 OAuth를 사용 하 여 Azure SignalR Service 리소스에 대 한 액세스 권한을 부여 하기 위한 다음과 같은 Azure 기본 제공 역할을 제공 합니다.

### <a name="signalr-app-server"></a>SignalR App 서버

이 역할을 사용 하 여 클라이언트 토큰을 서명 하기 위한 임시 액세스 키를 가져올 수 있는 액세스 권한을 부여할 수 있습니다.

### <a name="signalr-serverless-contributor"></a>서버를 사용 하지 않는 SignalR 기여자

이 역할을 사용 하 여 Azure SignalR Service에서 직접 클라이언트 토큰을 가져올 수 있는 액세스 권한을 부여할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조 하세요.

- [Azure AD를 사용 하 여 Azure SignalR Service에 액세스 하는 응용 프로그램 인증](authenticate-application.md)
- [Azure AD를 사용 하 여 Azure SignalR Service에 액세스 하는 관리 id 인증](authenticate-managed-identity.md)