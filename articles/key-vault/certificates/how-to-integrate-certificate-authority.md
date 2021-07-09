---
title: DigiCert 인증 기관과 Key Vault 통합
description: 이 문서에서는 Key Vault를 DigiCert 인증 기관과 통합하여 네트워크에 대한 인증서를 프로비전, 관리 및 배포할 수 있는 방법을 설명합니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 99be94db2dd904db09b8b265b74442d5ba31b629
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109845603"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>DigiCert 인증 기관과 Key Vault 통합

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 디지털 인증서는 전자 거래에서 신원 증명을 설정하는 전자 자격 증명입니다. 

Azure Key Vault 사용자는 자신의 키 자격 증명 모음에서 직접 DigiCert 인증서를 생성할 수 있습니다. Key Vault는 DigiCert 인증 기관과 신뢰할 수 있는 파트너 관계를 맺고 있습니다. 이 파트너 관계는 DigiCert에서 발급한 인증서에 대한 엔드투엔드 인증서 수명 주기 관리를 보장합니다.

인증서에 대한 일반적인 내용은 [Azure Key Vault 인증서](./about-certificates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 절차를 완료하려면 다음이 필요합니다.
* 키 자격 증명 모음. 다음 빠른 시작 중 하나의 단계를 완료하여 기존 키 자격 증명 모음을 사용하거나 새로 만들 수 있습니다.
   - [Azure CLI를 사용하여 키 자격 증명 모음 만들기](../general/quick-create-cli.md)
   - [Azure PowerShell을 사용하여 키 자격 증명 모음 만들기](../general/quick-create-powershell.md)
   - [Azure Portal을 사용하여 키 자격 증명 모음 만들기](../general/quick-create-portal.md)
*   활성화된 DigiCert CertCentral 계정. CertCentral 계정에 [가입](https://www.digicert.com/account/signup/)합니다.
*   계정의 관리자 수준 권한.


### <a name="before-you-begin"></a>시작하기 전에

DigiCert CertCentral 계정에서 다음과 같은 정보가 있는지 확인합니다.
-   CertCentral 계정 ID
-   조직 ID
-   API 키

## <a name="add-the-certificate-authority-in-key-vault"></a>Key Vault에 인증 기관 추가 
DigiCert CertCentral 계정에서 위의 정보를 수집한 후에는 키 자격 증명 모음의 인증 기관 목록에 DigiCert를 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1.  DigiCert 인증 기관을 추가하려면 추가할 키 자격 증명 모음으로 이동합니다. 
2.  Key Vault 속성 페이지에서 **인증서** 를 선택합니다.
3.  **인증 기관** 탭을 선택합니다. :::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png" alt-text="인증 기관 탭 선택을 보여주는 스크린샷":::
4.  **추가** 를 선택합니다. :::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png" alt-text="인증 기관 탭의 추가 단추를 보여주는 스크린샷":::
5.  **인증 기관 만들기** 에서 다음 값을 입력합니다.
    -   **이름**: 식별 가능한 발급자 이름입니다. 예: **DigiCertCA**.
    -   **공급자**: **DigiCert**.
    -   **계정 ID**: DigiCert CertCentral 계정 ID입니다.
    -   **계정 암호**: DigiCert CertCentral 계정에서 생성한 API 키입니다.
    -   **조직 ID**: DigiCert CertCentral 계정의 조직 ID입니다. 

1. **만들기** 를 선택합니다.
   
DigicertCA가 이제 인증 기관 목록에 있습니다.


### <a name="azure-powershell"></a>Azure PowerShell

명령 또는 스크립트에서 Azure PowerShell을 사용하여 Azure 리소스를 만들고 관리할 수 있습니다. Azure는 브라우저에서 Azure Portal을 통해 사용할 수 있는 대화형 셸 환경인 Azure Cloud Shell을 호스트합니다.

PowerShell을 로컬에 설치하고 사용하기로 선택한 경우 이 절차를 완료하려면 Azure AZ PowerShell 모듈 1.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion`을 입력하면 버전을 확인할 수 있습니다. 업그레이드해야 하는 경우 [Azure AZ PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Login-AzAccount
```

1.  [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

    ```azurepowershell-interactive
    New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
    ```

2. 고유한 이름을 가진 키 자격 증명 모음을 만듭니다. 여기서 `Contoso-Vaultname`은 키 자격 증명 모음의 이름입니다.

   - **자격 증명 모음 이름**: `Contoso-Vaultname`
   - **리소스 그룹 이름**: `ContosoResourceGroup`
   - **위치**: `EastUS`

    ```azurepowershell-interactive
    New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
   ```

3. DigiCert CertCentral 계정에서 다음 값에 대한 변수를 정의합니다.

   - **계정 ID** 
   - **조직 ID** 
   - **API 키** 

   ```azurepowershell-interactive
   $accountId = "myDigiCertCertCentralAccountID"
   $org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
   $secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
   ```

4. 발급자를 설정합니다. 이렇게 하면 Digicert가 키 자격 증명 모음에 인증 기관으로 추가됩니다. [매개 변수에 대해 자세히 알아보세요.](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
   ```azurepowershell-interactive
   Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
   ```

5. Key Vault에서 직접 DigiCert의 인증서 및 인증서 발급에 대한 정책을 설정합니다.

   ```azurepowershell-interactive
   $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
   Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
   ```

이제 지정된 키 자격 증명 모음의 DigiCert 인증 기관에서 인증서가 발급됩니다.

## <a name="troubleshoot"></a>문제 해결

발급된 인증서가 Azure Portal에서 사용할 수 없는 상태이면 인증서 작업을 보고 인증서에 대한 DigiCert 오류 메시지를 검토합니다.

:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png" alt-text="인증서 작업 창을 보여주는 스크린샷":::

오류 메시지: "이 인증서 요청을 완료하려면 병합을 수행하세요."
   
인증 기관에서 서명한 CSR을 병합하여 요청을 완료합니다. CSR 병합에 대한 자세한 내용은 [CSR 생성 및 병합](./create-certificate-signing-request.md)을 참조하세요.

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

- **Key Vault를 사용하여 DigiCert 와일드카드 인증서를 생성할 수 있나요?** 
   
  예, 하지만 DigiCert 계정을 구성한 방식에 따라 다릅니다.
- **DigiCert로 OV SSL 또는 EV SSL 인증서를 생성하려면 어떻게 해야 하나요?**
 
   Key Vault는 OV 및 EV SSL 인증서 생성을 지원합니다. 인증서를 만들 때 **고급 정책 구성** 을 선택한 다음, 인증서 유형을 지정하세요. 지원되는 값: OV SSL, EV SSL
   
   DigiCert 계정에서 허용하는 경우 Key Vault에서 이러한 유형의 인증서를 만들 수 있습니다. 이러한 유형의 인증서의 경우 DigiCert에서 유효성 검사가 수행됩니다. 유효성 검사에 실패하면 DigiCert 지원 팀이 도움을 드릴 수 있습니다. `subjectName`에서 정보를 정의하여 인증서를 만들 때 정보를 추가할 수 있습니다.

  예: `SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"`.
   
- **DigiCert에서 직접 인증서를 받는 것보다 통합을 통해 DigiCert 인증서를 만드는 데 시간이 더 오래 걸리나요?**
   
   아니요. 인증서를 만들 때 확인 프로세스에 시간이 걸릴 수 있습니다. 이런 프로세스는 DigiCert에서 제어됩니다.


## <a name="next-steps"></a>다음 단계

- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)