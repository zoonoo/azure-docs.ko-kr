---
title: 보안
titleSuffix: Azure Cognitive Services
description: 코그너티브 서비스 사용에 대한 다양한 보안 고려 사항에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131528"
---
# <a name="azure-cognitive-services-security"></a>Azure 코그너티브 서비스 보안

모든 응용 프로그램을 개발할 때 보안을 최우선 순위로 고려해야 합니다. 인공 지능이 활성화된 응용 프로그램이 시작되면서 보안이 더욱 중요해짐에 따라 보안이 더욱 중요해짐에 따라 보안이 더욱 중요해짐에 따라 보안이 더욱 중요해짐에 요 이 문서에서는 전송 계층 보안, 인증 및 중요한 데이터를 안전하게 구성하는 것과 같은 Azure Cognitive Services 보안의 다양한 측면에 대해 설명합니다.

## <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

HTTP에 노출된 모든 코그너티브 서비스 끝점은 TLS 1.2를 적용합니다. 강제 보안 프로토콜을 사용하면 코그너티브 서비스 엔드포인트를 호출하려는 소비자는 다음 지침을 준수해야 합니다.

* 클라이언트 운영 체제(OS)는 TLS 1.2를 지원해야 합니다.
* HTTP 호출을 만드는 데 사용되는 언어(및 플랫폼)는 요청의 일부로 TLS 1.2를 지정해야 합니다.
  * 언어 및 플랫폼에 따라 TLS 지정은 암시적 또는 명시적으로 수행됩니다.

.NET 사용자의 경우 <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">전송 계층 보안 <span class="docon docon-navigate-external x-hidden-focus"> </span>모범 사례를 고려하십시오. </a>

## <a name="authentication"></a>인증

인증에 대해 논의할 때 몇 가지 일반적인 오해가 있습니다. 인증 및 권한 부여는 서로 혼동되는 경우가 많습니다. ID는 보안의 주요 구성 요소이기도 합니다. ID는 <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">보안 주체에 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>대한 정보의 모음입니다. ID 공급자(IdP)는 인증 서비스에 ID를 제공합니다. 인증은 사용자의 신원을 확인하는 행위입니다. 권한 부여는 지정된 ID에 대한 리소스에 대한 액세스 권한 및 권한의 사양입니다. 코그너티브 서비스 제품 중 에는 역할 기반 액세스 제어(RBAC)가 포함됩니다. RBAC는 수동으로 보안 주체를 관리하는 데 관련된 일부 행사를 단순화하는 데 사용할 수 있습니다. 자세한 내용은 [Azure 리소스에 대한 역할 기반 액세스 제어를](../role-based-access-control/overview.md)참조하십시오.

구독 키, 액세스 토큰 및 AAD(Azure Active Directory)를 사용하여 인증하는 인증에 대한 자세한 내용은 <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">Azure Cognitive 서비스에<span class="docon docon-navigate-external x-hidden-focus"></span>대한 인증 요청을</a>참조하십시오.

## <a name="environment-variables-and-application-configuration"></a>환경 변수 및 응용 프로그램 구성

환경 변수는 특정 환경 내에 저장된 이름-값 쌍입니다. 중요한 데이터에 하드 코딩된 값을 사용하는 대신 환경 변수를 사용하는 것이 더 안전합니다. 하드 코딩된 값은 안전하지 않으며 피해야 합니다.

> [!CAUTION]
> 중요한 데이터에 하드 코딩된 값을 **사용하지** 마십시오.

> [!NOTE]
> 환경 변수는 일반 텍스트로 저장되지만 환경으로 격리됩니다. 환경이 손상된 경우 환경의 변수도 마찬가지입니다.

### <a name="set-environment-variable"></a>환경 변수 설정

환경 변수를 설정하려면 명명된 `ENVIRONMENT_VARIABLE_KEY` 키이며 `value` 환경 변수에 저장된 값인 다음 명령 중 하나를 사용합니다.

# <a name="command-line"></a>[명령줄](#tab/command-line)

값이 주어지면 지속된 환경 변수를 만들고 할당합니다.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

**명령 프롬프트의**새 인스턴스에서 환경 변수를 읽습니다.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

값이 주어지면 지속된 환경 변수를 만들고 할당합니다.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

**Windows PowerShell의**새 인스턴스에서 환경 변수를 읽습니다.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

값이 주어지면 지속된 환경 변수를 만들고 할당합니다.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

**Bash의**새 인스턴스에서 환경 변수를 읽습니다.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 환경 변수를 설정한 후 IDE(통합 개발 환경)를 다시 시작하여 새로 추가된 환경 변수를 사용할 수 있도록 합니다.

### <a name="get-environment-variable"></a>환경 변수 얻기

환경 변수를 얻으려면 메모리로 읽어야 합니다. 사용 중인 언어에 따라 다음 코드 조각을 고려하십시오. 이러한 코드 조각은 환경 변수를 지정하고 `ENVIRONMENT_VARIABLE_KEY` 라는 `value`변수에 할당하는 방법을 보여 줍니다.

# <a name="c"></a>[C #](#tab/csharp)

자세한 내용은 을 <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C + +](#tab/cpp)

자세한 내용은 을 <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 을 <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

자세한 내용은 을 <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 을 <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

자세한 내용은 을 <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>다음 단계

* 다양한 [코그너티브 서비스](welcome.md) 살펴보기
* [코그너티브 서비스 가상 네트워크에](cognitive-services-virtual-networks.md) 대해 자세히 알아보기
