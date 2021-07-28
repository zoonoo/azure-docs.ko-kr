---
title: KeyVaultCertificateSelector UI 요소
description: Azure Portal의 Microsoft.KeyVault.KeyVaultCertificateSelector UI 요소에 대해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101224"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft.KeyVault.KeyVaultCertificateSelector UI 요소

키 자격 증명 모음 인증서를 선택하기 위한 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

사용자에게 사용 가능한 인증서를 선택하는 옵션이 제공됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector":::

사용자는 **인증서 선택** 을 선택한 후 키 자격 증명 모음과 키 자격 증명 모음의 인증서를 지정할 수 있습니다.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 인증서 선택":::

선택한 키 자격 증명 모음 및 인증서 이름을 표시하도록 컨트롤이 업데이트됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 선택한 인증서 표시":::

## <a name="schema"></a>스키마

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
