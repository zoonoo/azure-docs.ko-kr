---
title: .NET Framework 4.7.2 업그레이드로 인해 Azure 데이터 레이크 애널리틱스 U-SQL 작업 실패 문제를 해결하는 방법
description: .NET Framework 4.7.2로 업그레이드하여 U-SQL 작업 실패문제를 해결합니다.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213592"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure 데이터 레이크 애널리틱스가 .NET 프레임워크 v4.7.2로 업그레이드되고 있습니다.

Azure 데이터 레이크 분석 기본 런타임이 .NET Framework v4.5.2에서 .NET 프레임워크 v4.7.2로 업그레이드되고 있습니다. 이 변경으로 U-SQL 코드에서 사용자 지정 어셈블리를 사용하고 사용자 지정 어셈블리에서 .NET 라이브러리를 사용하는 경우 변경 내용이 변경될 위험이 적습니다.

.NET Framework 4.5.2에서 버전 4.7.2로 업그레이드하면 U-SQL 런타임(기본 런타임)에 배포된 .NET 프레임워크가 항상 4.7.2가 됩니다. .NET Framework 버전에는 나란히 있는 옵션이 없습니다.

.NET Framework 4.7.2로 업그레이드가 완료되면 시스템의 관리 코드는 버전 4.7.2로 실행되며, 사용자가 제공한 U-SQL 사용자 지정 어셈블리와 같은 라이브러리는 어셈블리가 해당 버전에 적합한 이전 버전 호환 모드에서 실행됩니다. 생성됩니다.

- 버전 4.5.2에 대해 어셈블리 DLL이 생성되는 경우 배포된 프레임워크는 4.5.2 라이브러리로 처리하여 (몇 가지 예외를 제외하고) 4.5.2 의미 체계를 제공합니다.
- 이제 .NET Framework 4.7.2를 대상으로 하는 경우 버전 4.7.2 기능을 사용하는 U-SQL 사용자 지정 어셈블리를 사용할 수 있습니다.

.NET Framework 4.7.2로 업그레이드되므로 .NET 사용자 지정 어셈블리를 사용하는 U-SQL 작업에 주요 변경 사항이 발생할 수 있습니다. 아래 절차를 사용하여 이전 버전과의 호환성 문제를 확인하는 것이 좋습니다.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>이전 버전과의 호환성 문제를 확인하는 방법

U-SQL 사용자 지정 어셈블리에서 .NET 코드에서 .NET 호환성 검사를 실행하여 이전 버전과의 호환성 문제를 방지할 수 있는지 확인합니다.

> [!Note]
> 이 도구는 실제 주요 변경 내용을 검색하지 않습니다. 특정 입력에 대해 문제를 일으킬 수 있는 .NET API라고만 식별합니다. 문제에 대한 알림을 받는 경우에도 코드는 여전히 괜찮을 수 있지만 자세한 내용은 확인해야 합니다.

