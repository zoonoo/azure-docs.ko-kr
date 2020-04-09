---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 19f3a99c087c0755a82ce7940326708fd1f6a3e5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673196"
---
### <a name="prerequisite-check"></a>필수 구성 요소 확인

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 2.7.1846 버전 이상인지 확인합니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.0.76 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

::: zone pivot="programming-language-python"  
+ `python --version`(Linux/MacOS) 또는 `py --version`(Windows)을 실행하여 Python 버전이 3.8.x, 3.7.x 또는 3.6.x로 보고되는지 확인합니다.
::: zone-end