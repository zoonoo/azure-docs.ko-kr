---
title: Azure CLI를 사용하여 Azure Attestation 설정
description: Azure CLI를 사용하여 증명 공급자를 설정하고 구성하는 방법입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: fb8b0f12844ce1057bd3cfc4716a32ee64ec5586
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937222"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Attestation 설정

증명을 설정하는 Azure CLI를 사용하여 Azure Attestation을 시작합니다.

## <a name="get-started"></a>시작

1. 아래 CLI 명령을 사용하여 이 확장을 설치합니다.

   ```azurecli
   az extension add --name attestation
   ```
   
1. 버전 확인

   ```azurecli
   az extension show --name attestation --query version
   ```

1. 다음 명령을 사용하여 Azure에 로그인합니다.

   ```azurecli
   az login
   ```

1. 필요한 경우 Azure Attestation에 대한 구독으로 전환합니다.

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az provider register](/cli/azure/provider#az_provider_register) 명령을 사용하여 Microsoft.Attestation 리소스 공급자를 구독에 등록합니다.

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Azure 리소스 공급자에 대한 자세한 내용 및 이를 구성하고 관리하는 방법은 [Azure 리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

   > [!NOTE]
   > 리소스 공급자는 구독에 한 번만 등록하면 됩니다.

1. 증명 공급자에 대한 리소스 그룹을 만듭니다. 클라이언트 애플리케이션 인스턴스가 있는 가상 머신을 포함하여 다른 Azure 리소스를 동일한 리소스 그룹에 배치할 수 있습니다. [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>증명 공급자 만들기 및 관리

다음은 증명 공급자를 만들고 관리하는 데 사용할 수 있는 명령입니다.

1. [az attestation create](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_create) 명령을 실행하여 증명 공급자를 만듭니다.

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. [az attestation show](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_show) 명령을 실행하여 상태 및 AttestURI와 같은 증명 공급자 속성을 검색합니다.

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   이 명령은 다음 출력과 같은 값을 표시합니다.

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

[az attestation delete](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_delete) 명령을 사용하여 증명 공급자를 삭제할 수 있습니다.

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>정책 관리

여기에 설명된 명령을 사용하여 한 번의 하나의 증명 유형으로 증명 공급자에 대한 정책 관리를 제공합니다.

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_show) 명령은 지정된 TEE에 대한 현재 정책을 반환합니다.

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> 이 명령은 정책을 텍스트 및 JWT 형식으로 모두 표시합니다.

지원되는 TEE 유형은 다음과 같습니다.

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

[az attestation policy set](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_set) 명령을 사용하여 지정된 증명 유형에 대한 새 정책을 설정합니다.

파일 경로를 사용하여 지정된 종류의 증명 유형에 대해 텍스트 형식으로 정책을 설정하려면 다음을 수행합니다.

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

파일 경로를 사용하여 지정된 종류의 증명 유형에 대해 JWT 형식으로 정책을 설정하려면 다음을 수행합니다.

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>다음 단계

- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [코드 샘플을 사용하여 SGX enclave에서 증명 구현](/samples/browse/?expanded=azure&terms=attestation)
