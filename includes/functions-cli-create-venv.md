---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: 44969063765099d350e21abfbd07792891443911
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673187"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>가상 환경 만들기 및 활성화

적절한 폴더에서 다음 명령을 실행하여 `.venv`라는 가상 환경을 만들고 활성화합니다. Azure Functions에서 지원하는 Python 3.8, 3.7 또는 3.6을 사용해야 합니다.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python에서 venv 패키지를 Linux 배포에 설치하지 않은 경우 다음 명령을 실행합니다.

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

활성화된 가상 환경에서 이후의 모든 명령을 실행합니다. (가상 환경을 종료하려면 `deactivate`를 실행하세요.)
::: zone-end