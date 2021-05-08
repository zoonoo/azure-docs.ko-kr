---
title: Azure App Configuration 모범 사례 | Microsoft Docs
description: Azure App Configuration 사용의 모범 사례 알아보기. 이 항목에서는 키 그룹화, 키-값 컴포지션, App Configuration 부트스트랩 등을 다룹니다.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371552"
---
# <a name="azure-app-configuration-best-practices"></a>Azure App Configuration 모범 사례

이 문서에서는 Azure App Configuration 사용의 일반적인 패턴 및 모범 사례에 대해 설명합니다.

## <a name="key-groupings"></a>키 그룹화

App Configuration은 다음과 같이 키를 구성하는 두 가지 옵션을 제공합니다.

* 키 접두사
* 레이블

두 옵션 중 하나 또는 두 옵션 모두를 사용하여 키를 그룹화할 수 있습니다.

*키 접두사* 는 키의 시작 부분입니다. 키의 이름에 동일한 키 접두사를 사용하여 키 집합을 논리적으로 그룹화할 수 있습니다. 키 접두사는 네임스페이스를 구성하기 위해 `/`과 같이 URL 경로와 유사한 구분 기호로 연결된 여러 구성 요소를 포함할 수 있습니다. 해당 계층은 하나의 App Configuration 저장소에 많은 애플리케이션, 구성 요소 서비스 및 환경에 대한 키를 저장하는 경우에 유용합니다.

명심해야 할 점은 키가 애플리케이션 코드에서 해당 설정의 값을 검색하기 위해 참조하는 것이라는 것입니다. 키를 변경해서는 안 되고, 변경 시에는 코드를 수정해야 합니다.

*레이블* 은 키에 대한 특성입니다. 레이블은 키의 변형을 만드는 데 사용됩니다. 예를 들어, 여러 버전의 키에 레이블을 할당할 수 있습니다. 키의 버전은 반복, 환경 또는 일부 다른 컨텍스트 정보일 수 있습니다. 애플리케이션은 다른 레이블을 지정하여 완전히 다른 키 값 집합을 요청할 수 있습니다. 따라서 모든 키 참조는 코드에서 변경되지 않은 상태로 유지됩니다.

## <a name="key-value-compositions"></a>키-값 컴포지션

App Configuration은 저장된 모든 키를 독립적인 엔터티로 처리합니다. App Configuration은 키 사이의 관계를 유추하거나 해당 계층 구조를 기반으로 키 값을 상속하지 않습니다. 그러나 애플리케이션 코드에서 적절한 구성 스태킹과 결합된 레이블을 사용하면 여러 키 집합을 집계할 수 있습니다.

예를 살펴보겠습니다. **Asset1** 라는 설정이 있는 경우 해당 값은 개발 환경에 따라 달라질 수 있습니다. 빈 레이블과 ‘Development’ 레이블이 있는 ‘Asset1’ 키를 만듭니다. 빈 레이블에서 **Asset1** 의 기본값을 입력하고, 후자에는 ‘Development’에 대한 특정 값을 입력합니다.

코드에서 먼저 레이블 없는 키 값을 검색한 다음, ‘Development’ 레이블을 사용하여 동일한 키 값 집합을 다시 검색합니다. 키 값을 다시 검색할 때 키의 이전 값은 덮어쓰기됩니다. .NET Core 구성 시스템을 사용하면 여러 구성 데이터 세트를 차례대로 ‘스택’할 수 있습니다. 키가 여러 세트에 포함되어 있는 경우 해당 키를 포함하는 마지막 세트가 사용됩니다. .NET Core와 같은 최신 프로그래밍 프레임워크를 사용할 경우, 원시 구성 공급자를 사용하여 App Configuration에 액세스할 때, 해당 스태킹 기능을 평가판으로 이용할 수 있습니다. 다음 코드 조각은 .NET Core 애플리케이션에서 해당 스태킹을 실행하는 방법을 보여 줍니다.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[다른 환경에 대해 각기 다른 구성을 설정하도록 레이블 사용](./howto-labels-aspnet-core.md)은 전체 예제를 제공합니다.

## <a name="app-configuration-bootstrap"></a>App Configuration 부트스트랩

App Configuration 저장소에 액세스하기 위해, Azure Portal에서 이용 가능한 연결 문자열을 사용할 수 있습니다. 연결 문자열에는 자격 증명 정보가 포함되어 있으므로 비밀로 간주됩니다. 해당 비밀은 Azure Key Vault에 저장되어야 하며, 이를 검색하려면 Key Vault에 코드를 인증해야 합니다.

보다 나은 방법은 Azure Active Directory에서 관리 ID 기능을 사용하는 것입니다. 관리 ID를 사용한다면, App Configuration 엔드포인트 URL만으로 App Configuration 저장소에 대한 액세스를 부트스트랩할 수 있습니다. 애플리케이션 코드(예: *appsettings.json* 파일)에 URL을 포함할 수 있습니다. 자세한 내용은 [Azure 관리 ID와 통합](howto-integrate-azure-managed-service-identity.md)을 참조하세요.

