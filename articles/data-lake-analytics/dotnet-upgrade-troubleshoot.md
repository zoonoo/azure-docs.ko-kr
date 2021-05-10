---
title: .NET Framework 4.7.2 업그레이드로 인한 Azure Data Lake Analytics U-SQL 작업 오류를 해결하는 방법
description: .NET Framework 4.7.2로 업그레이드하여 발생하는 U-SQL 작업 오류 문제를 해결합니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217680"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics는 .NET Framework v4.7.2로 업그레이드 중입니다.

Azure Data Lake Analytics 기본 런타임은 .NET Framework v4.5.2에서 .NET Framework v4.7.2로 업그레이드하는 중입니다. U-SQL 코드에서 사용자 지정 어셈블리를 사용하는데 해당 사용자 지정 어셈블리가 .NET 라이브러리를 사용하는 경우 이 변경 사항으로 인해 호환성이 손상되는 변경이 발생할 약간의 위험이 있습니다.

.NET Framework 4.5.2에서 버전 4.7.2로 업그레이드한다는 것은 U-SQL 런타임(기본 런타임)에 배포되는 .NET Framework가 이제는 항상 4.7.2라는 의미입니다. .NET Framework 버전에 대한 병렬 옵션은 없습니다.

.NET Framework 4.7.2로 업그레이드한 후에는 시스템의 관리 코드가 버전 4.7.2로 실행되고, U-SQL 사용자 지정 어셈블리와 같은 사용자 제공 라이브러리는 어셈블리가 생성된 버전에 적합한, 이전 버전과 호환되는 모드에서 실행됩니다.

- 4\.5.2 버전의 어셈블리 DLL이 생성된 경우 배포된 프레임워크는 DLL을 4.5.2 라이브러리로 처리하고 (몇 가지 예외가 있긴 하지만) 4.5.2 의미 체계를 제공합니다.
- 이제 .NET Framework 4.7.2를 대상으로 하는 경우 버전 4.7.2 기능을 사용하는 U-SQL 사용자 지정 어셈블리를 사용할 수 있습니다.

.NET Framework 4.7.2로 업그레이드 때문에 .NET 사용자 지정 어셈블리를 사용하는 U-SQL 작업에 대해 호환성이 손상되는 변경이 발생할 수 있습니다. 아래 절차에 따라 이전 버전과의 호환성 문제를 확인하는 것이 좋습니다.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>이전 버전과의 호환성 문제를 확인하는 방법

U-SQL 사용자 지정 어셈블리에서 .NET 코드에 대한 .NET 호환성 검사를 실행하여 이전 버전과의 호환성이 손상되는 문제가 발생할 수 있는지 확인합니다.

> [!Note]
> 이 도구는 실제 호환성이 손상되는 변경은 탐지하지 않습니다. (특정 입력에 대해) 문제를 발생시킬 수 있는 호출된 .NET API만 식별합니다. 문제에 대한 알림이 표시되더라도 코드에 문제가 없을 수 있지만 자세한 확인해 보아야 합니다.

1. 다음을 수행하여 .NET DLL에 대한 이전 버전과의 호환성 검사를 실행합니다.
   1. [.NET Portability Analyzer Visual Studio 확장](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)에서 Visual Studio 확장 사용
   1. [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)에서 독립 실행형 도구 다운로드 및 사용 독립 실행형 도구를 실행하기 위한 지침은 [GitHub dotnetapiport 호환성이 손상되는 변경](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)에 나와 있습니다.
   1. 4\.7.2. 호환성과 관련하여 `read isRetargeting == True`가 가능한 문제를 식별합니다.
2. 이 도구에 이전 버전과의 가능한 비호환성으로 인해 코드가 영향을 받을 수 있다고 표시되는 경우(비호환의 일반적인 예는 아래에 나와 있음) 다음을 수행하여 확인할 수 있습니다.
   1. 코드를 분석하여 코드가 영향을 받는 API에 값을 전달하는지 확인합니다.
   1. 런타임 검사를 수행합니다. 런타임 배포는 ADLA에서 병렬로 수행되지 않습니다. 대표 데이터 세트에 대해 로컬 .NET Framework 4.7.2와 함께 VisualStudio 로컬 실행을 사용하여 업그레이드 전에 런타임 검사를 수행할 수 있습니다.
