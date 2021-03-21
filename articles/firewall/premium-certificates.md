---
title: Azure Firewall 프리미엄 미리 보기 인증서
description: Azure 방화벽 프리미엄 미리 보기에서 TLS 검사를 적절히 구성 하려면 중간 CA 인증서를 구성 하 고 설치 해야 합니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: 47ebc752dedd72bbdedc02908911f1686584acda
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615502"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Azure Firewall 프리미엄 미리 보기 인증서 

> [!IMPORTANT]
> Azure 방화벽 프리미엄은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

 Azure 방화벽 프리미엄 미리 보기 TLS 검사를 올바르게 구성 하려면 유효한 중간 CA 인증서를 제공 하 고 Azure Key vault에 보관 해야 합니다.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Azure 방화벽 프리미엄 미리 보기에서 사용 하는 인증서

일반적인 배포에는 다음과 같은 세 가지 유형의 인증서가 사용 됩니다.

- **중간 CA 인증서 (CA 인증서)**

   CA (인증 기관)는 디지털 인증서에 서명 하는 데 신뢰할 수 있는 조직입니다. CA는 인증서를 요청 하는 회사 또는 개인의 id와 적법성를 확인 합니다. 확인에 성공 하면 CA는 서명 된 인증서를 발급 합니다. 서버에서 SSL/TLS 핸드셰이크 중에 클라이언트 (예: 웹 브라우저)에 인증서를 제공 하는 경우 클라이언트는 *알려진 좋은* 서명자 목록과 비교 하 여 서명을 확인 하려고 시도 합니다. 웹 브라우저는 일반적으로 호스트를 식별 하기 위해 암시적으로 신뢰 하는 Ca 목록과 함께 제공 됩니다. 기관이 해당 인증서를 서명 하는 일부 사이트와 같이 목록에 없는 경우 브라우저는 인증서가 인식 된 기관에서 서명 되지 않은 것을 사용자에 게 알리고 검증 되지 않은 사이트와의 통신을 계속할지 묻는 메시지를 사용자에 게 표시 합니다.

- **서버 인증서 (웹 사이트 인증서)**

   특정 도메인 이름에 연결 된 인증서입니다. 웹 사이트에 유효한 인증서가 있는 경우이는 인증 기관에서 웹 주소가 실제로 해당 조직에 속해 있는지 확인 하는 단계를 수행 했음을 의미 합니다. URL을 입력 하거나 보안 웹 사이트에 대 한 링크를 따라 브라우저에서 인증서를 확인 하 여 다음 특성을 확인 합니다.
   - 웹 사이트 주소는 인증서의 주소와 일치 합니다.
   - 인증서는 브라우저가 *신뢰할 수* 있는 기관으로 인식 하는 인증 기관에 의해 서명 됩니다.
   
   사용자가 신뢰할 수 없는 인증서를 사용 하 여 서버에 연결할 수 있는 경우가 있습니다. Azure 방화벽은 서버가 연결을 종료 한 것 처럼 연결을 삭제 합니다.

- **루트 CA 인증서 (루트 인증서)**

   인증 기관은 트리 구조 형식으로 여러 인증서를 발급할 수 있습니다. 루트 인증서는 트리의 최상위 인증서입니다.

Azure 방화벽 프리미엄 미리 보기는 아웃 바운드 HTTP/S 트래픽을 가로채서 용 서버 인증서를 자동으로 생성할 수 있습니다 `www.website.com` . 이 인증서는 사용자가 제공 하는 중간 CA 인증서를 사용 하 여 생성 됩니다. 이 절차를 수행 하려면 최종 사용자 브라우저와 클라이언트 응용 프로그램에서 조직의 루트 CA 인증서 또는 중간 CA 인증서를 신뢰 해야 합니다. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="인증서 프로세스":::

## <a name="intermediate-ca-certificate-requirements"></a>중간 CA 인증서 요구 사항

CA 인증서가 다음 요구 사항을 준수 하는지 확인 합니다.

- Key Vault 암호로 배포 되는 경우 인증서와 개인 키를 사용 하 여 암호 없는 PFX (Pkcs12)를 사용 해야 합니다.

- 단일 인증서 여야 하며 전체 인증서 체인을 포함 해서는 안 됩니다.  

- 1 년 앞으로 유효 해야 합니다.  

- 최소 크기인 4096 바이트의 RSA 개인 키 여야 합니다.  

- `KeyUsage`플래그와 함께 중요로 표시 된 확장 `KeyCertSign` (RFC 5280; 4.2.1.3 키 사용)이 있어야 합니다.

- 이 파일에는 `BasicContraints` 중요 한 것으로 표시 된 확장 (RFC 5280; 4.2.1.9 Basic 제약 조건)이 있어야 합니다.  

- `CA`플래그는 TRUE로 설정 해야 합니다.

- 경로 길이는 1 보다 크거나 같아야 합니다.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md)는 비밀, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure 방화벽 프리미엄은 방화벽 정책에 연결 된 서버 인증서에 대 한 Key Vault와의 통합을 지원 합니다.
 
키 자격 증명 모음을 구성 하려면:

