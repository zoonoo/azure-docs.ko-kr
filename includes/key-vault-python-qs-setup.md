---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482137"
---
1. [활성 구독이 포함된 Azure 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)이 있는지 확인합니다.

1. [Python 2.7+ 또는 3.5.3+](https://www.python.org/downloads)를 설치합니다.

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.

1. 로컬 서비스 주체를 만들고 환경 변수를 통해 Python용 Azure Key Vault 클라이언트에서 사용할 수 있도록 설정하는 [로컬 개발용 인증 구성](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication)의 지침을 따릅니다. 

    Azure에서 직접 코드를 실행할 때 앱에서 관리 ID를 사용하는 경우 별도의 서비스 주체가 필요하지 않습니다.

1. 터미널 또는 명령 프롬프트에서 적절한 프로젝트 폴더를 만든 다음, [Python 가상 환경 사용](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)에 설명된 대로 Python 가상 환경을 만들고 활성화합니다.

1. Azure Active Directory ID 라이브러리를 설치합니다.

    ```terminal
    pip install azure.identity
    ```
