---
title: Azure Key Vault에서 CSR 만들기 및 병합
description: Azure Key Vault에서 CSR 만들기 및 병합
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 9772ea320ff28325ffdc8cdcb6e35947b182d1b3
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095519"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Key Vault에서 CSR 만들기 및 병합

Azure Key Vault는 프라이빗-공개 키 쌍을 사용하여 인증서 서명 요청을 만들고, 원하는 인증 기관을 통해 서명하는 것을 지원합니다. 서명 기관은 내부 엔터프라이즈 CA일 수도 있고 외부 퍼블릭 CA일 수도 있습니다. 인증서 서명 요청(CSR 또는 인증 요청이라고도 함)은 사용자가 디지털 인증서 발급을 요청하기 위해 CA(인증 기관)로 보내는 메시지입니다.

인증서에 대한 일반적인 내용은 [Azure Key Vault 인증서](/azure/key-vault/certificates/about-certificates)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>신뢰할 수 없는 CA에서 발급한 인증서를 Key Vault에 추가

다음 단계는 Key Vault와 파트너십을 맺지 않은 인증 기관에서 인증서를 만드는 데 도움이 됩니다(예를 들어 GoDaddy는 신뢰할 수 있는 키 자격 증명 모음 CA가 아님). 


### <a name="azure-powershell"></a>Azure PowerShell



1.  먼저 **인증서 정책을 만듭니다**. 이 시나리오에서 선택한 CA는 지원되는 CA가 아니며 따라서 IssuerName이 [Unknown]으로 설정되므로, Key Vault가 사용자 대신 발급자의 인증서를 등록하거나 갱신하지 않습니다.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. **인증서 서명 요청**을 만듭니다.

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. **CA가 서명한 CSR 요청**을 가져옵니다. `$certificateOperation.CertificateSigningRequest`는 인증서에 대해 base4로 인코딩된 인증서 서명 요청입니다. 이 BLOB을 가져와서 발급자의 인증서 요청 웹 사이트로 덤프할 수 있습니다. 이 단계는 CA마다 다르며, 가장 좋은 방법은 이 단계를 실행하는 방법에 대한 CA 지침을 살펴보는 것입니다. certreq 또는 openssl 같은 도구를 사용하여 서명된 인증서 요청을 가져와서 인증서 생성 프로세스를 완료할 수도 있습니다.


4. Key Vault에서 **서명된 인증서를 병합**합니다. 발급자가 인증서 요청에 서명한 후에는 서명된 인증서를 다시 가져와서 Azure Key Vault에 만든 초기 프라이빗-공개 키 쌍과 병합할 수 있습니다.

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    이제 인증서 요청이 성공적으로 병합되었습니다.

### <a name="azure-portal"></a>Azure portal

1.  원하는 CA에 대한 CSR을 생성하려면 인증서를 추가하려는 Key Vault로 이동합니다.
2.  Key Vault 속성 페이지에서 **인증서**를 선택합니다.
3.  **생성/가져오기** 탭을 선택합니다.
4.  **인증서 만들기** 화면에서 다음 값을 선택합니다.
    - **인증서를 만드는 방법**: Generate.
    - **인증서 이름:** ContosoManualCSRCertificate.
    - **CA(인증 기관) 유형:** 통합되지 않은 CA에서 발급한 인증서
    - **제목:** `"CN=www.contosoHRApp.com"`
    - 원하는 다른 값을 선택합니다. **만들기**를 클릭합니다.

    ![인증서 속성](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  인증서 목록에 인증서가 추가된 것을 볼 수 있습니다. 방금 만든 새 인증서를 선택합니다. 아직 CA가 인증서를 발급하지 않았기 때문에 인증서의 현재 상태는 ‘사용 안 함’입니다.
7. **인증서 작업** 탭을 클릭하고 **CSR 다운로드**를 선택합니다.
 ![인증서 속성](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  .csr 파일을 요청 서명에 사용할 CA로 가져옵니다.
9.  CA가 요청을 서명한 후, 동일한 인증서 작업 화면에서 인증서 파일을 **서명된 요청 병합**으로 다시 가져옵니다.

이제 인증서 요청이 성공적으로 병합되었습니다.

## <a name="troubleshoot"></a>문제 해결

발급된 인증서가 Azure Portal의 '사용 안 함' 상태이면 **인증서 작업** 보기를 계속 진행하여 해당 인증서에 대한 오류 메시지를 검토합니다.

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
