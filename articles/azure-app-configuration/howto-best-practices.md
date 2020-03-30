---
title: Azure 앱 구성 모범 사례 | 마이크로 소프트 문서
description: Azure 앱 구성을 가장 잘 사용하는 방법 알아보기
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348672"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 앱 구성 모범 사례

이 문서에서는 Azure 앱 구성을 사용할 때의 일반적인 패턴 및 모범 사례에 대해 설명합니다.

## <a name="key-groupings"></a>키 그룹화

앱 구성은 키를 구성하기 위한 두 가지 옵션을 제공합니다.

* 주요 접두사
* 레이블

하나 또는 두 가지 옵션을 사용하여 키를 그룹화할 수 있습니다.

*키 접두사는* 키의 시작 부분입니다. 이름에 동일한 접두사를 사용하여 키 집합을 논리적으로 그룹화할 수 있습니다. 접두사는 URL 경로와 `/`유사하게 구분 기호에 의해 연결된 여러 구성 요소를 포함하여 네임스페이스를 형성할 수 있습니다. 이러한 계층 구조는 하나의 앱 구성 저장소에 많은 응용 프로그램, 구성 요소 서비스 및 환경에 대한 키를 저장할 때 유용합니다.

중요한 점은 키가 해당 설정의 값을 검색하기 위해 응용 프로그램 코드가 참조하는 것입니다. 키는 변경되지 않아야 하며, 그렇지 않으면 발생할 때마다 코드를 수정해야 합니다.

*레이블은* 키의 특성입니다. 키의 변형을 만드는 데 사용됩니다. 예를 들어 여러 버전의 키에 레이블을 할당할 수 있습니다. 버전은 반복, 환경 또는 기타 컨텍스트 정보일 수 있습니다. 응용 프로그램은 다른 레이블을 지정하여 완전히 다른 키 값 집합을 요청할 수 있습니다. 따라서 모든 키 참조는 코드에서 변경되지 않습니다.

## <a name="key-value-compositions"></a>키-값 컴포지션

앱 구성은 함께 저장된 모든 키를 독립 엔터티로 처리합니다. 앱 구성은 키 간의 관계를 추론하거나 계층 구조에 따라 키 값을 상속하려고 시도하지 않습니다. 그러나 응용 프로그램 코드에 적절한 구성 스태킹과 결합된 레이블을 사용하여 여러 키 집합을 집계할 수 있습니다.

예제를 살펴보겠습니다. 개발 환경에 따라 값이 달라질 수 있는 **Asset1이라는**설정이 있다고 가정합니다. 빈 레이블과 "개발"이라는 레이블이 있는 "Asset1"이라는 키를 만듭니다. 첫 번째 레이블에서 **Asset1에**대 한 기본값을 넣고 후자에 "개발"에 대 한 특정 값을 넣습니다.

코드에서 먼저 레이블 없이 키 값을 검색한 다음 "개발" 레이블을 사용하면 동일한 키 값 집합을 두 번째로 검색합니다. 두 번째로 값을 검색하면 키의 이전 값이 덮어씁입니다. .NET Core 구성 시스템을 사용하면 여러 구성 데이터 집합을 서로 위에 "스택"할 수 있습니다. 키가 두 개 이상의 집합에 있는 경우 키가 포함된 마지막 집합이 사용됩니다. .NET Core와 같은 최신 프로그래밍 프레임워크를 사용하면 기본 구성 공급자를 사용하여 앱 구성에 액세스하는 경우 이 스태킹 기능을 무료로 사용할 수 있습니다. 다음 코드 조각은 .NET Core 응용 프로그램에서 스태킹을 구현하는 방법을 보여 주며 다음과 같습니다.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[레이블을 사용하여 다양한 환경에 대해 서로 다른 구성을 사용하도록](./howto-labels-aspnet-core.md) 설정하면 완전한 예제를 볼 수 있습니다.

## <a name="app-configuration-bootstrap"></a>앱 구성 부트스트랩

