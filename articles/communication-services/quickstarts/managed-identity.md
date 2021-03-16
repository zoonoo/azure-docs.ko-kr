---
title: 통신 서비스에서 관리 되는 id 사용
titleSuffix: An Azure Communication Services quickstart
description: 관리 id를 사용 하면 azure Vm, 함수 앱 및 기타 리소스에서 실행 되는 응용 프로그램에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492356"
---
# <a name="use-managed-identities"></a>관리 ID 사용
관리 id를 사용 하 여 Azure 통신 서비스를 시작 하세요. 통신 서비스 Id 및 SMS 클라이언트 라이브러리는 [azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 azure AD (Azure Active Directory) 인증을 지원 합니다.

이 빠른 시작에서는 관리 되는 id를 지 원하는 Azure 환경에서 Id 및 SMS 클라이언트 라이브러리에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다. 개발 환경에서 코드를 테스트 하는 방법에 대해서도 설명 합니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

- [Azure 역할 기반 access control에 대 한 자세한 정보](../../../articles/role-based-access-control/index.yml)
- [.NET 용 Azure id 라이브러리에 대해 자세히 알아보기](/dotnet/api/overview/azure/identity-readme)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [SMS 메시지 보내기](../quickstarts/telephony-sms/send.md)
- [SMS에 대한 자세한 정보](../concepts/telephony-sms/concepts.md)
