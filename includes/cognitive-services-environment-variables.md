---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841515"
---
## <a name="configure-an-environment-variable-for-authentication"></a>인증을 위한 환경 변수 구성

응용 프로그램은 사용 하는 Cognitive Services에 대 한 액세스를 인증 해야 합니다. 인증 하려면 Azure 리소스에 대 한 키를 저장 하는 환경 변수를 만드는 것이 좋습니다. 

키가 있으면 응용 프로그램을 실행 하는 로컬 컴퓨터의 새 환경 변수에 씁니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. 리소스 `your-key` 의 키 중 하나로 대체 합니다.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 예제를 실행합니다.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.
    
* macOS
    
    .bash_profile을 편집하고, 환경 변수를 추가합니다.
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.
