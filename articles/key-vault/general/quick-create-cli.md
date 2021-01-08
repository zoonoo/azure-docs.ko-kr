---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Key Vault 만들기
description: Azure CLI를 사용하여 Azure Key Vault를 만드는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e29a692e3fdad1bea7132b3bed50444c7398ba46
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936314"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 키 자격 증명 모음 만들기

Azure Key Vault는 [키](../keys/index.yml), [비밀](../secrets/index.yml) 및 [인증서](../certificates/index.yml)에 대한 보안 저장소를 제공하는 클라우드 서비스입니다. Key Vault에 대한 자세한 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요. 키 자격 증명 모음에 저장할 수 있는 항목에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-certificates.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 이 빠른 시작에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *ContosoResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

이전 단계에서 리소스 그룹에 Key Vault를 만듭니다. 몇 가지 정보를 제공해야 합니다.

- 키 자격 증명 모음 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있는 3~24자의 문자열

  > [!Important]
  > 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 <your-unique-keyvault-name>을 Key Vault의 이름으로 바꿉니다.

- 리소스 그룹 이름: **myResourceGroup**
- 위치: **EastUS**

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

이 cmdlet의 출력에는 새로 만든 Key Vault의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **자격 증명 모음 이름**: 위의 --name 매개 변수에 제공한 이름입니다.
- **자격 증명 모음 URI**: 이 예제에서 이는 https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요하지 않은 경우 Azure CLI [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고 삭제했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](overview.md) 참조
- [Azure Key Vault 보안 개요](security-overview.md)를 검토하세요.
- [Azure CLI az keyvault 명령](/cli/azure/keyvault)에 대한 참조를 참조하세요.