1. .NET DLL에서 이전 버전과의 호환성 검사기를 실행합니다.
   1. [.NET 이식성 분석기 비주얼 스튜디오 확장에서](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) 비주얼 스튜디오 확장 사용
   1. [GitHub dotnetapiport에서](https://github.com/microsoft/dotnet-apiport)독립 실행형 도구를 다운로드하고 사용하십시오. 독립 실행형 도구를 실행하는 방법에 대한 지침은 [GitHub 닷넷타피포트 주요 변경 사항에 있습니다.](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 4.7.2. 가능한 `read isRetargeting == True` 문제를 식별합니다.
2. 도구가 코드가 가능한 역호환성(비호환성의 몇 가지 일반적인 예는 아래에 나열되어 있음)에 의해 코드가 영향을 받을 수 있는지 를 나타내는 경우,
   1. 코드 분석 및 코드가 영향을 받는 API에 값을 전달하는지 식별
   1. 런타임 검사를 수행합니다. 런타임 배포는 ADLA에서 나란히 수행되지 않습니다. 대표 데이터 집합에 대해 로컬 .NET Framework 4.7.2를 사용하여 VisualStudio의 로컬 실행을 사용하여 업그레이드 하기 전에 런타임 검사를 수행할 수 있습니다.
3. 실제로 이전 버전과의 호환성에 의해 영향을 받는 경우 데이터 또는 코드 논리 수정과 같은 수정에 필요한 단계를 수행합니다.

대부분의 경우 이전 버전과의 호환성의 영향을 받지 않아야 합니다.

## <a name="timeline"></a>타임라인

여기에서 [런타임 문제 해결을](runtime-troubleshoot.md)위한 새 런타임의 배포를 확인하고 이전에 성공한 작업을 확인할 수 있습니다.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>제 시간에 코드를 검토할 수 없는 경우 어떻게 해야 합니까?

4.5.2를 대상으로 빌드된 이전 런타임 버전에 대해 작업을 제출할 수 있지만 .NET Framework 나란히 기능이 없기 때문에 여전히 4.5.2 호환성 모드에서만 실행됩니다. 이 동작으로 인해 이전 버전과의 호환성 문제가 발생할 수 있습니다.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>발생할 수 있는 가장 일반적인 이전 버전과의 호환성 문제는 무엇입니까?

검사기에서 식별할 가능성이 가장 일반적인 역호환성은 라이브러리가 영향을 받는 라이브러리(참고: 라이브러리를 간접적으로만 호출할 수 있으므로 작업에 영향을 받는지 확인하기 위해 필요한 조치를 취하는 #1) 및 해결 가능한 조치입니다. 참고: 우리 자신의 직업에 대한 거의 모든 경우에 경고는 대부분의 주요 변경 사항의 좁은 특성으로 인해 거짓 긍정으로 판명되었습니다.

- 결과 작업을 완료하려면 IAsyncResult.CompletedSynchronously 속성이 정확해야 함
  - TaskFactory.FromAsync를 호출할 때 IAsyncResult.CompletedSynchronously 속성이 올바르게 구현되어야 결과 작업을 완료할 수 있습니다. 즉, 이 속성은 구현이 동기적으로 완료된 경우에만 true를 반환해야 합니다. 이전에는 이 속성이 선택되어 있지 않았습니다.
  - 영향을 받은 라이브러리: mscorlib, System.SThreading.Tasks
  - 제안된 작업: TaskFactory.FromAsync가 true를 올바르게 반환하도록 합니다.

- DataObject.GetData가 이제 데이터를 UTF-8로 검색
  - .NET Framework 4를 대상으로 하거나 .NET Framework 4.5.1 이하 버전에서 실행되는 앱의 경우, DataObject.GetData는 HTML 형식의 데이터를 ASCII 문자열로 검색합니다. 따라서 ASCII 가 0x7F보다 큰 비ASCI 문자(ASCII 코드가 0x7F보다 큰 문자)는 두 개의 임의 문자로 표시됩니다.#N##N#a.5 이상을 대상으로 `DataObject.GetData` 하고 .NET Framework 4.5.2에서 실행되는 앱의 경우 HTML 형식의 데이터를 0x7F보다 큰 문자를 올바르게 나타내는 UTF-8로 검색합니다.
  - 영향을 받은 라이브러리: Glo
  - 권장 작업: 검색된 데이터가 원하는 형식인지 확인

- 잘못된 서로게이트 쌍에서 XmlWriter가 throw함
  - .NET Framework 4.5.2 또는 이전 버전을 대상으로 하는 앱의 경우 예외 대체(fallback) 처리를 사용하여 잘못된 서로게이트 쌍을 작성해도 항상 예외가 발생하지는 않습니다. .NET Framework 4.6을 대상으로 하는 앱의 경우 잘못된 서로게이트 쌍을 쓰려고 하면 `ArgumentException`을 throw합니다.
  - 영향을 받은 라이브러리: System.Xml, System.Xml.ReaderWriter
  - 제안된 작업: 인수 예외를 일으키는 잘못된 서로게이트 쌍을 작성하지 않는지 확인합니다.

- HtmlTextWriter가 `<br/>` 요소를 올바르게 렌더링하지 않음
  - .NET Framework 4.6부터 `<BR />` 요소로 `HtmlTextWriter.RenderBeginTag()` 및 `HtmlTextWriter.RenderEndTag()`를 호출하면 (두 개가 아닌) 단 하나의 `<BR />`만 올바르게 삽입합니다.
  - 영향을 받은 라이브러리: System.Web
  - 권장 작업: 프로덕션 작업에서 임의 `<BR />` 동작이 표시되지 않도록 예상되는 양을 삽입해야 합니다.

- Null 인수를 사용한 CreateDefaultAuthorizationContext 호출이 변경되었습니다.
  - Null authorizationPolicies를 사용하는 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)`에 대한 호출로 반환된 AuthorizationContext의 구현이 .NET Framework 4.6에서 변경되었습니다.
  - 영향을 받은 라이브러리: 시스템.ID모델
  - 권장 조치: null 권한 부여 정책이 있을 때 예상되는 새 동작을 처리하고 있는지 확인합니다.
  
- 이제 RSACng가 비표준 키 크기의 RSA 키를 올바르게 로드함
  - 4.6.2 이전의 .NET Framework 버전의 경우, RSA 인증서의 비표준 키 크기를 가진 고객은 `GetRSAPublicKey()` 및 `GetRSAPrivateKey()` 확장 메서드를 통해 해당 키에 액세스할 수 없습니다. "요청된 키 크기가 지원되지 않습니다"라는 메시지가 있는 A가 `CryptographicException` throw됩니다. .NET Framework 4.6.2에서 이 문제가 해결되었습니다. 마찬가지로, `RSA.ImportParameters()` `RSACng.ImportParameters()` 지금은 던지지 `CryptographicException`않고 비표준 키 크기로 작동합니다.
  - 영향을 받은 라이브러리: mscorlib, System.Core
  - 제안된 조치: RSA 키가 예상대로 작동하는지 확인

- 경로 콜론 검사가 더욱 엄격해짐
  - .NET Framework 4.6.2에서 이전에 지원되지 않던 경로(길이 및 형식 모두)를 지원하도록 여러 가지가 변경되었습니다. 적절한 드라이브 구분 기호(콜론) 구문에 대해 검사가 좀 더 정확해졌습니다. 이 구문은 허용되었던 일부 선택 경로 API에서 일부 URI 경로가 차단되는 부작용이 있었습니다.
  - 영향을 받은 라이브러리: mscorlib, System.Runtime.확장
  - 제안된 조치:

- ClaimsIdentity 생성자 호출
  - .NET Framework 4.6.2부터는 `T:System.Security.Principal.IIdentity` 매개 변수로 `T:System.Security.Claims.ClaimsIdentity` 생성자가 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성을 설정하는 방법이 변경되었습니다. `T:System.Security.Principal.IIdentity` 인수가 `T:System.Security.Claims.ClaimsIdentity` 개체이고 해당 `T:System.Security.Claims.ClaimsIdentity` 개체의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 `null`이 아닌 경우 `M:System.Security.Claims.ClaimsIdentity.Clone` 메서드를 사용하여 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 연결됩니다. Framework 4.6.1 및 이전 버전에서는 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 기존 참조로 연결됩니다. 이 변경 으로 인해 .NET Framework 4.6.2로 시작 `P:System.Security.Claims.ClaimsIdentify.Actor` `T:System.Security.Claims.ClaimsIdentity` 하 고 새 `P:System.Security.Claims.ClaimsIdentify.Actor` 개체의 속성은 생성자의 `T:System.Security.Principal.IIdentity` 인수의 속성과 같지 않습니다. .NET Framework 4.6.1 이전 버전에서는 이 속성이 같습니다.
  - 영향을 받은 라이브러리: mscorlib
  - 제안된 조치: 클레임Id가 새 런타임에서 예상대로 작동하는지 확인

- DataContractJsonSerializer를 사용한 제어 문자의 serialization가 이제 ECMAScript V6 및 V8과 호환됨
  - .NET 프레임워크 4.6.2 및 이전 버전에서 DataContractJson Serializer는 ECMAScript V6 및 V8 표준과 호환되는 방식으로 \b, \f 및 \t와 같은 일부 특수 제어 문자를 직렬화하지 않았습니다. .NET Framework 4.7부터는 이러한 제어 문자의 serialization가 ECMAScript V6 및 V8과 호환됩니다.
  - 영향을 받은 라이브러리: System.Runtime.직렬화.Json
  - 제안된 작업: 데이터계약Json 직렬화기와 동일한 동작 보장
