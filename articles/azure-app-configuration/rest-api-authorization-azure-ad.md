---
title: Azure 앱 Configuration REST API-Azure Active Directory 권한 부여
description: REST API를 사용 하 여 Azure 앱 구성에 대 한 권한 부여를 위해 Azure Active Directory를 사용 합니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 89c51e69a4274affcecb4d967deb96dcebcfd70f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253373"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 권한 부여-REST API 참조

Azure Active Directory (Azure AD) 인증을 사용 하는 경우 권한 부여는 RBAC (역할 기반 액세스 제어)에 의해 처리 됩니다. RBAC를 사용 하려면 리소스에 대 한 액세스 권한을 부여 하기 위해 사용자를 역할에 할당 해야 합니다. 각 역할에는 역할에 할당 된 사용자가 수행할 수 있는 일련의 작업이 포함 됩니다.

## <a name="roles"></a>역할

Azure 구독에서 기본적으로 사용할 수 있는 역할은 다음과 같습니다.

- **Azure 앱 구성 데이터 소유자**:이 역할은 모든 작업에 대 한 모든 권한을 제공 합니다.
- **Azure 앱 구성 데이터 판독기**:이 역할은 읽기 작업을 사용 하도록 설정 합니다.

## <a name="actions"></a>동작

역할에는 해당 역할에 할당 된 사용자가 수행할 수 있는 작업 목록이 포함 됩니다. Azure 앱 구성은 다음 작업을 지원 합니다.

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`:이 작업을 통해 앱 구성 키-값 리소스 (예:/kv 및/slalas)에 대 한 읽기 액세스를 허용 합니다.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`:이 작업은 앱 구성 키-값 리소스에 대 한 쓰기 액세스를 허용 합니다.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`:이 작업을 수행 하면 앱 구성 키-값 리소스를 삭제할 수 있습니다. 리소스를 삭제 하면 삭제 된 키 값이 반환 됩니다.

## <a name="error"></a>오류

```http
HTTP/1.1 403 Forbidden
```

**이유:** 요청을 수행 하는 보안 주체에 게 요청 된 작업을 수행 하는 데 필요한 권한이 없습니다.
**해결 방법:** 요청 된 작업을 수행 하는 데 필요한 역할을 요청 하는 보안 주체에 할당 합니다.

## <a name="managing-role-assignments"></a>역할 할당 관리

모든 Azure 서비스에서 standard 인 [RBAC 절차](https://docs.microsoft.com/azure/role-based-access-control/overview) 를 사용 하 여 역할 할당을 관리할 수 있습니다. Azure CLI, PowerShell 및 Azure Portal를 통해이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 참조 하세요.
