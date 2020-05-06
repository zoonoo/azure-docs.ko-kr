---
title: 자습서 - Key Vault에서 인증서의 자동 회전 빈도 업데이트 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Key Vault에서 인증서의 자동 회전 빈도를 업데이트하는 방법을 보여주는 자습서
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107560"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>자습서: Key Vault에서 인증서의 자동 회전 구성

Azure Key Vault를 사용하면 디지털 인증서를 쉽게 프로비저닝하고, 관리하고, 배포할 수 있습니다. 인증서는 인증 기관에서 서명한 퍼블릭 및 프라이빗 SSL/TLS 인증서일 수도 있고 자체 서명된 인증서일 수도 있습니다. 또한 Key Vault는 파트너 관계를 맺은 인증 기관을 통해 인증서를 요청하고 갱신할 수 있으며, 인증서 수명 주기 관리를 위한 강력한 솔루션을 제공합니다. 이 자습서에서는 인증서의 특성, 즉, 유효 기간, 자동 회전 빈도, CA를 업데이트합니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 검토합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 인증서 관리
> * 인증 기관 공급자 계정 추가
> * 인증서 유효 기간 업데이트
> * 인증서 자동 회전 빈도 업데이트
> * Azure Powershell을 사용하여 인증서 특성 업데이트


시작하기 전에 [Key Vault 기본 개념](../general/basic-concepts.md)을 읽어보세요. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

작업을 수행할 기존 Key Vault를 선택하거나 새로 만듭니다 [(키 자격 증명 모음을 만드는 단계).](../quick-create-portal.md) 이 예제에서 Vault 이름은 **Example-Vault**입니다. 

![Key Vault 만들기 완료 후 출력](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Key Vault에서 인증서 만들기

자격 증명 모음에서 인증서를 만들거나 가져옵니다 [(Key Vault에서 인증서를 만드는 단계).](../quick-create-portal.md) 여기서는 **ExampleCertificate**라는 인증서를 사용합니다.

> [!NOTE]
> Azure Key Vault에서 인증서의 수명 주기 특성은 인증서를 만들 때는 물론이고 만든 후에도 업데이트할 수 있습니다. 
## <a name="updating-certificates-life-cycle-attributes"></a>인증서의 수명 주기 특성 업데이트

Key Vault에서 만든 인증서는 다음 중 하나입니다. 
- 자체 서명된 인증서
- Key Vault와 파트너 관계인 CA(인증 기관)를 통해 만든 인증서
- Key Vault와 파트너 관계가 아닌 인증 기관을 통해 만든 인증서

다음 인증 기관은 현재 Key Vault와 파트너 관계를 맺고 있습니다.
- DigiCert - Key Vault는 DigiCert를 통해 OV TLS/SSL 인증서를 제공합니다.
- GlobalSign - Key Vault는 GlobalSign을 통해 OV TLS/SSL 인증서를 제공합니다.

Azure Key Vault는 파트너 관계를 맺은 인증 기관을 통해 인증서를 자동으로 회전합니다. 설정된 파트너 관계를 통해 Key Vault는 인증서를 자동으로 요청하고 갱신합니다. 따라서 **자동 회전 기능은 Key Vault와 파트너 관계가 아닌 CA를 통해 만든 인증서에는 적용되지 않습니다.** 

> [!NOTE]
> CA 공급자의 계정 관리자는 Key Vault에서 TLS/SSL 인증서를 만들고, 갱신하고, 사용할 자격 증명을 만듭니다.
![인증 기관](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>인증서를 만들 때 인증서의 수명 주기 특성 업데이트

1. Key Vault 속성 페이지에서 **인증서**를 선택합니다.
2. **생성/가져오기**를 클릭합니다.
3. **인증서 만들기** 화면에서 다음 값을 업데이트합니다.
    

    - **유효 기간**: 값(월 단위)을 입력합니다. 수명이 짧은 인증서를 만드는 것이 보안에 좋습니다. 기본적으로 새로 만드는 인증서의 유효 값은 12개월입니다.
    - **수명 동작 유형**: 인증서의 자동 갱신 및 경고 동작을 선택합니다. 선택하는 항목에 따라 '백분율 수명' 또는 '만료 전 일수'를 업데이트합니다. 기본적으로 인증서의 자동 갱신은 수명의 80%로 설정됩니다.<br> 드롭다운 메뉴에서 다음 옵션을 선택합니다.

    |  지정된 시간에 자동으로 갱신| 지정된 시간에 모든 담당자에게 이메일 보내기 |
    |-----------|------|
    |이 옵션을 선택하면 자동 회전이 켜집니다. | 이 옵션을 선택하면 자동으로 회전되지 않고, 담당자에게 경고만 합니다.|
        


4. **만들기**를 클릭합니다.

![인증서 수명 주기](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>저장된 인증서의 수명 주기 특성 업데이트

1. Key Vault를 선택합니다.
2. Key Vault 속성 페이지에서 **인증서**를 선택합니다.
3. 업데이트하려는 인증서를 선택합니다. 여기서는 **ExampleCertificate**라는 인증서를 사용합니다.
4. 상단 메뉴 모음에서 **발급 정책**을 선택합니다.

![인증서 속성](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. **발급 정책** 화면에서 다음 값을 업데이트합니다.
- **유효 기간**: 값(월 단위)을 업데이트합니다.
- **수명 동작 유형**: 인증서의 자동 갱신 및 경고 동작을 선택합니다. 선택하는 항목에 따라 '백분율 수명' 또는 '만료 전 일수'를 업데이트합니다. 

![인증서 속성](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. **Save**를 클릭합니다.

> [!IMPORTANT]
> 인증서의 수명 동작 유형을 변경하면 기존 인증서의 수정 사항이 즉시 기록됩니다.


### <a name="updating-certificates-attributes-using-powershell"></a>PowerShell을 사용하여 인증서 특성 업데이트

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 인증서 목록의 갱신 정책을 수정하려면 VaultName,CertName을 포함하는 File.csv를 입력합니다. <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
[여기](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)에서 매개 변수에 대해 자세히 알아보기

## <a name="clean-up-resources"></a>리소스 정리

다른 Key Vault 빠른 시작과 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요 없으면 리소스 그룹을 삭제하고 Key Vault 및 관련 리소스를 삭제합니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 인증서의 수명 주기를 업데이트했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

[Azure Key Vault에서 인증서 생성 관리](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)에 대해 자세히 알아봅니다.
- [Key Vault 개요 검토](../general/overview.md)