---
title: Azure Key Vault에서 키 특성 만들기 및 검색 - Azure CLI
description: Azure CLI를 사용하여 Azure Key Vault에서 키를 설정하고 검색하는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 19c4a966bb4099e70efea224127bc9b7292ffd0a
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940551"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Key Vault에서 키 설정 및 검색

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Key Vault에서 Key Vault를 만듭니다. Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 살펴보세요. Azure CLI는 명령 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 작업을 완료하면 키를 저장할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬에서 CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 살펴보세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *ContosoResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

그런 다음, 이전 단계에서 만든 리소스 그룹에 Key Vault를 만듭니다. 몇 가지 정보를 제공해야 합니다.

- 이 빠른 시작의 경우 **Contoso-vault2**를 사용합니다. 테스트 시 고유한 이름을 제공해야 합니다.
- 리소스 그룹 이름은 **ContosoResourceGroup**입니다.
- 위치는 **미국 동부**입니다.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

이 cmdlet의 출력에는 새로 만든 Key Vault의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **자격 증명 모음 이름**: 이 예에서는 **Contoso-Vault2**입니다. 다른 Key Vault 명령에 이 이름을 사용합니다.
- **자격 증명 모음 URI**: 이 예에서는 https://contoso-vault2.vault.azure.net/ 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-key-to-key-vault"></a>Key Vault에 키 추가

자격 증명 모음에 키를 추가하려면 몇 가지 추가 단계만 수행하면 됩니다. 이 키는 애플리케이션에서 사용할 수 있습니다. 

아래 명령을 입력하여 호출된**ExampleKey**를 만듭니다.

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

이제 해당 URI를 사용하여 Azure Key Vault에 추가한 이 키를 참조할 수 있습니다. **'https://Contoso-Vault2.vault.azure.net/keys/ExampleKey '** 를 사용하여 현재 버전을 가져옵니다. 

이전에 저장한 키를 보려면 다음을 수행합니다.

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

지금까지 Key Vault를 만들고 키를 저장하고, 검색했습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 모든 관련된 리소스를 제거할 수 있습니다. 다음과 같이 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 키를 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure CLI az keyvault 명령](/cli/azure/keyvault?view=azure-cli-latest)에 대한 참조를 참조하세요.
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토
