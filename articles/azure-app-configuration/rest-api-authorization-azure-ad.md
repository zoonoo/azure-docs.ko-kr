---
title: Azure App Configuration REST API - Azure Active Directory 권한 부여
description: REST API를 사용하여 Azure App Configuration에 대한 권한 부여에 Azure Active Directory를 사용합니다
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092789"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 권한 부여 - REST API 참조

Azure Active Directory(Azure AD) 인증을 사용하는 경우 권한 부여는 RBAC(역할 기반 액세스 제어)에 의해 처리됩니다. RBAC를 사용하여 리소스에 대한 액세스 권한을 부여하려면 사용자를 역할에 할당해야 합니다. 각 역할에는 역할에 할당된 사용자가 수행할 수 있는 일련의 작업이 포함됩니다.

## <a name="roles"></a>역할

Azure 구독에서 기본적으로 사용할 수 있는 역할은 다음과 같습니다.

- **Azure App Configuration 데이터 소유자**: 이 역할은 모든 작업에 대한 모든 권한을 제공합니다.
- **Azure App Configuration 데이터 독자**: 이 역할은 읽기 작업을 사용하도록 설정합니다.

## <a name="actions"></a>동작

역할에는 해당 역할에 할당된 사용자가 수행할 수 있는 작업 목록이 포함됩니다. Azure App Configuration은 다음 작업을 지원합니다.

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: 이 작업은 App Configuration 키-값 리소스(예:/kv 및 /labels)에 대한 읽기 액세스를 허용합니다.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: 이 작업은 App Configuration 키-값 리소스에 대한 쓰기 액세스를 허용합니다.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: 이 작업은 App Configuration 키-값 리소스 삭제를 허용합니다. 리소스를 삭제하면 삭제된 키-값이 반환됩니다.

## <a name="error"></a>Error

```http
HTTP/1.1 403 Forbidden
```

**이유:** 요청을 하는 보안 주체에게 요청된 작업을 수행하는 데 필요한 권한이 없습니다.
**해결 방법:** 요청된 작업을 수행하는 데 필요한 역할을 요청을 하는 보안 주체에게 할당합니다.

## <a name="managing-role-assignments"></a>역할 할당 관리

모든 Azure 서비스에서 표준인 [Azure RBAC 절차](../role-based-access-control/overview.md)를 사용하여 역할 할당을 관리할 수 있습니다. Azure CLI, PowerShell, Azure Portal을 통해 이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.