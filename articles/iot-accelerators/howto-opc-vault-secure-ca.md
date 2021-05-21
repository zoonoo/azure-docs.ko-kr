---
title: OPC 자격 증명 모음 인증서 관리 서비스를 안전하게 실행하는 방법 - Azure
description: Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 안전하게 실행하는 방법을 설명하고, 고려해야 할 다른 보안 지침을 검토합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646224"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>OPC 자격 증명 모음 인증서 관리 서비스를 안전하게 실행

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

이 문서에서는 Azure에서 OPC 자격 증명 모음 인증서 관리 서비스를 안전하게 실행하는 방법을 설명하고, 고려해야 할 다른 보안 지침을 검토합니다.

## <a name="roles"></a>역할

### <a name="trusted-and-authorized-roles"></a>신뢰할 수 있고 권한 있는 역할

OPC 자격 증명 모음 마이크로서비스를 사용하면 고유한 역할에서 서비스의 다양한 부분에 액세스할 수 있습니다.

> [!IMPORTANT]
> 배포하는 동안 스크립트는 배포 스크립트를 실행하는 사용자만 모든 역할의 사용자로 추가합니다. 프로덕션 배포의 경우 이 역할 할당을 검토하고 아래 지침에 따라 적절하게 다시 구성해야 합니다. 이 작업을 수행하려면 Azure AD(Azure Active Directory) 엔터프라이즈 애플리케이션 포털에서 역할 및 서비스를 수동으로 할당해야 합니다.

### <a name="certificate-management-service-roles"></a>인증서 관리 서비스 역할

OPC 자격 증명 모음 마이크로서비스에서 정의하는 역할은 다음과 같습니다.

- **읽기 권한자**: 읽기 액세스 권한은 기본적으로 테넌트의 모든 인증된 사용자에게 있습니다. 
  - 애플리케이션 및 인증서 요청에 대한 읽기 액세스 권한입니다. 애플리케이션 및 인증서 요청을 나열하고 쿼리할 수 있습니다. 또한 디바이스 검색 정보 및 퍼블릭 인증서는 읽기 액세스 권한으로 액세스할 수 있습니다.
- **쓰기 권한자**: 특정 작업에 대한 쓰기 권한을 추가할 수 있는 쓰기 권한자 역할은 사용자에게 할당됩니다. 
  - 애플리케이션 및 인증서 요청에 대한 읽기/쓰기 액세스 권한입니다. 애플리케이션을 등록, 업데이트 및 등록 취소할 수 있습니다. 인증서 요청을 만들고, 승인된 프라이빗 키 및 인증서를 가져올 수 있습니다. 프라이빗 키를 삭제할 수도 있습니다.
- **승인자**: 인증서 요청을 승인하거나 거부할 수 있는 승인자 역할은 사용자에게 할당됩니다. 역할에는 다른 역할이 포함되지 않습니다.
  - OPC 자격 증명 모음 마이크로서비스 API에 액세스할 수 있는 승인자 역할 외에도 인증서에 서명할 수 있는 Azure Key Vault의 키 서명 권한도 있어야 합니다.
  - 쓰기 권한자 역할 및 승인자 역할은 다른 사용자에게 할당해야 합니다.
  - 승인자의 주 역할은 인증서 요청의 생성 및 거부에 대한 승인입니다.
- **관리자**: 인증서 그룹을 관리할 수 있는 관리자 역할은 사용자에게 할당됩니다. 이 역할은 승인자 역할을 지원하지 않지만 쓰기 권한자 역할을 포함합니다.
  - 관리자는 새 CRL(인증서 해지 목록)을 발급하여 인증서 그룹을 관리하고, 구성을 변경하며, 애플리케이션 인증서를 해지할 수 있습니다.
  - 쓰기 권한자, 승인자, 관리자 역할은 다른 사용자에게 할당하는 것이 가장 좋습니다. 추가 보안을 위해 승인자 또는 관리자 역할의 사용자에게는 인증서를 발급하거나 발급자 CA 인증서를 갱신할 수 있는 Key Vault의 키 서명 권한도 필요합니다.
  - 역할에는 마이크로서비스 관리 역할 외에도 다음이 포함되지만 이에 국한되지는 않습니다.
    - CA의 보안 사례 구현에 대한 관리 책임
    - 인증서의 생성, 해지 및 일시 중단 관리 
    - 암호화 키 수명 주기 관리(예: 발급자 CA 키 갱신)
    - CA를 운영하는 서비스의 설치, 구성 및 유지 관리
    - 일상적인 서비스 운영 
    - CA 및 데이터베이스 백업/복구

