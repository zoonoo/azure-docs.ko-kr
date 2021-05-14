---
title: Media Services 계정에 대한 역할 기반 액세스 제어
description: 이 문서에서는 Azure Media Services 계정에 대한 Azure RBAC(Azure 역할 기반 액세스 제어)를 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c086fe6a47227901cd44c4684452c842df5286ae
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064429"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Media Services 계정에 대한 Azure RBAC(Azure 역할 기반 액세스 제어)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

현재 Azure Media Services는 서비스와 관련된 사용자 지정 역할을 정의하지 않습니다. Media Services 계정에 대한 모든 권한을 가져오려면 고객은 **소유자** 또는 **참가자** 의 기본 제공 역할을 사용하면 됩니다. 이러한 역할 간의 주요 차이점은 **소유자** 는 리소스에 대한 액세스 권한이 있는 사용자를 제어할 수 있지만 **참가자** 는 제어할 수 없다는 것입니다. 기본 제공 **Reader** 역할도 사용할 수 있지만 사용자 또는 애플리케이션에는 Media Services API에 대한 읽기 권한만 있습니다. 

## <a name="design-principles"></a>디자인 원칙

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 API는 **Get** 또는 **List** 작업에서 비밀 또는 자격 증명을 반환하지 않습니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 사용자는 비밀 또는 자격 증명을 가져오는 별도의 작업 메서드를 호출해야 합니다. **Reader** 역할은 Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets와 같은 작업을 호출할 수 없습니다. 별도의 작업이 있으면 원하는 경우 사용자 지정 역할에서 보다 세분화된 Azure RBAC 보안 권한을 설정할 수 있습니다.

Media Services에서 지원하는 작업을 나열하려면 다음을 수행합니다.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[기본 제공 역할 정의](../../role-based-access-control/built-in-roles.md) 문서에서는 역할이 부여하는 내용을 정확히 알려줍니다. 

자세한 내용은 다음 문서를 참조하세요.

- [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [REST API를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-rest.md)
- [Media Services 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>다음 단계

- [Media Services v3 API를 사용하여 개발](media-services-apis-overview.md)
- [Media Services .NET을 사용하여 콘텐츠 키 정책 가져오기](drm-get-content-key-policy-dotnet-how-to.md)
