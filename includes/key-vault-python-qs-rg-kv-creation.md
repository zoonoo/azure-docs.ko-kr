---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967128"
---
1. `az group create` 명령을 사용하여 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    선호하는 경우 "eastus"를 사용자에게 가까운 위치로 변경할 수 있습니다.

1. `az keyvault create`를 사용하여 키 자격 증명 모음을 만듭니다.

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    `<your-unique-keyvault-name>`을 모든 Azure에서 고유한 이름으로 바꿉니다. 일반적으로 다른 번호 및 식별자와 함께 개인 또는 회사 이름을 사용합니다. 

1. 다음 코드에 Key Vault의 이름을 제공하는 환경 변수를 만듭니다.

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```