### <a name="other-role-assignments"></a>기타 역할 할당

또한 서비스를 실행하는 경우 다음 역할을 고려합니다.

- 외부 루트 인증 기관과의 인증서 조달 계약의 비즈니스 소유자(예: 소유자가 외부 CA에서 인증서를 구매하거나 외부 CA에 종속된 CA를 운영하는 경우)
- 인증 기관의 개발 및 유효성 검사
- 감사 레코드 검토
- CA를 지원하거나 물리적 및 클라우드 시설을 관리하는 데 도움이 주지만 CA 작업을 수행하는 데 직접적으로 신뢰할 수 없는 직원은 *권한 있는* 역할에 있습니다. 권한 있는 역할의 사용자가 수행할 수 있는 작업 세트도 문서화해야 합니다.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>분기별로 신뢰할 수 있고 권한 있는 역할의 구성원 자격 검토

적어도 분기별로 신뢰할 수 있고 권한 있는 역할의 구성원 자격을 검토합니다. 각 역할의 사용자(수동 프로세스용) 또는 서비스 ID(자동화된 프로세스용) 세트가 최소한으로 유지되는지 확인합니다.

### <a name="role-separation-between-certificate-requester-and-approver"></a>인증서 요청자와 승인자 간의 역할 분리

인증서 발급 프로세스는 인증서 요청자와 인증서 승인자 역할(사용자 또는 자동화된 시스템) 간의 역할 분리를 적용해야 합니다. 인증서 발급에는 인증서 요청자가 인증서를 얻을 수 있는 권한이 있는지 확인하는 인증서 승인자 역할 권한이 부여되어야 합니다. 인증서 승인자 역할이 있는 사용자는 공식적으로 권한 있는 사용자여야 합니다.

### <a name="restrict-assignment-of-privileged-roles"></a>권한 있는 역할의 할당 제한

관리자 및 승인자 그룹의 구성원 자격 권한 부여와 같은 권한 있는 역할은 제한된 권한 있는 직원 세트에 할당하도록 제한해야 합니다. 모든 권한 있는 역할 변경에는 24시간 내에 철회되는 액세스 권한이 있어야 합니다. 마지막으로 권한 있는 역할 할당을 분기별로 검토하고 필요하지 않거나 만료된 할당을 제거합니다.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>권한 있는 역할은 2단계 인증을 사용해야 함

서비스에 대한 승인자 및 관리자의 대화형 로그인에는 다단계 인증(2단계 인증이라고도 함)을 사용합니다.

## <a name="certificate-service-operation-guidelines"></a>인증서 서비스 운영 지침

### <a name="operational-contacts"></a>운영 연락처

인증서 서비스에는 자세한 운영 인시던트 대응 연락처가 포함된 최신 보안 대응 계획이 정리되어 있어야 합니다.

### <a name="security-updates"></a>보안 업데이트

모든 시스템을 지속적으로 모니터링하여 최신 보안 업데이트로 업데이트해야 합니다.

> [!IMPORTANT]
> OPC 자격 증명 모음 서비스의 GitHub 리포지토리는 보안 패치로 지속적으로 업데이트됩니다. 이러한 업데이트를 모니터링하여 정기적으로 서비스에 적용합니다.

### <a name="security-monitoring"></a>보안 모니터링

적절한 보안 모니터링을 구독하거나 구현합니다. 예를 들어 중앙 모니터링 솔루션(예: Azure Security Center 또는 Microsoft 365 모니터링 솔루션)을 구독하고, 보안 이벤트를 모니터링 솔루션에 보내도록 적절하게 구성합니다.

