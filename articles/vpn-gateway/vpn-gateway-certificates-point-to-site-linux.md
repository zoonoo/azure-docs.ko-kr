---
title: '지점 및 사이트 간에 대한 인증서 생성 및 내보내기: Linux: CLI: Azure | Microsoft Docs'
description: Linux(strongSwan) CLI를 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305732"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Linux strongSwan CLI를 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 문서에서는 Linux CLI 및 strongSwan을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [Powershell](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 문서를 참조하세요.

> [!NOTE]
> 이 문서의 단계에는 strongSwan이 필요합니다.
>

이 문서의 단계에 사용되는 컴퓨터 구성은 다음과 같습니다.

| | |
|---|---|
|**컴퓨터**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**종속성**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>strongSwan 설치

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

GUI를 사용하여 strongSwan을 설치하는 방법에 대한 자세한 내용은 [클라이언트 구성](point-to-site-vpn-client-configuration-azure-cert.md#install) 문서의 단계를 참조하세요.

## <a name="generate-keys-and-certificate"></a>키 및 인증서 생성

1. CA 인증서를 생성합니다.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. CA 인증서를 base64 형식으로 인쇄합니다. Azure에서 지원하는 형식입니다. 나중에 이것을 P2S 구성의 일부로 Azure에 업로드합니다.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. 사용자 인증서를 생성합니다.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. 사용자 인증서를 포함하는 p12 번들을 생성합니다. 이 번들은 [클라이언트 구성 파일](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)을 사용할 때 다음 단계에서 사용됩니다.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)에 대한 지점 및 사이트 간 구성을 계속합니다.