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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413613"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 앱 구성 모범 사례

이 문서에서는 Azure 앱 구성을 사용 하는 경우 일반적인 패턴 및 사례를 설명 합니다.

## <a name="key-groupings"></a>그룹화 키

키를 구성 하기 위한 두 가지 옵션을 제공 하는 앱 구성: 접두사 또는 레이블 키입니다. 하나 또는 둘 다 사용할 수 있습니다.

키 접두사는 키의 시작 부분입니다. 이름에 동일한 접두사를 사용 하 여 키 집합을 논리적으로 그룹화 할 수 있습니다. 접두사와 같은 구분 기호로 함께 연결 하는 여러 구성 요소를 포함할 수 있습니다 `/`, 네임 스페이스를 구성 하는 URL 경로 비슷합니다. 이러한 계층은 하나의 앱 구성 저장소에 많은 응용 프로그램, 구성 요소 서비스 및 환경에 대 한 키를 저장 하는 경우에 유용 합니다. 기억해 야 할 중요 한 점은 키 해당 설정의 값을 검색 하려면 응용 프로그램 코드에서 참조 항목입니다. 키를 변경 하지 마십시오. 그렇지 않으면 될 때마다 발생 하는 코드를 수정 해야 합니다.

레이블이 키에 대 한 특성입니다. 키의 변형을 만드는 사용 됩니다. 예를 들어, 키의 여러 버전에 레이블을 할당할 수 있습니다. 반복, 환경 또는 다른 컨텍스트 정보를 버전 수 있습니다. 응용 프로그램에 다른 레이블을 지정 하 여 완전히 다른 키 값 집합을 요청할 수 있습니다. 모든 키 참조를 변경할 필요가 없습니다.

## <a name="key-value-compositions"></a>키-값 컴퍼지션

앱 구성 독립적인 엔터티로 저장 된 모든 키를 처리 합니다. 키 사이의 관계를 유추 하 여 해당 계층을 기반으로 하는 키 값을 상속 하지 않습니다. 그러나 여러 키 집합을 집계할 수 있습니다, 응용 프로그램 코드에서 스택 적절 한 구성을 사용 하 여 결합 된 레이블을 사용 하 여 합니다.

예제를 살펴보겠습니다. 설정이 있는 **Asset1** "개발" 환경에 대 한 값을 갖는 다를 수 있습니다. 빈 레이블이 및 "개발" 이라는 레이블을 사용 하 여 "Asset1" 라는 키를 만들 수 있습니다. 에 대 한 기본값을 넣으면 **Asset1** 전자 및 후자에 "개발"에 대 한 특정 값입니다. 코드에서 먼저 하지 않고 모든 레이블을 차례로 동일한 키의 이전 값을 덮어쓰려면 "개발" 레이블을 사용 하 여 해당 키 값을 검색 합니다. .NET Core와 같은 최신 프로그래밍 프레임 워크를 사용 하는 경우 얻을 수 있습니다이 겹침 기능은 무료로 앱 구성에 액세스 하는 기본 구성 공급자를 사용 하는 경우. 다음 코드 조각에서는.NET Core 응용 프로그램에서 스택 구현 하는 방법을 보여 줍니다.

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

앱 구성 저장소에 액세스 하려면 Azure portal에서 사용할 수 있는 연결 문자열을 사용할 수 있습니다. 연결 문자열 자격 증명 정보를 포함 하 고 비밀로 간주 됩니다. Key Vault에 저장 해야 합니다. 더 나은 옵션은 Azure 사용 하 여 관리 id 이 메서드를 사용 하 여 하기만 하면 앱 구성 끝점 URL에 구성 저장소에 대 한 액세스를 부트스트랩 합니다. 응용 프로그램 코드에 URL을 포함할 수 있습니다 (예를 들어 합니다 *appsettings.json* 파일). 참조 [Azure 사용 하 여 통합 identities 관리](howto-integrate-azure-managed-service-identity.md) 대 한 자세한 내용은 합니다.

## <a name="web-app-or-function-access-to-app-configuration"></a>앱 구성에 대 한 웹 앱 또는 함수 액세스

Azure portal 통해 App Service의 응용 프로그램 설정으로 앱 구성 저장소에 연결 문자열을 입력할 수 있습니다. 또한 Key Vault에 저장할 수 있습니다 하 고 [App Service에서 참조](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)합니다. Azure를 이용할 수 있습니다 관리 되는 구성 저장소에 액세스 하는 id입니다. 참조 [Azure 사용 하 여 통합 identities 관리](howto-integrate-azure-managed-service-identity.md) 대 한 자세한 내용은 합니다.

또는 App Service에 앱 구성에서 구성을 푸시할 수 있습니다. 앱 구성 (Azure portal 및 CLI)에서 App Service에 직접 데이터를 전송 하는 내보내기 함수를 제공 합니다. 이 메서드를 사용 하 여 응용 프로그램 코드를 전혀 변경할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)
