---
title: Azure 앱 구성 모범 사례 | Microsoft Docs
description: Azure 앱 구성을 사용 하는 최상의 방법 알아보기
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393583"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 앱 구성 모범 사례

이 문서에서는 Azure 앱 구성을 사용 하는 경우 일반적인 패턴 및 모범 사례를 설명 합니다.

## <a name="key-groupings"></a>그룹화 키

앱 구성 키를 구성 하기 위한 두 가지 옵션이 표시 됩니다.

* 키 접두사
* 레이블

키를 그룹화 하거나 두 옵션 중 하나를 사용할 수 있습니다.

*접두사를 키* 키의 시작 부분입니다. 이름에 동일한 접두사를 사용 하 여 키 집합을 논리적으로 그룹화 할 수 있습니다. 접두사와 같은 구분 기호를 통해 연결 하는 여러 구성 요소를 포함할 수 있습니다 `/`, 네임 스페이스를 구성 하는 URL 경로 비슷합니다. 이러한 계층은 하나의 앱 구성 저장소에 많은 응용 프로그램, 구성 요소 서비스 및 환경에 대 한 키를 저장 하는 경우에 유용 합니다.

기억해 야 할 중요 한 점은 키 해당 설정의 값을 검색 하려면 응용 프로그램 코드에서 참조 항목입니다. 될 때마다 발생 하는 코드를 수정 해야 합니다. 그렇지 않으면 키가 변경 하지 않아야 합니다.

*레이블* 키에 대 한 특성을 됩니다. 키의 변형을 만드는 데 사용 합니다. 예를 들어, 키의 여러 버전에 레이블을 할당할 수 있습니다. 반복, 환경 또는 다른 컨텍스트 정보를 버전 수 있습니다. 응용 프로그램에 다른 레이블을 지정 하 여 완전히 다른 키 값 집합을 요청할 수 있습니다. 결과적으로, 모든 키 참조 코드에서 변경 되지 않습니다.

## <a name="key-value-compositions"></a>키-값 컴퍼지션

앱 구성 독립적인 엔터티로 저장 된 모든 키를 처리 합니다. 앱 구성 키 간의 관계를 유추 하거나 해당 계층을 기반으로 하는 키 값을 상속 하도록 시도 하지 않습니다. 그러나 응용 프로그램 코드에서 스택 적절 한 구성을 사용 하 여 결합 된 레이블을 사용 하 여 여러 키 집합을 집계할 수 있습니다.

예제를 살펴보겠습니다. 이라는 설정을 있다고 가정해 봅시다 **Asset1**, 개발 환경에 따라 값이 달라질 수 있습니다. 빈 레이블이와 "개발" 이라는 레이블이 "Asset1" 라는 키를 만듭니다. 에 대 한 기본 값을 입력 하면 첫 번째 레이블의 **Asset1**, 후자에 "개발"에 대 한 특정 값을 저장 합니다.

코드에서 모든 레이블이 없는 키 값을 먼저 검색 하 고 키 값의 동일한 집합을 검색 한 다음 "개발" 레이블 사용 하 여 두 번째 시간입니다. 두 번째 시간 값을 검색 하는 경우 키의 이전 값을 덮어씁니다. .NET Core 구성 시스템을 사용 하면 여러 집합이 서로 위에 구성 데이터를 "스택" 수 있습니다. 둘 이상의 집합에 있는 키를 포함 하는 마지막 집합 사용 됩니다. .NET Core와 같은 최신 프로그래밍 프레임 워크를 사용 하 여 얻게이 겹침 기능은 무료로 앱 구성에 액세스 하는 기본 구성 공급자를 사용 하는 경우. 다음 코드 조각은.NET Core 응용 프로그램에서 스택을 구현 하는 방법을 보여 줍니다.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>앱 구성 부트스트랩

앱 구성 저장소에 액세스 하려면 Azure portal에서 사용할 수 있는 연결 문자열을 사용할 수 있습니다. 연결 문자열에 자격 증명 정보를 포함 하기 때문에 비밀 간주 합니다. 이러한 암호를 Azure Key Vault에 저장 해야 하 고 코드 가져와 Key Vault에 인증 해야 합니다.

더 나은 방법은 Azure Active Directory에서 관리 되는 id 기능을 사용 하는 것입니다. 관리 되는 id를 사용 하 여 앱 구성 저장소에 부트스트랩 액세스 하려면 앱 구성 끝점 URL만 필요 합니다. 응용 프로그램 코드에 URL을 포함할 수 있습니다 (예를 들어 합니다 *appsettings.json* 파일). 참조 [Azure와 통합 관리 identities](howto-integrate-azure-managed-service-identity.md) 세부 정보에 대 한 합니다.

## <a name="app-or-function-access-to-app-configuration"></a>앱 구성에 대 한 앱 또는 함수 액세스

다음 방법 중 하나를 사용 하 여 웹 앱 또는 함수에 대 한 앱 구성에 대 한 액세스를 제공할 수 있습니다.

* Azure portal을 통해 App Service의 응용 프로그램 설정에서 앱 구성 저장소에 연결 문자열을 입력 합니다.
* Key Vault에 앱 구성 저장소에 대 한 연결 문자열을 저장 하 고 [App Service에서 참조](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)합니다.
* 사용 하 여 Azure 앱 구성 저장소에 액세스 하기 위한 id를 관리 합니다. 자세한 내용은 [Azure 사용 하 여 통합 identities 관리](howto-integrate-azure-managed-service-identity.md)합니다.
* App Service에 앱 구성에서 구성을 푸시하십시오. 앱 구성 App Service에 직접 데이터를 전송 하는 Azure portal 및 Azure CLI) (에서 내보내기 함수를 제공 합니다. 이 메서드를 사용 하 여 응용 프로그램 코드를 전혀 변경할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)
