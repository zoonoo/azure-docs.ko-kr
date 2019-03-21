---
title: Azure Cloud Shell에서 Bash를 사용하여 Ansible 실행
description: Azure Cloud Shell에서 Bash를 사용하여 다양한 Ansible 작업을 수행하는 방법 알아보기
ms.service: azure
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 37426f1cdc51734c28b2c9739e26ad35f08e606f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791395"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Azure Cloud Shell에서 Bash를 사용하여 Ansible 실행

이 자습서에서는 Cloud Shell에서 Bash를 사용하여 Azure 구독을 Ansible 작업 영역으로 구성하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **Azure Cloud Shell 구성** - Azure Cloud Shell을 처음 사용하는 경우 Cloud Shell을 시작하고 구성하는 방법이 [Azure Cloud Shell의 Bash 빠른 시작](https://docs.microsoft.com/azure/cloud-shell/quickstart) 문서에 설명되어 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>자동 자격 증명 구성

Cloud Shell에 로그인하면 Ansible은 추가 구성없이 인프라를 관리하기 위해 Azure에서 인증을 받습니다. 둘 이상의 구독이 있는 경우 `AZURE_SUBSCRIPTION_ID` 환경 변수를 내보내 Ansible이 작동할 구독을 선택할 수 있습니다. 모든 Azure 구독을 나열하려면 다음 명령을 실행합니다.

```azurecli-interactive
az account list
```

작업하려는 구독의 **ID**를 사용하여 다음과 같이 **AZURE_SUBSCRIPTION_ID**를 설정합니다.

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>구성 확인
구성이 성공했는지 확인하려면 Ansible을 사용하여 리소스 그룹을 만듭니다.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Ansible을 사용하여 기본 가상 머신 만들기](/azure/virtual-machines/linux/ansible-create-vm)