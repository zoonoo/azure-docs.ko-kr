---
title: "지점 및 사이트 간에 대한 인증서 만들기 및 내보내기: PowerShell: Azure | Microsoft Docs"
description: "이 문서에는 Windows 10의 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공용 키를 내보내고, 클라이언트 인증서를 생성하는 단계가 나와 있습니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

이 문서에서는 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 이 문서에서는 지점 및 사이트 간 구성 지침 또는 지점 및 사이트 간 FAQ는 포함하지 않습니다. 다음 목록에서 '지점 및 사이트 간 구성' 문서 중 하나를 선택하여 정보를 확인할 수 있습니다.

> [!div class="op_single_selector"]
> * [자체 서명된 인증서 만들기 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [자체 서명된 인증서 만들기 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [지점 및 사이트 간 구성 - Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [지점 및 사이트 간 구성 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [지점 및 사이트 간 구성 - Classic - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 지점 및 사이트 간 연결을 구성할 때 루트 인증서의 공용 키(.cer 파일)를 Azure에 업로드해야 합니다. 또한 클라이언트 인증서는 루트 인증서에서 생성되고 VNet에 연결되는 모든 클라이언트 컴퓨터에 설치되어야 합니다. 클라이언트 인증서를 통해 클라이언트가 인증할 수 있습니다.


> [!NOTE]
> Windows 10을 실행하는 컴퓨터에서 이 문서의 단계를 수행해야 합니다. 인증서를 생성하는 데 필요한 PowerShell cmdlet은 Windows 10 운영 체제의 일부이며 다른 Windows 버전에서는 작동하지 않습니다. 인증서를 생성하려면 이러한 cmdlet만 있으면 됩니다. 인증서를 생성한 후에는 지원되는 모든 클라이언트 운영 체제에 설치할 수 있습니다. Windows 10 컴퓨터에 액세스할 수 없는 경우 makecert를 사용하여 인증서를 생성할 수 있습니다. 그러나 makecert를 사용하여 생성된 인증서는 지점 및 사이트 간과 호환되지만 SHA-2가 아닙니다. makecert 관련 지침은 [makecert를 사용하여 인증서 만들기](vpn-gateway-certificates-point-to-site-makecert.md)를 참조하세요. PowerShell 또는 makecert를 사용하여 만든 인증서는 해당 인증서를 만드는 데 사용한 운영 체제뿐 아니라 [지원되는 모든 클라이언트 운영 체제](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)에 설치할 수 있습니다.
> 
>

## <a name="rootcert"></a>자체 서명된 루트 인증서 만들기

다음 단계에서는 Windows 10에서 PowerShell을 사용하여 자체 서명된 루트 인증서를 만드는 방법을 보여 줍니다. 이러한 단계에 사용되는 cmdlet 및 매개 변수는 PowerShell 버전이 아닌 Windows 10 운영 체제에 속합니다. 그렇다고 해서 만든 인증서를 Windows 10에만 설치할 수 있는 것은 아닙니다. 지원되는 모든 클라이언트에 설치할 수 있습니다. 지원되는 클라이언트에 대한 자세한 내용은 [지점 및 사이트 간 FAQ](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)를 참조하세요.

1. Windows 10을 실행하는 컴퓨터에서 상승된 권한으로 Windows PowerShell 콘솔을 엽니다.
2. 다음 예제를 사용하여 자체 서명된 루트 인증서를 만듭니다. 다음 예제에서는 'Certificates-Current User\Personal\Certificates'에 자동으로 설치된 'P2SRootCert'라는 자체 서명된 루트 인증서를 만듭니다. *certmgr.msc* 또는 *사용자 인증서 관리*를 열어 인증서를 볼 수 있습니다.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>공개 키(.cer) 내보내기

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

exported.cer 파일을 Azure에 업로드해야 합니다. 자세한 내용은 [지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-rm-ps.md#upload)을 참조하세요.

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>자체 서명된 루트 인증서 및 공개 키를 내보낸 다음 저장(선택 사항)

자체 서명된 루트 인증서를 내보낸 다음 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에서 해당 인증서를 설치하고 더 많은 클라이언트 인증서를 생성하거나 다른 .cer 파일을 내보낼 수 있습니다. 자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport)에서 설명하는 것과 같은 단계를 사용합니다.

## <a name="clientcert"></a>클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.

Windows 10에서는 다음 PowerShell 단계를 사용하여 클라이언트 인증서를 생성해야 합니다. 이러한 단계에 사용되는 cmdlet 및 매개 변수는 PowerShell 버전이 아닌 Windows 10 운영 체제에 속합니다. 그렇다고 해서 만든 인증서를 Windows 10에만 설치할 수 있는 것은 아닙니다. 지원되는 클라이언트에 대한 자세한 내용은 [지점 및 사이트 간 FAQ](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)를 참조하세요.

### <a name="example-1"></a>예 1

이 예제에서는 이전 섹션에서 선언된 '$cert' 변수를 사용합니다. 자체 서명된 루트 인증서를 만든 후 PowerShell 콘솔을 종료했거나 새 PowerShell 콘솔 세션에서 추가 클라이언트 인증서를 생성하려는 경우 [예제 2](#ex2)의 단계를 사용합니다.

샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다.  자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>예 2

추가 클라이언트 인증서를 만들거나 자체 서명된 루트 인증서를 만드는 데 사용한 것과 동일한 PowerShell 세션을 사용하지 않을 경우 다음 단계를 사용합니다.

1. 컴퓨터에 설치되어 있는 자체 서명된 루트 인증서를 식별합니다. 이 cmdlet은 컴퓨터에 설치된 인증서 목록을 반환합니다.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. 반환된 목록에서 주체 이름을 찾은 다음 텍스트 파일에 옆에 있는 지문을 복사합니다. 다음 예제에는 두 개의 인증서가 있습니다. CN 이름은 자식 인증서를 생성하려는 자체 서명된 루트 인증서의 이름입니다. 이 경우에는 'P2SRootCert'입니다.

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 이전 단계의 지문을 사용하여 루트 인증서에 대한 변수를 선언합니다. THUMBPRINT을 자식 인증서를 생성하려는 루트 인증서의 지문으로 바꿉니다.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  예를 들어 이전 단계의 P2SRootCert에 대한 지문을 사용할 경우 변수는 다음과 같습니다.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다. 자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>클라이언트 인증서 내보내기   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>내보낸 클라이언트 인증서 설치

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 구성을 계속합니다. 

* **Resource Manager** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요. 
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.
