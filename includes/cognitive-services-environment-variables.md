---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85073349"
---
## <a name="configure-an-environment-variable-for-authentication"></a>인증을 위한 환경 변수 구성

애플리케이션은 사용하는 Cognitive Services에 대한 액세스를 인증해야 합니다. 인증하려면 Azure 리소스의 키를 저장할 환경 변수를 만드는 것이 좋습니다. 

키를 받으면 애플리케이션을 실행하는 로컬 머신의 새 환경 변수에 씁니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. `your-key`를 리소스의 키 중 하나로 바꿉니다.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 예제를 실행합니다.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

#### <a name="macos"></a>[macOS](#tab/unix)

.bash_profile을 편집하고, 환경 변수를 추가합니다.

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

***
