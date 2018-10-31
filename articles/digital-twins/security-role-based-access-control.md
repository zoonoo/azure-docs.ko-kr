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
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323956"
---
# <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Digital Twins를 사용하면 공간 그래프의 특정 데이터, 리소스 및 작업에 대해 정확히 액세스 제어할 수 있습니다. 이 작업은 ‘역할 기반 액세스 제어’라는 세분화된 역할 및 권한 관리를 통해 수행됩니다. 역할 기반 액세스 제어는 ‘역할’ 또는 권한 수준과 ‘역할 할당’ 또는 사용자나 장치에 대한 역할 연결로 구성됩니다.

역할 기반 액세스 제어를 사용하면 사용자, 장치, 서비스 주체, 사용자 정의 함수, 도메인에 속한 모든 사용자 또는 테넌트에 권한을 부여할 수 있습니다. 또한 액세스 수준을 세부적으로 조정할 수도 있습니다.

역할 기반 액세스 제어는 권한이 공간 그래프에서 상속된다는 점에서 고유합니다.

## <a name="what-can-i-do-with-role-based-access-control"></a>역할 기반 액세스 제어로 할 수 있는 작업은 무엇인가요?

개발자는 역할 기반 액세스 제어를 사용하여 다음을 수행할 수 있습니다.

* 사용자에게 전체 건물에 대해서나 특정 방이나 층에 대해서만 장치를 관리하는 기능을 부여합니다.
* 관리자에게 전체 그래프의 모든 공간 그래프 노드에 대한 전역 액세스 권한이나 그래프의 특정 섹션에 대한 권한을 부여합니다.
* 지원 전문가에게 액세스 키를 제외하고 그래프에 대한 읽기 액세스 권한을 부여합니다.
* 도메인의 모든 멤버에게 모든 그래프 개체에 대한 읽기 액세스 권한을 부여합니다.

## <a name="role-based-access-control-best-practices"></a>역할 기반 액세스 제어 모범 사례

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>역할

### <a name="role-definitions"></a>역할 정의

**역할 정의**는 권한 컬렉션이며 때로 **역할**이라고도 합니다. 역할 정의에는 ‘만들기’, ‘읽기’, ‘업데이트’, ‘삭제’를 포함한 허용되는 작업이 나열됩니다. 또한 이러한 권한이 적용되는 개체 유형을 지정합니다.

Azure Digital Twins에서 다음 역할을 사용할 수 있습니다.

* **공간 관리자**: 지정된 공간 및 아래의 모든 노드에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 글로벌 권한입니다.
* **사용자 관리자**: 사용자 및 사용자 관련 개체에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **장치 관리자**: 장치 및 장치 관련 개체에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **키 관리자**: 액세스 키에 대한 만들기, 읽기, 업데이트 및 삭제 권한. 공간에 대한 읽기 권한입니다.
* **토큰 관리자**: 액세스 키에 대한 읽기 및 업데이트 권한. 공간에 대한 읽기 권한입니다.
* **사용자**: 해당 관련 개체를 포함하여 공간, 센서 및 사용자에 대한 읽기 권한.
* **지원 전문가**: 액세스 키를 제외한 모든 것에 대한 읽기 권한.
* **장치 설치자**: 해당 관련 개체를 포함하여 장치 및 센서에 대한 읽기 및 업데이트 권한. 공간에 대한 읽기 권한입니다.
* **게이트웨이 장치**: 센서에 대한 만들기 권한. 해당 관련 개체를 포함하여 장치 및 센서에 대한 읽기 권한입니다.

>[!NOTE]
> ‘위의 전체 정의는 시스템/역할 API를 조회하여 검색할 수 있습니다.’

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

역할 할당을 만들어 받는 사람에게 권한을 부여하고, 역할 할당을 제거하여 권한을 취소합니다. Azure Digital Twins 역할 할당은 개체(사용자, Azure AD 테넌트 등), 역할 및 공간을 연결합니다. 그런 다음, 공간(아래의 전체 공간 그래프 포함)에 속한 모든 개체에 권한을 부여합니다.

예를 들어, 사용자에게 건물을 나타내는 공간 그래프의 루트 노드에 대한 `DeviceInstaller` 역할로 역할 할당을 부여한 경우 이 사용자는 해당 노드뿐 아니라 건물 내의 다른 모든 하위 공간에 대해서도 장치를 읽고 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참고하세요.
