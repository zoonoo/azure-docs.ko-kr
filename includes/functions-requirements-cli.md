---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673180"
---
## <a name="configure-your-local-environment"></a>로컬 환경 구성

시작하기 전에 다음이 있어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 버전 2.7.1846 이상 2.x 버전
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 및 3.7에는 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 버전 2.7.1846 이상 2.x 버전이 필요합니다. Python 3.8에는 Core Tools의 [버전 3.x](../articles/azure-functions/functions-run-local.md#v2)가 필요합니다.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.0.76 이상 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
::: zone-end

::: zone pivot="programming-language-python"
+ Azure Functions에서 지원되는 [Python 3.8(64비트)](https://www.python.org/downloads/release/python-382/), [Python 3.7(64비트)](https://www.python.org/downloads/release/python-375/), [Python 3.6(64비트)](https://www.python.org/downloads/release/python-368/). 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), 버전 8.

+ [Apache Maven](https://maven.apache.org) 버전 3.0 이상

> [!IMPORTANT]
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.
::: zone-end