- 키 쌍을 포함 하는 기존 인증서를 키 자격 증명 모음으로 가져와야 합니다. 
- 또는 암호 없는 base-64로 인코딩된 PFX 파일로 저장 된 key vault 암호를 사용할 수도 있습니다.  PFX 파일은 개인 키와 공개 키를 모두 포함 하는 디지털 인증서입니다.
- 인증서 만료 날짜를 기반으로 경고를 구성할 수 있으므로 CA 인증서 가져오기를 사용 하는 것이 좋습니다.
- 인증서 나 암호를 가져온 후에는 인증서/암호에 대 한 액세스 권한을 id에 부여할 수 있도록 키 자격 증명 모음에 액세스 정책을 정의 해야 합니다.
- 제공 된 CA 인증서를 Azure 워크 로드에서 신뢰 해야 합니다. 올바르게 배포 되었는지 확인 합니다.

Azure 방화벽에서 사용자를 대신 하 여 Key Vault에서 인증서를 검색 하는 데 사용 하는 기존의 사용자 할당 관리 id를 만들거나 다시 사용할 수 있습니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 란?](../active-directory/managed-identities-azure-resources/overview.md) 을 참조 하세요. 

## <a name="configure-a-certificate-in-your-policy"></a>정책에서 인증서 구성

방화벽 프리미엄 정책에서 CA 인증서를 구성 하려면 정책을 선택한 다음 **TLS 검사 (미리 보기)** 를 선택 합니다. **TLS 검사** 페이지에서 **사용** 을 선택 합니다. 그런 다음, 다음 그림에 표시 된 것 처럼 Azure Key Vault에서 CA 인증서를 선택 합니다.

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure 방화벽 프리미엄 개요 다이어그램":::
 
> [!IMPORTANT]
> Azure Portal에서 인증서를 보고 구성 하려면 Azure 사용자 계정을 Key Vault 액세스 정책에 추가 해야 합니다. **비밀 사용 권한** 아래에서 사용자 계정 **가져오기** 및 **목록을** 지정 합니다.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault 액세스 정책":::


## <a name="create-your-own-self-signed-ca-certificate"></a>자체 서명 된 CA 인증서 만들기

TLS 검사를 테스트 하 고 확인 하는 데 도움이 되도록 다음 스크립트를 사용 하 여 자체 서명 된 루트 CA 및 중간 CA를 만들 수 있습니다.

> [!IMPORTANT]
> 프로덕션의 경우에는 회사 PKI를 사용 하 여 중간 CA 인증서를 만들어야 합니다. 회사 PKI는 기존 인프라를 활용 하 고 모든 끝점 컴퓨터에 대 한 루트 CA 배포를 처리 합니다. 자세한 내용은 [Azure 방화벽 미리 보기에 대 한 엔터프라이즈 CA 인증서 배포 및 구성](premium-deploy-certificates-enterprise-ca.md)을 참조 하세요.

이 스크립트에는 다음과 같은 두 가지 버전이 있습니다.
- bash 스크립트 `cert.sh` 
- PowerShell 스크립트 `cert.ps1` 

 또한 두 스크립트 모두 `openssl.cnf` 구성 파일을 사용 합니다. 스크립트를 사용 하려면 `openssl.cnf` , 또는의 내용을 `cert.sh` `cert.ps1` 로컬 컴퓨터에 복사 합니다.

스크립트는 다음 파일을 생성 합니다.
- Rootca.cer/Rootca.cer-루트 CA 공용 인증서 및 개인 키입니다.
- interCA .crt/interCA. 키-중간 CA 공용 인증서 및 개인 키
- interCA .pfx-방화벽에서 사용 되는 중간 CA pkcs12 패키지

> [!IMPORTANT]
> Rootca.cer는 안전한 오프 라인 위치에 저장 해야 합니다. 스크립트는 1024 일의 유효 기간 동안 인증서를 생성 합니다.
> 스크립트에는 로컬 컴퓨터에 openssl 바이너리가 설치 되어 있어야 합니다. 자세한 내용은 https://www.openssl.org/을 참조하십시오.
> 
인증서를 만든 후에 다음 위치에 배포 합니다.
- Rootca.cer-끝점 컴퓨터에서 배포 합니다 (공용 인증서에만 해당).
- interCA .pfx-Key Vault에서 인증서로 가져오고 방화벽 정책에 할당 합니다.

### <a name="opensslcnf"></a>**openssl. my.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash 스크립트-cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell-cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>문제 해결

CA 인증서가 올바르지만 TLS 검사에서 Fqdn 또는 Url에 액세스할 수 없는 경우 다음 항목을 확인 하세요.

- 웹 서버 인증서가 유효한 지 확인 합니다.  

- 루트 CA 인증서가 클라이언트 운영 체제에 설치 되어 있는지 확인 하십시오.  

- 브라우저 또는 HTTPS 클라이언트에 유효한 루트 인증서가 포함 되어 있는지 확인 합니다. Firefox 및 일부 다른 브라우저에는 특별 한 인증 정책이 있을 수 있습니다.  

- 응용 프로그램 규칙의 URL 대상 형식이 올바른 경로 및 대상 HTML 페이지에 포함 된 다른 하이퍼링크를 포함 하는지 확인 합니다. 필요한 전체 URL 경로를 쉽게 검사 하기 위해 와일드 카드를 사용할 수 있습니다.  


## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 프리미엄 기능에 대 한 자세한 정보](premium-features.md)
