---
title: "지점 및 사이트 간에 대한 인증서 만들기 및 내보내기: makecert: Azure | Microsoft Docs"
description: "이 문서에는 makecert를 사용하여 자체 서명된 루트 인증서를 만들고, 공용 키를 내보내고, 클라이언트 인증서를 생성하는 단계가 나와 있습니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 0800a7754241eb409dbd86db82b586a3e19a29fc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>makecert를 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

> [!NOTE]
> Windows 10 컴퓨터에 액세스할 수 없는 경우에만 이러한 지침을 사용하여 지점 및 사이트 간 연결에 대한 자체 서명된 인증서를 생성합니다. makecert는 더 이상 사용되지 않습니다. 또한 makecert는 SHA-2 인증서를 만들 수 없으며, 여전히 P2S에 유효한 SHA-1만 만들 수 있습니다. 이러한 이유로, 가능한 경우 [PowerShell 단계](vpn-gateway-certificates-point-to-site.md)를 사용하는 것이 좋습니다. PowerShell 또는 makecert를 사용하여 만든 인증서는 해당 인증서를 만드는 데 사용한 운영 체제뿐 아니라 [지원되는 모든 클라이언트 운영 체제](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)에 설치할 수 있습니다.
>
>


이 문서에서는 자체 서명된 루트 인증서를 만들고, 공용 키를 내보내고, 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 이 문서에서는 지점 및 사이트 간 구성 지침 또는 지점 및 사이트 간 FAQ는 포함하지 않습니다. 다음 목록에서 '지점 및 사이트 간 구성' 문서 중 하나를 선택하여 정보를 확인할 수 있습니다.

> [!div class="op_single_selector"]
> * [자체 서명된 인증서 만들기 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [자체 서명된 인증서 만들기 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [지점 및 사이트 간 구성 - Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [지점 및 사이트 간 구성 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [지점 및 사이트 간 구성 - Classic - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 지점 및 사이트 간 연결을 구성할 때 루트 인증서의 공용 키(.cer 파일)를 Azure에 업로드해야 합니다. 또한 클라이언트 인증서는 루트 인증서에서 생성되고 VNet에 연결되는 모든 클라이언트 컴퓨터에 설치되어야 합니다. 클라이언트 인증서를 통해 클라이언트가 인증할 수 있습니다.

## <a name="rootcert"></a>자체 서명된 루트 인증서 만들기

다음 단계에서는 makecert를 사용하여 자체 서명된 인증서를 만드는 방법을 보여 줍니다. 이러한 단계는 배포 모델에 한정되지 않습니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

1. [makecert](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386968(v=vs.85).aspx)를 다운로드 및 설치합니다.
2. 설치가 끝나면 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>' 경로 아래에서 makecert.exe 유틸리티를 찾을 수 있습니다. 관리자 권한으로 명령 프롬프트를 열고 makecert 유틸리티의 위치로 이동합니다. 다음 예제를 사용할 수 있습니다.

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. 사용자 컴퓨터의 개인 인증서 저장소에 인증서를 만들고 설치합니다. 다음 예제에서는 P2S를 구성할 때 Azure에 업로드하는 해당 *.cer* 파일을 만듭니다. 'P2SRootCert' 및 'P2SRootCert.cer'을 인증서에 사용하려는 이름으로 바꿉니다. 인증서는 'Certificates - Current User\Personal\Certificates'에 위치합니다.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha1 -len 2048 -ss My "P2SRootCert.cer"
  ```

## <a name="cer"></a>공개 키(.cer) 내보내기

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

exported.cer 파일을 Azure에 업로드해야 합니다. 자세한 내용은 [지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-rm-ps.md#upload)을 참조하세요.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>자체 서명된 인증서 및 개인 키를 내보낸 다음 저장(선택 사항)

자체 서명된 루트 인증서를 내보낸 다음 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에서 해당 인증서를 설치하고 더 많은 클라이언트 인증서를 생성하거나 다른 .cer 파일을 내보낼 수 있습니다. 자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport)에서 설명하는 것과 같은 단계를 사용합니다.

## <a name="create-and-install-client-certificates"></a>클라이언트 인증서 만들기 및 설치

자체 서명된 인증서를 클라이언트 컴퓨터에 직접 설치하지는 않으며, 자체 서명된 인증서에서 클라이언트 인증서를 생성해야 합니다. 그런 다음 클라이언트 인증서를 클라이언트 컴퓨터로 내보낸 후 설치해야 합니다. 이러한 단계는 배포 모델에 관계없이 적용됩니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

### <a name="clientcert"></a>클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.
 
1. 자체 서명된 인증서를 만드는 데 사용한 동일한 컴퓨터에서 관리자로 명령 프롬프트를 엽니다.
2. 샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다.
  * *"P2SRootCert"*는 클라이언트 인증서를 생성 중인 자체 서명된 루트의 이름으로 변경합니다. 루트 인증서의 이름을 사용하고 있는지 확인합니다. 이 경우에 'CN=' 값은 자체 서명된 루트를 만들 때 지정한 값입니다.
  * *P2SChildCert*는 생성하는 클라이언트 인증서에 사용할 이름으로 변경합니다.

  다음 예제를 수정하지 않고 실행하면 루트 인증서 P2SRootCert에서 생성된 클라이언트 인증서 P2SChildcert가 개인 인증서 저장소에 저장됩니다.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha1
  ```

### <a name="clientexport"></a>클라이언트 인증서 내보내기

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>내보낸 클라이언트 인증서 설치

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 구성을 계속합니다. 

* **Resource Manager** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요.
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.
