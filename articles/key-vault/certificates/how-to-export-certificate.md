---
title: Azure Key Vault의 인증서 내보내기
description: Azure Key Vault의 인증서 내보내기
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588895"
---
# <a name="export-certificate-from-azure-key-vault"></a>Azure Key Vault의 인증서 내보내기

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 인증서에 대한 일반적인 내용은 [Azure Key Vault 인증서](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)를 참조하세요.

## <a name="about-azure-key-vault-certificate"></a>Azure Key Vault 인증서 정보

### <a name="composition-of-certificate"></a>인증서 작성
Key Vault 인증서가 만들어지면 주소 지정 가능한 키와 암호도 동일한 이름으로 만들어집니다. Key Vault 키는 키 작업을 허용하고, Key Vault 비밀은 인증서 값을 비밀로 검색할 수 있게 합니다. Key Vault 인증서에는 공용 x509 인증서 메타데이터도 포함됩니다. [자세히 알아보기](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>내보내기 가능/불가능 키
Key Vault 인증서가 만들어지면 PFX 또는 PEM 형식의 프라이빗 키를 사용하여 주소 지정 가능한 비밀에서 해당 인증서를 검색할 수 있습니다. 인증서를 만드는 데 사용된 정책은 키를 내보낼 수 있다고 표시해야 합니다. 정책에서 내보낼 수 없다고 표시하는 경우 프라이빗 키는 비밀로 검색될 때 값에 포함되지 않습니다.

두 가지 유형의 키, 즉 RSA 또는 인증서가 있는 RSA HSM이 지원됩니다. 내보내기 가능한 키는 RSA에서만 허용되며, RSA HSM에서는 지원되지 않습니다. [자세히 알아보기](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Azure CLI, PowerShell 또는 포털을 사용하여 Azure Key Vault에 저장된 인증서를 내보낼 수 있습니다.

> [!NOTE]
> 키 자격 증명 모음에서 인증서를 가져올 때에만 인증서 암호를 입력하면 됩니다. Key Vault는 연결된 암호를 저장하지 않으므로, 인증서를 내보낼 때 암호는 비어 있습니다.

## <a name="exporting-certificate-using-cli"></a>CLI를 사용하여 인증서 내보내기
다음 명령을 사용하여 Key Vault 인증서의 **공개 부분**을 다운로드할 수 있습니다.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
예제 및 매개 변수 정의는 [여기를 참조](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)하세요.



전체 인증서(즉, **컴퍼지션의 공개 및 비공개 부분 모두**)를 다운로드하려는 경우 인증서를 비밀로 다운로드하면 됩니다.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
매개 변수 정의는 [여기를 참조](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)하세요.


## <a name="exporting-certificate-using-powershell"></a>PowerShell을 사용하여 인증서 내보내기

이 명령은 ContosoKV01이라는 키 자격 증명 모음에서 TestCert01이라는 인증서를 가져옵니다. 인증서를 pfx 파일로 다운로드하려면 다음 명령을 실행합니다. 이러한 명령은 SecretId에 액세스 한 다음, 콘텐츠를 pfx 파일로 저장합니다.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
그러면 프라이빗 키가 포함된 인증서의 전체 체인을 내보내고, 이 인증서는 암호로 보호됩니다.
```Get-AzKeyVaultCertificate``` 명령 및 매개 변수에 대한 자세한 내용은 [예제 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)를 참조하세요.

## <a name="exporting-certificate-using-portal"></a>포털을 사용하여 인증서 내보내기

포털의 [인증서] 블레이드에서 인증서를 만들거나 가져올 때 인증서가 성공적으로 생성되었다는 알림이 표시됩니다. 인증서를 선택할 때 현재 버전을 클릭하면 인증서를 다운로드할 수 있는 옵션이 표시됩니다.


"CER 형식으로 다운로드" 또는 "PFX/PEM 형식으로 다운로드" 단추를 클릭하면 인증서를 다운로드할 수 있습니다.


![인증서 다운로드](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>키 자격 증명 모음의 App Service Certificate 내보내기

Azure App Service Certificate는 포털 내에서 바로 SSL 인증서를 구매하여 Azure 앱에 할당할 수 있는 편리한 방법을 제공합니다. 다른 곳에서 사용할 수 있도록 포털에서 이러한 인증서를 PFX 파일로 내보낼 수도 있습니다.
가져온 앱 서비스 인증서를 **비밀 아래에 배치**할 수 있습니다.

앱 서비스 인증서를 내보내는 단계는 [여기를 참조](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)하세요.

## <a name="read-more"></a>자세히 알아보기
* [다양한 인증서 파일 형식 및 정의](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)