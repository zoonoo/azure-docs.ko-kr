---
title: Communication Services에서 관리되는 ID 사용
titleSuffix: An Azure Communication Services quickstart
description: 관리 ID를 사용하면 Azure VM, 함수 앱, 기타 리소스에서 실행되는 애플리케이션에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110757"
---
# <a name="use-managed-identities"></a>관리 ID 사용
관리 ID를 사용하여 Azure Communication Services를 시작하세요. Communication Services ID 및 SMS SDK는 [Azure 리소스용 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 Azure AD(Azure Active Directory) 인증을 지원합니다.

이 빠른 시작에서는 관리 ID를 지원하는 Azure 환경에서 ID 및 SMS SDK에 대한 액세스 권한을 부여하는 방법을 보여 줍니다. 또한 개발 환경에서 코드를 테스트하는 방법도 설명합니다.

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

- [Azure 역할 기반 액세스 제어에 대해 자세히 알아보기](../../../articles/role-based-access-control/index.yml)
- [.NET용 Azure ID 라이브러리에 대해 자세히 알아보기](/dotnet/api/overview/azure/identity-readme)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [SMS 메시지 보내기](../quickstarts/telephony-sms/send.md)
- [SMS에 대한 자세한 정보](../concepts/telephony-sms/concepts.md)