## <a name="app-or-function-access-to-app-configuration"></a>App Configuration에 앱 또는 함수 액세스

다음 방법 중 하나를 사용하여 웹앱 또는 함수가 App Configuration에 액세스할 수 있도록 설정할 수 있습니다.

* Azure Portal을 통해 App Service의 애플리케이션 설정에서 App Configuration 저장소에 대한 연결 문자열을 입력합니다.
* Key Vault에서 App Configuration 저장소에 연결 문자열을 저장하고 [App Service에서 이를 참조합니다](../app-service/app-service-key-vault-references.md).
* Azure 관리 ID를 사용하여 App Configuration 저장소에 액세스합니다. 자세한 내용은 [Azure 관리 ID와 통합](howto-integrate-azure-managed-service-identity.md)을 참조하세요.
* App Configuration에서 App Service로 구성을 푸시합니다. App Configuration은 데이터를 App Service로 직접 보내는 내보내기 함수(Azure Portal 및 Azure CLI에서)를 제공합니다. 해당 방법을 사용하면 애플리케이션 코드를 변경할 필요가 없습니다.

## <a name="reduce-requests-made-to-app-configuration"></a>App Configuration으로 보내는 요청 줄이기

App Configuration으로 보내는 과도한 요청은 대역폭 제한 또는 추가 요금을 발생시킬 수 있습니다. 요청 수를 줄이려면 다음을 수행합니다.

* 특히 구성 값을 자주 변경하지 않는 경우 새로 고침 제한 시간을 늘립니다. [`SetCacheExpiration` 메서드](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)를 사용하여 새로 고침 제한 시간을 새로 지정합니다.

* 개별 키를 관찰하는 대신 단일 *센티널 키* 만을 관찰합니다. 센티널 키가 변경되는 경우에만 모든 구성을 새로 고칩니다. [ASP.NET Core 앱에서 동적 구성 사용](enable-dynamic-configuration-aspnet-core.md)을 참조하세요.

* 변경 사항을 지속적으로 폴링하는 대신 Azure Event Grid를 사용하여 구성 변경 시 알림을 받을 수 있습니다. 자세한 내용은 [Azure App Configuration 이벤트를 웹 엔드포인트로 라우팅](./howto-app-configuration-event.md)을 참조하세요.

## <a name="importing-configuration-data-into-app-configuration"></a>구성 데이터를 App Configuration으로 가져오기

App Configuration에서는 Azure Portal 또는 CLI를 사용하여 현재 구성 파일에서 구성 설정을 대량으로 [가져올](./howto-import-export-data.md) 수 있는 옵션을 제공합니다. 동일한 옵션을 사용하여 App Configuration에서 값을 내보낼 수 있습니다(예: 관련 저장소 간에 이동). GitHub 리포지토리와의 지속적인 동기화를 설정하려는 경우, [GitHub 작업](./concept-github-action.md)으로 App Configuration의 혜택을 누리면서 기존의 원본 제어 방법을 계속 사용할 수 있습니다.

## <a name="multi-region-deployment-in-app-configuration"></a>App Configuration에서 여러 하위 지역 배포

App Configuration은 지역 서비스입니다. 하위 지역별 구성이 서로 다른 애플리케이션의 경우 해당 구성을 하나의 인스턴스에 저장하는 것은 단일 실패 지점을 발생시킬 수 있습니다. 여러 하위 지역에 지역당 하나의 App Configuration 인스턴스를 배포하는 것이 보다 나은 선택일 수 있습니다. 해당 방법은 지역 재해 복구, 성능 및 보안 저장에 도움이 될 수 있습니다. 또한 하위 지역별로 인스턴스를 구성하면 대기 시간이 줄어들고, 제한이 인스턴스 별로 적용되므로 분리된 제한 할당량이 사용됩니다. 재해 복구 완화를 적용하기 위해 [다중 구성 저장소](./concept-disaster-recovery.md)를 사용할 수 있습니다. 

## <a name="client-applications-in-app-configuration"></a>App Configuration의 클라이언트 애플리케이션 

App Configuration으로 보내는 과도한 요청은 대역폭 제한 또는 추가 요금을 발생시킬 수 있습니다. 애플리케이션은 현재 제공되는 캐싱 및 지능형 새로 고침 기능을 활용하여 전송하는 요청 수를 최적화합니다. 해당 프로세스는 구성 저장소에 대한 직접 연결을 방지하여 대용량 클라이언트 애플리케이션에서 미러링할 수 있습니다. 대신, 클라이언트 애플리케이션은 사용자 지정 서비스에 연결되며, 이 서비스는 구성 저장소와 연결됩니다. 해당 프록시 솔루션은 클라이언트 애플리케이션이 구성 저장소의 대역폭 제한에 도달하지 않도록 해 줍니다. 대역폭 제한에 대한 자세한 내용은 [FAQ](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration)를 참조하세요.  

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)