3. 이전 버전과의 비호환성이 영향을 미치는 경우 이를 해결하는 데 필요한 단계를 수행합니다(예: 데이터 또는 코드 논리 수정).

대부분의 경우에는 이전 버전과의 비호환성이 영향을 미치면 안 됩니다.

## <a name="timeline"></a>타임라인

여기서 새 런타임의 배포를 확인하고 이전에 성공한 작업을 살펴보아 [런타임 문제를 해결](runtime-troubleshoot.md)할 수 있습니다.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>검토한 코드를 시간 내에 가져올 수 없는 경우

(4.5.2를 대상으로 빌드된) 이전 런타임 버전에 대해 작업을 제출할 수 있지만 .NET Framework 병렬 기능이 없기 때문에 해당 작업은 4.5.2 호환 모드에서만 실행됩니다. 이 동작으로 인해 이전 버전과의 호환성 문제 몇 가지가 여전히 발생할 수 있습니다.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>발생할 수 있는 가장 일반적인 이전 버전과의 호환성 문제

검사기에서 식별할 수 있는 가장 일반적인 이전 버전과의 비호환성, 영향을 받는 라이브러리(참고: 라이브러리는 간접적으로만 호출할 수 있음으로 작업이 영향을 받는지 확인하려면 필요한 작업 #1을 수행해야 함), 문제 해결을 위해 수행할 수 있는 작업은 아래와 같습니다. 아래 목록은 자체 내부 ADLA 작업에 대해 검사기를 실행하여 만들었습니다. 참고: 자체 작업의 거의 모든 사례에서는 대부분의 호환성이 손상되는 변경이 가진 제한된 특성 때문에 경고가 가양성으로 나타났습니다.

- 결과 작업을 완료하려면 IAsyncResult.CompletedSynchronously 속성이 정확해야 함
  - TaskFactory.FromAsync를 호출할 때 IAsyncResult.CompletedSynchronously 속성이 올바르게 구현되어야 결과 작업을 완료할 수 있습니다. 즉, 이 속성은 구현이 동기적으로 완료된 경우에만 true를 반환해야 합니다. 이전에는 이 속성이 선택되어 있지 않았습니다.
  - 영향을 받는 라이브러리: mscorlib, System.Threading.Tasks
  - 제안된 작업: TaskFactory.FromAsync가 true를 올바르게 반환하는지 확인합니다.

- DataObject.GetData가 이제 데이터를 UTF-8로 검색
  - .NET Framework 4를 대상으로 하거나 .NET Framework 4.5.1 이하 버전에서 실행되는 앱의 경우, DataObject.GetData는 HTML 형식의 데이터를 ASCII 문자열로 검색합니다. 그 결과 ASCII 문자가 아닌 문자(ASCII 코드가 0x7F보다 큰 문자)는 임의의 두 문자(#N##N#)로 표시됩니다. .NET Framework 4.5 이상을 대상으로 하고 .NET Framework 4.5.2에서 실행되는 앱의 경우, `DataObject.GetData`는 HTML 형식의 데이터를 UTF-8로 검색하여 0x7F보다 큰 문자를 올바르게 나타낼 수 있습니다.
  - 영향을 받는 라이브러리: Glo
  - 제안된 작업: 검색된 데이터가 필요한 형식인지 확인합니다.

- 잘못된 서로게이트 쌍에서 XmlWriter가 throw함
  - .NET Framework 4.5.2 또는 이전 버전을 대상으로 하는 앱의 경우 예외 대체(fallback) 처리를 사용하여 잘못된 서로게이트 쌍을 작성해도 항상 예외가 발생하지는 않습니다. .NET Framework 4.6을 대상으로 하는 앱의 경우 잘못된 서로게이트 쌍을 쓰려고 하면 `ArgumentException`을 throw합니다.
  - 영향을 받는 라이브러리: System.Xml, System.Xml.ReaderWriter
  - 제안된 작업: 인수 예외를 발생시키는 잘못된 서로게이트 쌍을 작성하지 않았는지 확인합니다.

- HtmlTextWriter가 `<br/>` 요소를 올바르게 렌더링하지 않음
  - .NET Framework 4.6부터 `<BR />` 요소로 `HtmlTextWriter.RenderBeginTag()` 및 `HtmlTextWriter.RenderEndTag()`를 호출하면 (두 개가 아닌) 단 하나의 `<BR />`만 올바르게 삽입합니다.
  - 영향을 받는 라이브러리: System.Web
  - 제안된 작업: 프로덕션 작업에서 임의 동작이 나타나지 않도록 표시해야 할 `<BR />`을 충분히 삽입했는지 확인합니다.

- Null 인수를 사용한 CreateDefaultAuthorizationContext 호출이 변경되었습니다.
  - Null authorizationPolicies를 사용하는 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)`에 대한 호출로 반환된 AuthorizationContext의 구현이 .NET Framework 4.6에서 변경되었습니다.
  - 영향을 받는 라이브러리: System.IdentityModel
  - 제안된 작업: null 권한 부여 정책이 있는 경우 새로운 예상 동작을 처리하는지 확인합니다.
  
- 이제 RSACng가 비표준 키 크기의 RSA 키를 올바르게 로드함
  - 4\.6.2 이전의 .NET Framework 버전의 경우, RSA 인증서의 비표준 키 크기를 가진 고객은 `GetRSAPublicKey()` 및 `GetRSAPrivateKey()` 확장 메서드를 통해 해당 키에 액세스할 수 없습니다. “요청한 키 크기가 지원되지 않습니다”라는 메시지와 함께 `CryptographicException`이 throw됩니다. .NET Framework 4.6.2에서 이 문제가 해결되었습니다. 마찬가지로, `RSA.ImportParameters()` 및 `RSACng.ImportParameters()`는 `CryptographicException`을 throw하지 않고 비표준 키 크기로 작업합니다.
  - 영향을 받는 라이브러리: mscorlib, System.Core
  - 제안된 작업: RSA 키가 예상대로 작동하는지 확인합니다.

- 경로 콜론 검사가 더욱 엄격해짐
  - .NET Framework 4.6.2에서 이전에 지원되지 않던 경로(길이 및 형식 모두)를 지원하도록 여러 가지가 변경되었습니다. 적절한 드라이브 구분 기호(콜론) 구문에 대해 검사가 좀 더 정확해졌습니다. 이 구문은 허용되었던 일부 선택 경로 API에서 일부 URI 경로가 차단되는 부작용이 있었습니다.
  - 영향을 받는 라이브러리: mscorlib, System.Runtime.Extensions
  - 제안된 작업:

- ClaimsIdentity 생성자 호출
  - .NET Framework 4.6.2부터는 `T:System.Security.Principal.IIdentity` 매개 변수로 `T:System.Security.Claims.ClaimsIdentity` 생성자가 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성을 설정하는 방법이 변경되었습니다. `T:System.Security.Principal.IIdentity` 인수가 `T:System.Security.Claims.ClaimsIdentity` 개체이고 해당 `T:System.Security.Claims.ClaimsIdentity` 개체의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 `null`이 아닌 경우 `M:System.Security.Claims.ClaimsIdentity.Clone` 메서드를 사용하여 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 연결됩니다. 프레임워크 4.6.1 이전 버전에서 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성은 기존 참조로 연결됩니다. 이 변경으로 인해 .NET Framework 4.6.2부터 새 `T:System.Security.Claims.ClaimsIdentity` 개체의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성은 생성자 `T:System.Security.Principal.IIdentity` 인수의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성과 같지 않습니다. .NET Framework 4.6.1 이전 버전에서는 이 속성이 같습니다.
  - 영향을 받는 라이브러리: mscorlib
  - 제안된 작업: 새 런타임에서 ClaimsIdentity가 예상대로 작동하는지 확인합니다.

- DataContractJsonSerializer를 사용한 제어 문자의 serialization가 이제 ECMAScript V6 및 V8과 호환됨
  - .NET Framework 4.6.2 이전 버전에서 DataContractJsonSerializer는 ECMAScript V6 및 V8 표준과 호환되는 방식으로 \b, \f, \t 등의 일부 특수 제어 문자를 직렬화하지 않았습니다. .NET Framework 4.7부터는 이러한 제어 문자의 serialization가 ECMAScript V6 및 V8과 호환됩니다.
  - 영향을 받는 라이브러리: System.Runtime.Serialization.Json
  - 제안된 작업: DataContractJsonSerializer와 동일하게 동작하는지 확인합니다.
