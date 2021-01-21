---
title: Azure Application Gateway 인증서 갱신
description: 애플리케이션 게이트웨이 수신기와 연결된 인증서를 갱신하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622183"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway 인증서 갱신

특정 시점에 TLS/SSL 암호화에 대해 application gateway를 구성한 경우 인증서를 갱신 해야 합니다.

Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 수신기와 연결된 인증서를 갱신할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

포털에서 수신기 인증서를 갱신하려면 애플리케이션 게이트웨이 수신기로 이동합니다. 갱신 해야 하는 인증서가 있는 수신기를 선택한 다음, **선택한 인증서 갱신 또는 편집** 을 선택 합니다.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="인증서 갱신":::

새 PFX 인증서를 업로드 하 고, 이름을 지정 하 고, 암호를 입력 한 다음, **저장** 을 선택 합니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell을 사용하여 인증서를 갱신하려면 다음 스크립트를 사용합니다.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>다음 단계

Azure 애플리케이션 Gateway를 사용 하 여 TLS 오프 로드를 구성 하는 방법을 알아보려면 [Tls 오프 로드 구성](./create-ssl-portal.md) 을 참조 하세요.