앱 구성 저장소에 액세스하려면 Azure 포털에서 사용할 수 있는 연결 문자열을 사용할 수 있습니다. 연결 문자열에는 자격 증명 정보가 포함되어 있으므로 암호로 간주됩니다. 이러한 비밀은 Azure Key Vault에 저장되어야 하며 코드는 키 볼트를 인증하여 검색해야 합니다.

더 나은 옵션은 Azure Active Directory에서 관리되는 ID 기능을 사용하는 것입니다. 관리되는 ID를 사용하면 앱 구성 저장소에 대한 액세스를 부트스트랩하기 위해 앱 구성 끝점 URL만 있으면 됩니다. 응용 프로그램 코드에 URL을 포함할 수 있습니다(예: *appsettings.json* 파일). 자세한 내용은 [Azure 관리 ID와 통합을](howto-integrate-azure-managed-service-identity.md) 참조하십시오.

## <a name="app-or-function-access-to-app-configuration"></a>앱 구성에 대한 앱 또는 기능 액세스

다음 방법 중 다음 을 사용하여 웹 앱 또는 기능에 대한 앱 구성에 대한 액세스를 제공할 수 있습니다.

* Azure 포털을 통해 앱 서비스의 응용 프로그램 설정에서 앱 구성 저장소에 연결 문자열을 입력합니다.
* 연결 문자열을 키 볼트의 앱 구성 저장소에 저장하고 [앱 서비스에서 참조합니다.](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)
* Azure 관리 ID를 사용하여 앱 구성 저장소에 액세스합니다. 자세한 내용은 [Azure 관리 ID와의 통합을 참조하십시오.](howto-integrate-azure-managed-service-identity.md)
* 앱 구성에서 앱 서비스로 구성을 푸시합니다. 앱 구성은 앱을 통해 직접 데이터를 전송하는 내보내기 기능(Azure Portal 및 Azure CLI)을 제공합니다. 이 방법을 사용하면 응용 프로그램 코드를 전혀 변경할 필요가 없습니다.

## <a name="reduce-requests-made-to-app-configuration"></a>앱 구성에 대한 요청 감소

앱 구성에 대한 과도한 요청으로 인해 제한 또는 초과 요금이 발생할 수 있습니다. 요청 횟수를 줄이려면 다음을 수행하십시오.

* 특히 구성 값이 자주 변경되지 않는 경우 새로 고침 시간 지정 시간을 늘립니다. 메서드를 사용하여 새 새로 고침 시간 지정 시간 지정합니다. [ `SetCacheExpiration` ](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)

* 개별 키를 보는 대신 하나의 *센티넬 키를*시청합니다. 센티넬 키가 변경된 경우에만 모든 구성을 새로 고칩니다. 예를 들어 [ASP.NET Core 앱에서 동적 구성 사용을](enable-dynamic-configuration-aspnet-core.md) 참조하십시오.

* Azure Event Grid를 사용하여 변경 내용을 지속적으로 폴링하는 대신 구성이 변경될 때 알림을 받습니다. 자세한 내용은 [Azure 앱 구성 이벤트를 웹 끝점으로 라우팅 을](./howto-app-configuration-event.md) 참조하세요.

## <a name="importing-configuration-data-into-app-configuration"></a>구성 데이터를 앱 구성으로 가져오기

앱 구성은 Azure 포털 또는 CLI를 사용하여 현재 구성 파일에서 구성 설정을 대량 [가져오는](https://aka.ms/azconfig-importexport1) 옵션을 제공합니다. 동일한 옵션을 사용하여 관련 상점 간에 예를 들어 App Configuration에서 값을 내보낼 수도 있습니다. GitHub 리포지토리와 지속적인 동기화를 설정하려는 경우 [GitHub 작업을](https://aka.ms/azconfig-gha2) 사용하여 기존 소스 제어 방법을 계속 사용하는 동시에 앱 구성의 이점을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)