> [!IMPORTANT]
> OPC 자격 증명 모음 서비스는 기본적으로 [Azure Application Insights](../azure-monitor/app/devops.md)와 함께 모니터링 솔루션으로 배포됩니다. [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 같은 보안 솔루션을 추가하는 것도 매우 좋습니다.

### <a name="assess-the-security-of-open-source-software-components"></a>오픈 소스 소프트웨어 구성 요소의 보안 평가

제품 또는 서비스 내에서 사용되는 모든 오픈 소스 구성 요소에는 보통 이상의 보안 취약성이 없어야 합니다.

> [!IMPORTANT]
> 연속 통합을 빌드하는 동안 OPC 자격 증명 모음 서비스의 GitHub 리포지토리는 모든 구성 요소에 대한 취약성을 검사합니다. GitHub에서 이러한 업데이트를 모니터링하여 정기적으로 서비스에 적용합니다.

### <a name="maintain-an-inventory"></a>인벤토리 유지 관리

모든 프로덕션 호스트(영구 가상 머신 포함), 디바이스, 모든 내부 IP 주소 범위, VIP 및 퍼블릭 DNS 도메인 이름에 대한 자산 인벤토리를 유지 관리합니다. 시스템, 디바이스 IP 주소, VIP 또는 퍼블릭 DNS 도메인을 추가하거나 제거할 때마다 인벤토리를 30일 내에 업데이트해야 합니다.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>기본 Azure OPC 자격 증명 모음 마이크로서비스 프로덕션 배포의 인벤토리 

Azure에서:
- **App Service 요금제**: 서비스 호스트에 대한 App Service 요금제입니다. 기본값은 S1입니다.
- **App Service**(마이크로서비스용): OPC 자격 증명 모음 서비스 호스트입니다.
- **App Service**(샘플 애플리케이션용): OPC 자격 증명 모음 샘플 애플리케이션 호스트입니다.
- **Key Vault 표준**: 웹 서비스에 대한 비밀 및 Azure Cosmos DB 키를 저장합니다.
- **Key Vault 프리미엄**: 서비스에 서명하고 애플리케이션 프라이빗 키 자격 증명 모음을 구성하고 저장하기 위해 발급자 CA 키를 호스팅합니다.
- **Azure Cosmos DB**: 애플리케이션 및 인증서 요청에 대한 데이터베이스입니다. 
- **Application Insights**: (선택 사항) 웹 서비스 및 애플리케이션에 대한 모니터링 솔루션입니다.
- **Azure AD 애플리케이션 등록**: 샘플 애플리케이션, 서비스 및 에지 모듈에 대한 등록입니다.

클라우드 서비스의 경우 서비스를 배포하는 데 사용되는 모든 호스트 이름, 리소스 그룹, 리소스 이름, 구독 ID 및 테넌트 ID를 문서화해야 합니다. 

Azure IoT Edge 또는 로컬 IoT Edge 서버에서:
- **OPC 자격 증명 모음 IoT Edge 모듈**: 팩터리 네트워크 OPC UA Global Discovery Server를 지원합니다. 

IoT Edge 디바이스의 경우 호스트 이름과 IP 주소를 문서화해야 합니다. 

### <a name="document-the-certification-authorities-cas"></a>CA(인증 기관) 문서화

CA 계층 구조 설명서는 운영되는 모든 CA를 포함해야 합니다. 여기에는 서비스에서 관리하지 않는 경우에도 관련된 모든 하위 CA, 부모 CA 및 루트 CA가 포함됩니다. 공식 설명서 대신 만료되지 않은 모든 CA 인증서의 전체 세트를 제공할 수 있습니다.

> [!NOTE]
> OPC 자격 증명 모음 샘플 애플리케이션은 문서화를 위해 서비스에서 사용되고 생성된 모든 인증서를 다운로드할 수 있도록 지원합니다.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>모든 CA(인증 기관)에서 발급한 인증서 문서화

지난 12개월 동안 발급된 모든 인증서의 전체 세트를 제공합니다.

> [!NOTE]
> OPC 자격 증명 모음 샘플 애플리케이션은 문서화를 위해 서비스에서 사용되고 생성된 모든 인증서를 다운로드할 수 있도록 지원합니다.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>암호화 키를 안전하게 삭제하는 표준 운영 절차 문서화

CA의 수명 동안 키 삭제는 거의 발생하지 않을 수 있습니다. 이는 Key Vault 인증서 삭제 권한이 할당되는 사용자가 없는 이유와 발급자 CA 인증서를 삭제하기 위해 공개되는 API가 없는 이유입니다. 인증 기관 암호화 키를 안전하게 삭제하는 수동 표준 작동 절차는 Azure Portal에서 Key Vault에 직접 액세스해야만 사용할 수 있습니다. 또한 Key Vault에서 인증서 그룹을 삭제할 수 있습니다. 즉시 삭제하려면 [Key Vault 일시 삭제](../key-vault/general/soft-delete-overview.md) 기능을 사용하지 않도록 설정합니다.

## <a name="certificates"></a>인증서

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>인증서에서 최소 인증서 프로필을 준수해야 함

OPC 자격 증명 모음 서비스는 구독자에게 최종 엔터티 인증서를 발급하는 온라인 CA입니다. OPC 자격 증명 모음 마이크로서비스는 기본 구현에서 다음 지침을 따릅니다.

- 모든 인증서는 아래에서 지정한 대로 다음 X.509 필드를 포함해야 합니다.
  - 버전 필드의 내용은 v3이어야 합니다. 
  - serialNumber 필드의 내용은 FIPS(연방 정보 처리 표준) 140 승인된 난수 생성기에서 가져온 8바이트 이상의 엔트로피를 포함해야 합니다.<br>
    > [!IMPORTANT]
    > OPC 자격 증명 모음 일련 번호는 기본적으로 20바이트이며, 운영 체제 암호화 난수 생성기에서 가져옵니다. 난수 생성기는 Windows 디바이스에서 승인된 FIPS 140이지만, Linux에서는 승인되지 않습니다. 기본 OpenSSL에서 FIPS 140을 승인하지 않은 Linux VM 또는 Linux docker 컨테이너를 사용하는 서비스 배포를 선택하는 경우 이를 고려해야 합니다.
  - issuerUniqueID 및 subjectUniqueID 필드가 없어야 합니다.
  - 최종 엔터티 인증서는 IETF RFC 5280에 따라 기본 제약 조건 확장으로 식별해야 합니다.
  - 발급하는 CA 인증서에 대해 pathLenConstraint 필드를 0으로 설정해야 합니다. 
  - 확장 키 사용 확장이 있어야 하며, 확장 키 사용 OID(개체 식별자)의 최소 세트를 포함해야 합니다. anyExtendedKeyUsage OID(2.5.29.37.0)를 지정하지 않아야 합니다. 
  - CDP(CRL 배포 지점) 확장은 발급자 CA 인증서에 있어야 합니다.<br>
    > [!IMPORTANT]
    > CDP 확장은 OPC 자격 증명 모음 CA 인증서에 있습니다. 그럼에도 불구하고 OPC UA 디바이스는 사용자 지정 메서드를 사용하여 CRL을 배포합니다.
  - 기관 정보 액세스 확장은 구독자 인증서에 있어야 합니다.<br>
    > [!IMPORTANT]
    > 기관 정보 액세스 확장은 OPC 자격 증명 모음 구독자 인증서에 있습니다. 그럼에도 불구하고 OPC UA 디바이스는 사용자 지정 메서드를 사용하여 발급자 CA 정보를 배포합니다.
- 승인된 비대칭 알고리즘, 키 길이, 해시 함수 및 패딩 모드를 사용해야 합니다.
  - RSA와 SHA-2만 유일하게 지원되는 알고리즘입니다.
  - RSA는 암호화, 키 교환 및 서명에 사용할 수 있습니다.
  - RSA 암호화는 OAEP, RSA-KEM 또는 RSA-PSS 패딩 모드만 사용해야 합니다.
  - 2,048비트 이상의 키 길이가 필요합니다.
  - SHA-2 해시 알고리즘 제품군(SHA256, SHA384 및 SHA512)을 사용합니다.
  - 일반적인 수명이 20년 이상인 RSA 루트 CA 키는 4,096비트 이상이어야 합니다.
  - RSA 발급자 CA 키는 2,048비트 이상이어야 합니다. CA 인증서 만료 날짜가 2030년 이후인 경우 CA 키는 4,096비트 이상이어야 합니다.
- 인증서 수명
  - 루트 CA 인증서: 루트 CA의 최대 인증서 유효 기간은 25년을 초과하지 않아야 합니다.
  - 하위 CA 또는 온라인 발급자 CA 인증서: 온라인 상태이고 구독자 인증서만 발급하는 CA의 최대 인증서 유효 기간은 6년을 초과하지 않아야 합니다. 이러한 CA의 경우 관련 프라이빗 서명 키를 3년 이하로 사용한 후에만 새 인증서를 발급할 수 있습니다.<br>
    > [!IMPORTANT]
    > 발급자 인증서는 외부 루트 CA를 제외하고 기본 OPC 자격 증명 모음 마이크로서비스에서 생성되므로 각 요구 사항 및 수명이 있는 온라인 하위 CA처럼 처리됩니다. 기본 수명은 5년으로 설정되며, 키 길이가 2,048보다 크거나 같습니다.
  - 모든 비대칭 키의 수명은 최대 5년이고, 권장 수명은 1년이어야 합니다.<br>
    > [!IMPORTANT]
    > 기본적으로 OPC 자격 증명 모음을 사용하여 발급된 애플리케이션 인증서의 수명은 2년이며, 매년 교체해야 합니다. 
  - 인증서가 갱신될 때마다 새 키로 갱신됩니다.
- 애플리케이션 인스턴스 인증서의 OPC UA 관련 확장
  - subjectAltName 확장은 URI 및 호스트 이름을 포함합니다. 여기에는 FQDN, IPv4 및 IPv6 주소도 포함될 수 있습니다.
  - keyUsage는 digitalSignature, nonRepudiation, keyEncipherment 및 dataEncipherment를 포함합니다.
  - extendedKeyUsage은 serverAuth 및 clientAuth를 포함합니다.
  - authorityKeyIdentifier는 서명된 인증서에 지정합니다.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 키 및 인증서에서 최소 요구 사항을 충족해야 함

- **프라이빗 키**: RSA 키는 2,048비트 이상이어야 합니다. CA 인증서 만료 날짜가 2030년 이후인 경우 CA 키는 4,096비트 이상이어야 합니다.
- **수명**: 온라인 상태이고, 구독자 인증서만 발급하는 CA의 최대 인증서 유효 기간은 6년을 초과하지 않아야 합니다. 이러한 CA의 경우 관련 프라이빗 서명 키를 3년 이하로 사용한 후에만 새 인증서를 발급할 수 있습니다.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA 키는 하드웨어 보안 모듈을 사용하여 보호됨

OpcVault는 Azure Key Vault 프리미엄을 사용하며, 키는 FIPS 140-2 수준 2 HSM(하드웨어 보안 모듈)으로 보호됩니다. 

Key Vault에서 사용하는 암호화 모듈(HSM 또는 소프트웨어)에 대해 FIPS 유효성 검사가 수행됩니다. HSM 보호로 만들었거나 가져온 키는 FIPS 140-2 수준 2 유효성으로 검사된 HSM 내에서 처리됩니다. 소프트웨어 보호로 만들었거나 가져온 키는 FIPS 140-2 수준 1 유효성으로 검사된 암호화 모듈 내에서 처리됩니다.

## <a name="operational-practices"></a>운영 사례

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>인증서 등록에 대한 표준 운영 PKI 사례 문서화 및 유지 관리

다음을 포함하여 CA에서 인증서를 발급하는 방법에 대한 SOP(표준 운영 절차)를 문서화하고 유지 관리합니다. 
- 구독자를 식별하고 인증하는 방법 
- 인증서 요청을 처리하고 유효성을 검사하는 방법(해당하는 경우 인증서 갱신 및 키 다시 입력 요청을 처리하는 방법 포함) 
- 발급된 인증서를 구독자에게 배포하는 방법 

OPC 자격 증명 모음 마이크로서비스 SOP는 [OPC 자격 증명 모음 아키텍처](overview-opc-vault-architecture.md) 및 [OPC 자격 증명 모음 인증서 서비스 관리](howto-opc-vault-manage.md)에서 설명하고 있습니다. 이 사례는 "OPC 통합 아키텍처 사양 12부: 검색 및 글로벌 서비스"를 따릅니다.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>인증서 해지에 대한 표준 운영 PKI 사례 문서화 및 유지 관리

인증서 해지 프로세스는 [OPC 자격 증명 모음 아키텍처](overview-opc-vault-architecture.md) 및 [OPC 자격 증명 모음 인증서 서비스 관리](howto-opc-vault-manage.md)에서 설명하고 있습니다.
    
### <a name="document-ca-key-generation-ceremony"></a>CA 키 생성 공식 절차 문서화 

Azure Key Vault의 보안 스토리지로 인해 OPC 자격 증명 모음 마이크로서비스의 발급자 CA 키 생성이 간소화됩니다. 자세한 내용은 [OPC 자격 증명 모음 인증서 서비스 관리](howto-opc-vault-manage.md)를 참조하세요.

그러나 외부 루트 인증 기관을 사용하는 경우 CA 키 생성 공식 절차는 다음 요구 사항을 준수해야 합니다.

CA 키 생성 공식 절차는 최소한 다음 항목이 포함된 문서화된 스크립트에 대해 수행해야 합니다. 
- 역할 및 참가자의 책임 정의
- CA 키 생성 공식 절차의 수행에 대한 승인
- 공식 절차에 필요한 암호화 하드웨어 및 정품 인증 자료
- 하드웨어 준비(자산/구성 정보 업데이트 및 로그오프 포함)
- 운영 체제 설치
- CA 키 생성 공식 절차 중에 수행되는 특정 단계: 
  - CA 애플리케이션 설치 및 구성
  - CA 키 생성
  - CA 키 백업
  - CA 인증서 서명
  - 서비스의 보호된 HSM에서 서명된 키 가져오기
  - CA 시스템 종료
  - 스토리지 자료 준비


## <a name="next-steps"></a>다음 단계

이제 OPC 자격 증명 모음을 안전하게 관리하는 방법을 알아보았으므로 다음 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 자격 증명 모음을 사용하여 OPC UA 디바이스 보호](howto-opc-vault-secure.md)