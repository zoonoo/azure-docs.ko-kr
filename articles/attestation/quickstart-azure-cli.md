---
title: Azure CLI를 사용하여 Azure Attestation 설정
description: Azure CLI를 사용하여 증명 공급자를 설정하고 구성하는 방법입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020945"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Attestation 설정

증명을 설정하는 Azure CLI를 사용하여 Azure Attestation을 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>시작

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

1. [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) 명령을 실행하여 증명 공급자를 만듭니다.

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   **--certs-input-path** 매개 변수는 신뢰할 수 있는 서명 키 세트를 지정합니다. 이 매개 변수에 대한 파일 이름을 지정하는 경우 증명 공급자는 서명된 JWT 형식의 정책만 사용하여 구성해야 합니다. 그렇지 않은 경우 정책을 텍스트 또는 서명되지 않은 JWT 형식으로 구성할 수 있습니다. JWT에 대한 자세한 내용은 [기본 개념](basic-concepts.md)을 참조하세요. 인증서 샘플은 [증명 정책 서명자 인증서의 예](policy-signer-examples.md)를 참조하세요.

1. [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) 명령을 실행하여 상태 및 AttestURI와 같은 증명 공급자 속성을 검색합니다.

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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

[az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) 명령을 사용하여 증명 공급자를 삭제할 수 있습니다.

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>정책 관리

정책을 관리하려면 Azure AD 사용자에게 `Actions`에 대한 다음 권한이 필요합니다.

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

이러한 권한은 `Owner`(와일드카드 권한), `Contributor`(와일드카드 권한) 또는 `Attestation Contributor`(Azure Attestation 전용의 특정 권한)와 같은 역할을 통해 Azure AD 사용자에게 할당할 수 있습니다.  

정책을 읽으려면 Azure AD 사용자에게 `Actions`에 대한 다음 권한이 필요합니다.

- `Microsoft.Attestation/attestationProviders/attestation/read`

이 권한은 `Reader`(와일드카드 권한) 또는 `Attestation Reader`(Azure Attestation 전용의 특정 권한)와 같은 역할을 통해 Azure AD 사용자에게 할당할 수 있습니다.

여기서 설명하는 명령을 사용하여 증명 공급자에 대한 정책 관리를 제공합니다(한 번에 하나의 TEE 사용).

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) 명령은 지정된 TEE에 대한 현재 정책을 반환합니다.

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> 이 명령은 정책을 텍스트 및 JWT 형식으로 모두 표시합니다.

지원되는 TEE 유형은 다음과 같습니다.

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

[az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) 명령을 사용하여 지정된 TEE에 대한 새 정책을 설정합니다.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

JWT 형식의 증명 정책에는 `AttestationPolicy`라는 클레임이 포함되어야 합니다. 서명된 정책은 기존 정책 서명자 인증서에 해당하는 키로 서명해야 합니다.

정책 샘플은 [증명 정책의 예](policy-examples.md)를 참조하세요.

[az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) 명령은 지정된 TEE에 대한 새 정책을 설정합니다.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>정책 서명자 인증서 관리

다음 명령을 사용하여 증명 공급자에 대한 정책 서명자 인증서를 관리합니다.

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

정책 서명자 인증서는 `maa-policyCertificate`라는 클레임이 있는 서명된 JWT입니다. 클레임 값은 추가할 신뢰할 수 있는 서명 키를 포함하는 JWK입니다. JWT는 기존 정책 서명자 인증서에 해당하는 프라이빗 키로 서명해야 합니다. JWT 및 JWK에 대한 자세한 내용은 [기본 개념](basic-concepts.md)을 참조하세요.

정책 서명자 인증서의 모든 의미 체계 조작은 Azure CLI 외부에서 수행해야 합니다. Azure CLI와 관련하여 이는 간단한 문자열입니다.

인증서 샘플은 [증명 정책 서명자 인증서의 예](policy-signer-examples.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [코드 샘플을 사용하여 SGX enclave에서 증명 구현](/samples/browse/?expanded=azure&terms=attestation)
