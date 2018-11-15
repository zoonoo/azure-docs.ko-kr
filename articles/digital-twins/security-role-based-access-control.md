---
title: Azure Digital Twins 역할 기반 액세스 제어 이해 | Microsoft Docs
description: 역할 기반 액세스 제어를 사용한 Digital Twins의 인증에 대해 알아봅니다.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014791"
---
# <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Digital Twins를 사용하면 공간 그래프의 특정 데이터, 리소스 및 작업에 대해 정확히 액세스 제어할 수 있습니다. 이 작업은 RBAC(역할 기반 액세스 제어)라는 세분화된 역할 및 권한 관리를 통해 수행됩니다. RBAC는 _역할_ 및 _역할 할당_으로 구성됩니다. 역할은 권한 수준을 식별합니다. 역할 할당은 역할을 사용자나 디바이스와 연결합니다.

RBAC를 사용하여 권한을 다음에 부여할 수 있습니다.

- 사용자
- 디바이스
- 서비스 주체
- 사용자 정의 함수 
- 도메인에 속한 모든 사용자 
- 테넌트
 
액세스 수준도 세부적으로 조정할 수 있습니다.

RBAC는 권한이 공간 그래프에서 상속된다는 점에서 고유합니다.

## <a name="what-can-i-do-with-rbac"></a>RBAC로 무엇을 할 수 있나요?

개발자는 RBAC를 다음 용도로 사용할 수 있습니다.

* 사용자에게 전체 건물에 대해서나 특정 방이나 층에 대해서만 디바이스를 관리하는 기능을 부여합니다.
* 관리자에게 전체 그래프의 모든 공간 그래프 노드에 대한 전역 액세스 권한이나 그래프의 특정 섹션에 대한 권한을 부여합니다.
* 지원 전문가에게 액세스 키를 제외하고 그래프에 대한 읽기 액세스 권한을 부여합니다.
* 도메인의 모든 멤버에게 모든 그래프 개체에 대한 읽기 액세스 권한을 부여합니다.

## <a name="rbac-best-practices"></a>RBAC 모범 사례

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>역할

### <a name="role-definitions"></a>역할 정의

역할 정의는 권한 컬렉션이며 때로 역할이라고도 합니다. 역할 정의에는 ‘만들기’, ‘읽기’, ‘업데이트’, ‘삭제’를 포함한 허용되는 작업이 나열됩니다. 또한 이러한 권한이 적용되는 개체 유형을 지정합니다.

Azure Digital Twins에서 다음 역할을 사용할 수 있습니다.

* **공간 관리자**: 지정된 공간 및 아래의 모든 노드에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 글로벌 권한입니다.
* **사용자 관리자**: 사용자 및 사용자 관련 개체에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **장치 관리자**: 디바이스 및 디바이스 관련 개체에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **키 관리자**: 액세스 키에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **토큰 관리자**: 액세스 키에 대한 읽기 및 업데이트 권한. 공간에 대한 읽기 권한입니다.
* **사용자**: 해당 관련 개체를 포함하여 공간, 센서 및 사용자에 대한 읽기 권한.
* **지원 전문가**: 액세스 키를 제외한 모든 것에 대한 읽기 권한.
* **디바이스 설치자**: 해당 관련 개체를 포함하여 디바이스 및 센서에 대한 읽기 및 업데이트 권한. 공간에 대한 읽기 권한입니다.
* **게이트웨이 장치**: 센서에 대한 만들기 권한. 해당 관련 개체를 포함하여 디바이스 및 센서에 대한 읽기 권한.

>[!NOTE]
> 이전 역할에 대한 전체 정의를 검색하려면 시스템/역할 API를 쿼리합니다.

### <a name="object-types"></a>개체 유형

`ObjectIdType`은 역할이 부여되는 ID의 유형을 나타냅니다. `DeviceId` 및 `UserDefinedFunctionId` 유형 외에도, 이 유형은 Azure AD(Azure Active Directory) 개체의 속성에 해당합니다.
  
* `UserId` 유형은 사용자에게 역할을 할당합니다.
* `DeviceId` 유형은 장치에 역할을 할당합니다.
* `DomainName` 유형은 도메인 이름에 역할을 할당합니다. 지정된 도메인 이름을 가진 각 사용자는 해당 역할의 액세스 권한을 가집니다.
* `TenantId` 유형은 테넌트에 역할을 할당합니다. 지정된 Azure AD 테넌트 ID에 속한 각 사용자는 해당 역할의 액세스 권한을 가집니다.
* `ServicePrincipalId` 유형은 서비스 주체 개체 ID에 역할을 할당합니다.
* `UserDefinedFunctionId` 유형은 UDF(사용자 정의 함수)에 역할을 할당합니다.

> [!div class="nextstepaction"]
> [사용자에 대한 쿼리 또는 개체 ID](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [서비스 주체에 대한 개체 ID 가져오기](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Azure AD 테넌트에 대한 개체 ID 검색](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>역할 할당

받는 사람에게 권한을 부여하려면 역할 할당을 만듭니다. 사용 권한을 취소하려면 역할 할당을 제거합니다. Azure Digital Twins 역할 할당은 개체(사용자, Azure AD 테넌트 등)를 역할 및 공간에 연결합니다. 권한은 해당 공간에 속하는 모든 개체에 부여됩니다. 공간은 그 아래의 전체 공간 그래프를 포함합니다.

예를 들어, 사용자에게 건물을 나타내는 공간 그래프의 루트 노드에 대한 `DeviceInstaller` 역할로 역할 할당을 부여한 경우 그러면 이 사용자는 해당 노드와, 건물 내 모든 다른 하위 공간에 대해 디바이스를 읽고 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참고하세요.
