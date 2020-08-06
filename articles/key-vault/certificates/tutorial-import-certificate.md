---
title: 자습서 - Azure Portal을 사용하여 Key Vault에서 인증서 가져오기 | Microsoft Docs
description: Azure Key Vault에서 인증서를 가져오는 방법을 보여 주는 자습서
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: ebf687716c8898acffb5e081fbf2f6217fe0f943
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503126"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>자습서: Azure Key Vault에서 인증서 가져오기

Azure Key Vault는 보안 비밀 저장소를 제공하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Azure Portal을 통해 Azure Key Vault를 만들고 관리할 수 있습니다. 이 자습서에서는 Key Vault를 만든 다음, 이를 사용하여 인증서를 가져옵니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 검토합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 포털을 사용하여 Key Vault에서 인증서를 가져옵니다.
> * CLI를 사용하여 Key Vault에서 인증서를 가져옵니다.
> * PowerShell을 사용하여 Key Vault에서 인증서를 가져옵니다.


시작하기 전에 [Key Vault 기본 개념](../general/basic-concepts.md)을 읽어보세요. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
2. 검색 상자에 **Key Vault**를 입력합니다.
3. 결과 목록에서 **Key Vault**를 선택합니다.
4. Key Vault 섹션에서 **만들기**를 선택합니다.
5. **Key Vault 만들기** 섹션에서 다음 정보를 제공합니다.
    - **Name**: 고유 이름은 필수입니다. 이 빠른 시작의 경우 **예제-자격 증명 모음**을 사용합니다. 
    - **구독**: 구독을 선택합니다.
    - **리소스 그룹**에서 **새로 만들기**를 선택하고 리소스 그룹 이름을 입력합니다.
    - **위치** 풀 다운 메뉴에서 위치를 선택합니다.
    - 다른 옵션은 기본값으로 그대로 둡니다.
6. 위의 정보를 제공한 후 **만들기**를 선택합니다.

아래에 나열된 두 개의 속성을 기록합니다.

* **자격 증명 모음 이름**: 이 예제에서 이는 **예제-자격 증명 모음**입니다. 다른 단계에서 이 이름을 사용합니다.
* **자격 증명 모음 URI**: 이 예에서는 https://example-vault.vault.azure.net/ 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 작업을 수행할 권한이 있는 유일한 계정입니다.

![Key Vault 만들기 완료 후 출력](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Key Vault로 인증서 가져오기

자격 증명 모음으로 인증서를 가져오려면 PEM 또는 PFX 인증서 파일이 디스크에 있어야 합니다. 이 경우 **ExampleCertificate**라는 파일 이름이 포함된 인증서를 가져옵니다.

> [!IMPORTANT]
> Azure Key Vault에서 지원되는 인증서 형식은 PFX 및 PEM입니다. 
> - .pem 파일 형식은 하나 이상의 X509 인증서 파일을 포함합니다.
> - .pfx 파일 형식은 여러 암호화 개체를 단일 파일(도메인에 대해 발급된 서버 인증서), 일치하는 프라이빗 키에 저장하기 위한 보관 파일 형식이며, 선택적으로 중간 CA를 포함할 수 있습니다.  

1. Key Vault 속성 페이지에서 **인증서**를 선택합니다.
2. **생성/가져오기**를 클릭합니다.
3. **인증서 만들기** 화면에서 다음 값을 선택합니다.
    - **인증서를 만드는 방법**: 가져오기
    - **인증서 이름**: ExampleCertificate
    - **인증서 파일 업로드**: 디스크에서 인증서 파일 선택
    - **암호**: 암호로 보호된 인증서 파일을 업로드하는 경우 여기에 해당 암호를 입력합니다. 그렇지 않으면 비워 둡니다. 인증서 파일을 성공적으로 가져오면 키 자격 증명 모음에서 해당 암호를 제거합니다.
4. **만들기**를 클릭합니다.

![인증서 속성](../media/certificates/tutorial-import-cert/cert-import.png)

**가져오기** 메서드를 사용하여 인증서를 추가하면 Azure Key Vault가 인증서 매개 변수(예: 유효 기간, 발급자 이름, 활성화 날짜 등)를 자동으로 채웁니다.

인증서를 성공적으로 가져왔다는 메시지가 표시되면 목록에서 인증서를 클릭하여 해당 속성을 볼 수 있습니다. 

![인증서 속성](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Azure CLI를 사용하여 인증서 가져오기

지정된 키 자격 증명 모음으로 인증서 가져오기 프라이빗 키를 포함하는 기존의 유효한 인증서를 Azure Key Vault로 가져오려면 가져올 파일이 PFX 또는 PEM 형식일 수 있습니다. 인증서가 PEM 형식인 경우 PEM 파일에는 키와 x509 인증서가 포함되어야 합니다. 이 작업을 수행하려면 인증서/가져오기 권한이 필요합니다.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

[매개 변수](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)에 대해 자세히 알아봅니다.

인증서를 가져온 후 [인증서 표시](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)를 사용하여 인증서를 볼 수 있습니다.


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

이제 Key Vault를 만들고, 인증서를 가져오고 인증서의 속성을 확인했습니다.

## <a name="import-a-certificate-using-azure-powershell"></a>Azure PowerShell을 사용하여 인증서 가져오기

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

[매개 변수](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)에 대해 자세히 알아봅니다.


## <a name="clean-up-resources"></a>리소스 정리

다른 Key Vault 빠른 시작과 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요 없으면 리소스 그룹을 삭제하고 Key Vault 및 관련 리소스를 삭제합니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Key Vault를 만들어 인증서를 가져왔습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault에서 인증서 생성 관리](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)에 대해 자세히 알아봅니다.
- [REST API를 사용하여 인증서 가져오기](/rest/api/keyvault/importcertificate/importcertificate)의 예제 참조
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토
