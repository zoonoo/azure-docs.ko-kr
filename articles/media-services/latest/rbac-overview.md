---
title: 미디어 서비스 계정에 대한 역할 기반 액세스 제어 - Azure | 마이크로 소프트 문서
description: 이 문서에서는 Azure Media Services 계정에 대한 RBAC(역할 기반 액세스 제어)에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236911"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>미디어 서비스 계정에 대한 역할 기반 액세스 제어(RBAC)

현재 Azure Media 서비스는 서비스와 관련된 사용자 지정 역할을 정의하지 않습니다. 미디어 서비스 계정에 대한 전체 액세스 권한을 얻으려면 고객은 **소유자** 또는 **기여자의**기본 제공 역할을 사용할 수 있습니다. 이러한 역할의 주요 차이점은 **소유자가** 리소스에 액세스할 수 있는 사람을 제어할 수 있고 **참여자는** 액세스할 수 없다는 것입니다. 기본 제공 **Reader** 역할도 사용할 수 있지만 사용자 또는 응용 프로그램은 미디어 서비스 API에 대한 읽기 액세스 권한만 갖습니다. 

## <a name="design-principles"></a>디자인 원칙

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 API는 **Get** 또는 **List** 작업에서 비밀 또는 자격 증명을 반환하지 않습니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 사용자는 비밀 또는 자격 증명을 얻으려면 별도의 작업 메서드를 호출해야 합니다. **리더** 역할은 Asset.ListContainerSas, 스트리밍로케이터.List콘텐츠키, 콘텐츠키정책.GetPolicyPropertiesWithSecrets와 같은 작업을 호출할 수 없습니다. 별도의 작업을 사용하면 원하는 경우 사용자 지정 역할에 보다 세분화된 RBAC 보안 권한을 설정할 수 있습니다.

Media Services가 지원하는 작업을 나열하려면 다음을 수행합니다.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[기본 제공 역할 정의](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 문서에서는 역할이 부여하는 내용을 정확히 알려줍니다. 

자세한 내용은 다음 아티클을 참조하세요.

- [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Azure 리소스에 대한 RBAC란 무엇입니까?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [RBAC를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [미디어 서비스 리소스 공급자 작업](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>다음 단계

- [미디어 서비스 v3 API로 개발](media-services-apis-overview.md)
- [미디어 서비스 .NET을 사용하여 콘텐츠 키 정책 받기](get-content-key-policy-dotnet-howto.md)
