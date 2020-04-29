---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059969"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>자체 서명된 루트 인증서 만들기

New-SelfSignedCertificate cmdlet을 사용하여 자체 서명된 루트 인증서를 만듭니다. 추가 매개 변수 정보는 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)를 참조하세요.

1. Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 상승된 권한으로 Windows PowerShell 콘솔을 엽니다. 이 예제는 Azure Cloud Shell "시도"에서는 작동하지 않습니다. 이 예제는 로컬에서 실행해야 합니다.
2. 다음 예제를 사용하여 자체 서명된 루트 인증서를 만듭니다. 다음 예제에서는 'Certificates-Current User\Personal\Certificates'에 자동으로 설치된 'P2SRootCert'라는 자체 서명된 루트 인증서를 만듭니다. *certmgr.msc* 또는 *사용자 인증서 관리*를 열어 인증서를 볼 수 있습니다.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. 이 루트 인증서를 만든 후에 클라이언트 인증서를 즉시 만들려면 PowerShell 콘솔을 열어 둡니다.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.

예제에서는 New-SelfSignedCertificate cmdlet을 사용하여 1년 후에 만료되는 클라이언트 인증서를 생성합니다. 클라이언트 인증서에 대해 다른 만료 값을 설정하는 등의 추가 매개 변수 정보는 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)를 참조하세요.

### <a name="example-1---powershell-console-session-still-open"></a>예제 1-PowerShell 콘솔 세션이 아직 열려 있습니다.

자체 서명된 루트 인증서를 만든 후에 PowerShell 콘솔을 닫지 않은 경우 이 예제를 사용합니다. 이 예제는 이전 섹션에서 계속되며 선언된 ‘$cert’ 변수를 사용합니다. 자체 서명 된 루트 인증서를 만든 후 PowerShell 콘솔을 닫았거나 새 PowerShell 콘솔 세션에서 추가 클라이언트 인증서를 만든 경우 [예제 2](#ex2)의 단계를 사용 합니다.

샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다.  자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>예 2-새 PowerShell 콘솔 세션

추가 클라이언트 인증서를 만들거나 자체 서명된 루트 인증서를 만드는 데 사용한 것과 동일한 PowerShell 세션을 사용하지 않을 경우 다음 단계를 사용합니다.

1. 컴퓨터에 설치되어 있는 자체 서명된 루트 인증서를 식별합니다. 이 cmdlet은 컴퓨터에 설치된 인증서 목록을 반환합니다.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. 반환된 목록에서 주체 이름을 찾은 다음 텍스트 파일에 옆에 있는 지문을 복사합니다. 다음 예제에는 두 개의 인증서가 있습니다. CN 이름은 자식 인증서를 생성하려는 자체 서명된 루트 인증서의 이름입니다. 이 경우에는 'P2SRootCert'입니다.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. 이전 단계의 지문을 사용하여 루트 인증서에 대한 변수를 선언합니다. THUMBPRINT을 자식 인증서를 생성하려는 루트 인증서의 지문으로 바꿉니다.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   예를 들어 이전 단계의 P2SRootCert에 대한 지문을 사용할 경우 변수는 다음과 같습니다.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. 샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다. 자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>루트 인증서 공개 키(.cer) 내보내기

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>자체 서명된 루트 인증서 및 프라이빗 키를 내보내고 저장(선택 사항)

자체 서명된 루트 인증서를 내보내고 백업으로 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에 설치하고 더 많은 클라이언트 인증서를 생성할 수 있습니다. 자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport)에서 설명하는 것과 같은 단계를 사용합니다.

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>클라이언트 인증서 내보내기

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
