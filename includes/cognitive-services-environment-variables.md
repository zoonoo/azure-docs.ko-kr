---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461515"
---
## <a name="configure-an-environment-variable-for-authentication"></a>인증에 대 한 환경 변수 구성

응용 프로그램을 사용 하 여 Cognitive Services에 대 한 액세스를 인증 해야 합니다. 인증을 위해 Azure 리소스에서 키를 저장 하는 환경 변수를 만드는 것이 좋습니다. 

키를 만든 후에 쓰게 새 환경 변수를 응용 프로그램을 실행 하는 로컬 컴퓨터입니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. 대체 `your-key` 이상 감지기 액세스 키를 사용 하 여:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어, Visual Studio 편집기로를 사용 하는 경우 Visual Studio 다시 시작 예제를 실행 하기 전에 합니다.

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
