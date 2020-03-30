---
title: OPC Vault 인증서 관리 서비스를 안전하게 실행하는 방법 - Azure | 마이크로 소프트 문서
description: Azure에서 OPC Vault 인증서 관리 서비스를 안전하게 실행하는 방법을 설명하고 고려해야 할 다른 보안 지침을 검토합니다.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271708"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>OPC Vault 인증서 관리 서비스를 안전하게 실행

이 문서에서는 Azure에서 OPC Vault 인증서 관리 서비스를 안전하게 실행하는 방법을 설명하고 고려해야 할 다른 보안 지침을 검토합니다.

## <a name="roles"></a>역할

### <a name="trusted-and-authorized-roles"></a>신뢰할 수 있고 승인된 역할

OPC Vault 마이크로 서비스를 사용하면 고유한 역할이 서비스의 다양한 부분에 액세스할 수 있습니다.

> [!IMPORTANT]
> 배포 하는 동안 스크립트는 모든 역할에 대 한 사용자로 배포 스크립트를 실행 하는 사용자만 추가 합니다. 프로덕션 배포의 경우 이 역할 할당을 검토하고 아래 지침에 따라 적절하게 다시 구성해야 합니다. 이 작업에는 Azure Active Directory(Azure AD) 엔터프라이즈 응용 프로그램 포털에서 역할 및 서비스를 수동으로 할당해야 합니다.

### <a name="certificate-management-service-roles"></a>인증서 관리 서비스 역할

OPC Vault 마이크로 서비스는 다음과 같은 역할을 정의합니다.

- **판독기**: 기본적으로 테넌트의 모든 인증된 사용자가 읽기 액세스 권한을 가수합니다. 
  - 응용 프로그램 및 인증서 요청에 대한 액세스를 읽습니다. 응용 프로그램 및 인증서 요청을 나열하고 쿼리할 수 있습니다. 또한 장치 검색 정보 및 공용 인증서는 읽기 액세스를 통해 액세스할 수 있습니다.
- **작성기**: 작성기 역할은 특정 작업에 대한 쓰기 권한을 추가하기 위해 사용자에게 할당됩니다. 
  - 응용 프로그램 및 인증서 요청에 대한 읽기/쓰기 액세스 응용 프로그램을 등록, 업데이트 및 등록 취소할 수 있습니다. 인증서 요청을 만들고 승인된 개인 키 및 인증서를 얻을 수 있습니다. 개인 키를 삭제할 수도 있습니다.
- **승인자**: 승인자 역할은 인증서 요청을 승인하거나 거부하도록 사용자에게 할당됩니다. 역할에는 다른 역할이 포함되지 않습니다.
  - OPC Vault 마이크로 서비스 API에 액세스하는 승인자 역할 외에도 사용자는 인증서에 서명할 수 있도록 Azure Key Vault에서 키 서명 권한이 있어야 합니다.
  - 작성기 및 승인자 역할은 다른 사용자에게 할당되어야 합니다.
  - 승인자의 주요 역할은 인증서 요청의 생성 및 거부승인입니다.
- **관리자**: 관리자 역할이 사용자에게 할당되어 인증서 그룹을 관리합니다. 역할은 승인자 역할을 지원하지 않지만 작성자 역할을 포함합니다.
  - 관리자는 새 CRL(인증서 해지 목록)을 발급하여 인증서 그룹을 관리하고 구성을 변경하고 응용 프로그램 인증서를 해지할 수 있습니다.
  - 이상적으로 는 작성기, 승인자 및 관리자 역할이 다른 사용자에게 할당됩니다. 추가 보안을 위해 승인자 또는 관리자 역할을 가진 사용자는 Key Vault에서 키 서명 권한을 부여하여 인증서를 발급하거나 발급자 CA 인증서를 갱신해야 합니다.
  - 마이크로 서비스 관리 역할 외에도 역할에는 다음이 포함되나 이에 국한되지 않습니다.
    - CA의 보안 관행 구현관리 에 대한 책임입니다.
    - 인증서의 생성, 해지 및 정지 관리. 
    - 암호화 키 수명 주기 관리(예: 발급자 CA 키 갱신).
    - CA를 운영하는 서비스의 설치, 구성 및 유지 관리.
    - 서비스의 일상적인 운영. 
    - CA 및 데이터베이스 백업 및 복구.

### <a name="other-role-assignments"></a>기타 역할 할당

또한 서비스를 실행할 때 다음 역할을 고려하십시오.

- 외부 루트 인증 기관과 인증서 조달 계약의 비즈니스 소유자(예: 소유자가 외부 CA에서 인증서를 구입하거나 외부 CA에 종속된 CA를 운영하는 경우).
- 인증 기관의 개발 및 유효성 검사.
- 감사 기록 검토.
- CA를 지원하거나 물리적 및 클라우드 시설을 관리하는 데 도움이 되지만 CA 작업을 수행하기 위해 직접 신뢰할 수 없는 직원은 *권한이 부여된* 역할에 있습니다. 권한이 부여된 역할의 담당자가 수행할 수 있는 작업 집합도 문서화되어야 합니다.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>분기별로 신뢰할 수 있고 승인된 역할의 멤버십 검토

적어도 분기별로 신뢰할 수 있고 승인된 역할의 구성원 자격을 검토합니다. 각 역할의 사용자 집합(수동 프로세스의 경우) 또는 서비스 ID(자동화된 프로세스의 경우)가 최소한으로 유지되도록 합니다.

### <a name="role-separation-between-certificate-requester-and-approver"></a>인증서 요청자와 승인자 간의 역할 구분

인증서 발급 프로세스는 인증서 요청자와 인증서 승인자 역할(사람 또는 자동화된 시스템) 간의 역할 분리를 적용해야 합니다. 인증서 발급은 인증서 요청자가 인증서를 획득할 권한이 있는지 확인하는 인증서 승인자 역할에 의해 승인되어야 합니다. 인증서 승인자 역할을 보유한 사람은 공식적으로 승인된 사람이어야 합니다.

### <a name="restrict-assignment-of-privileged-roles"></a>권한 있는 역할 할당 제한

관리자 및 승인자 그룹의 구성원 자격 권한을 부여하는 것과 같은 권한 있는 역할할당을 제한된 권한 있는 직원 집합으로 제한해야 합니다. 권한 있는 역할 변경 사항은 24시간 이내에 액세스가 취소되어야 합니다. 마지막으로 분기별로 권한 있는 역할 할당을 검토하고 불필요하거나 만료된 할당을 제거합니다.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>권한 있는 역할은 이중 인증을 사용해야 합니다.

서비스에 대한 승인자 및 관리자의 대화형 로그인에 다단계 인증(2단계 인증이라고도 함)을 사용합니다.

## <a name="certificate-service-operation-guidelines"></a>인증서 서비스 운영 지침

### <a name="operational-contacts"></a>운영 연락처

인증서 서비스에는 자세한 운영 인시던트 대응 연락처가 포함된 최신 보안 대응 계획이 있어야 합니다.

### <a name="security-updates"></a>보안 업데이트

모든 시스템은 최신 보안 업데이트로 지속적으로 모니터링하고 업데이트해야 합니다.

> [!IMPORTANT]
> OPC Vault 서비스의 GitHub 리포지토리는 보안 패치로 지속적으로 업데이트됩니다. 이러한 업데이트를 모니터링하고 정기적으로 서비스에 적용합니다.

### <a name="security-monitoring"></a>보안 모니터링

적절한 보안 모니터링을 구독하거나 구현합니다. 예를 들어 중앙 모니터링 솔루션(예: Azure Security Center 또는 Office 365 모니터링 솔루션)을 구독하고 보안 이벤트가 모니터링 솔루션으로 전송되도록 적절하게 구성합니다.

> [!IMPORTANT]
> 기본적으로 OPC Vault 서비스는 Azure [응용 프로그램 인사이트를](https://docs.microsoft.com/azure/azure-monitor/app/devops) 모니터링 솔루션으로 배포합니다. [Azure 보안 센터와](https://azure.microsoft.com/services/security-center/) 같은 보안 솔루션을 추가하는 것이 좋습니다.

### <a name="assess-the-security-of-open-source-software-components"></a>오픈 소스 소프트웨어 구성 요소의 보안 평가

제품 또는 서비스 내에서 사용되는 모든 오픈 소스 구성 요소에는 보통 또는 더 큰 보안 취약점이 없어야 합니다.

> [!IMPORTANT]
> 지속적인 통합 빌드 중에 OPC Vault 서비스의 GitHub 리포지토리는 모든 구성 요소에 취약점을 검사합니다. GitHub에서 이러한 업데이트를 모니터링하고 정기적으로 서비스에 적용합니다.

### <a name="maintain-an-inventory"></a>인벤토리 유지 관리

모든 프로덕션 호스트(영구 가상 시스템 포함), 장치, 모든 내부 IP 주소 범위, VIP 및 공용 DNS 도메인 이름에 대한 자산 인벤토리를 유지 관리합니다. 시스템, 장치 IP 주소, VIP 또는 공용 DNS 도메인을 추가하거나 제거할 때마다 30일 이내에 인벤토리를 업데이트해야 합니다.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>기본 Azure OPC Vault 마이크로 서비스 프로덕션 배포의 인벤토리 

Azure에서:
- **앱 서비스 계획**: 서비스 호스트를 위한 앱 서비스 계획입니다. 기본 S1.
- 마이크로 서비스에 대한 **앱 서비스:** OPC Vault 서비스 호스트입니다.
- 샘플 응용 프로그램에 대한 **앱 서비스:** OPC Vault 샘플 응용 프로그램 호스트입니다.
- **키 볼트 표준**: 웹 서비스에 대한 비밀 및 Azure 코스모스 DB 키를 저장합니다.
- **키 볼트 프리미엄**: 발급자 CA 키를 호스팅하고, 서비스에 서명하고, 응용 프로그램 개인 키의 볼트 구성 및 저장을 위해.
- **Azure Cosmos DB**: 응용 프로그램 및 인증서 요청에 대한 데이터베이스입니다. 
- **애플리케이션 인사이트**: 웹 서비스 및 애플리케이션을 위한 (선택 사항) 모니터링 솔루션입니다.
- **Azure AD 응용 프로그램 등록**: 샘플 응용 프로그램, 서비스 및 에지 모듈에 대한 등록입니다.

클라우드 서비스의 경우 서비스를 배포하는 데 사용되는 모든 호스트 이름, 리소스 그룹, 리소스 이름, 구독 ID 및 테넌트 ID를 문서화해야 합니다. 

Azure IoT 에지 또는 로컬 IoT 에지 서버에서:
- **OPC 볼트 IoT 에지 모듈**: 공장 네트워크 OPC UA 글로벌 검색 서버를 지원합니다. 

IoT Edge 장치의 경우 호스트 이름과 IP 주소를 문서화해야 합니다. 

### <a name="document-the-certification-authorities-cas"></a>인증 기관(CA) 문서화

CA 계층 구조 문서에는 모든 작동CA가 포함되어야 합니다. 여기에는 서비스에서 관리되지 않는 경우에도 관련된 모든 하위 CA, 상위 CA 및 루트 CA가 포함됩니다. 공식 문서 대신 만료되지 않은 모든 CA 인증서의 전체 집합을 제공할 수 있습니다.

> [!NOTE]
> OPC Vault 샘플 응용 프로그램은 문서화를 위해 서비스에서 사용되고 생성된 모든 인증서의 다운로드를 지원합니다.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>모든 인증 기관(CA)에서 발급된 인증서를 문서화합니다.

지난 12개월 동안 발급된 모든 인증서의 전체 집합을 제공합니다.

> [!NOTE]
> OPC Vault 샘플 응용 프로그램은 문서화를 위해 서비스에서 사용되고 생성된 모든 인증서의 다운로드를 지원합니다.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>암호화 키를 안전하게 삭제하기 위한 표준 운영 절차 문서화

CA의 수명 동안 키 삭제는 거의 발생하지 않을 수 있습니다. 따라서 키 볼트 인증서 삭제 권한이 할당된 사용자가 없으며 발급자 CA 인증서를 삭제하는 데 노출된 API가 없는 이유가 됩니다. 인증 기관 암호화 키를 안전하게 삭제하기 위한 수동 표준 운영 절차는 Azure 포털의 Key Vault에 직접 액세스해야만 사용할 수 있습니다. 키 볼트에서 인증서 그룹을 삭제할 수도 있습니다. 즉시 삭제하려면 키 볼트 [소프트 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 기능을 사용하지 않도록 설정합니다.

## <a name="certificates"></a>인증서

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>인증서는 최소 인증서 프로필을 준수해야 합니다.

OPC Vault 서비스는 최종 엔터티 인증서를 구독자에게 발급하는 온라인 CA입니다. OPC Vault 마이크로 서비스는 기본 구현에서 이러한 지침을 따릅니다.

- 모든 인증서에는 아래에 명시된 대로 다음 X.509 필드가 포함되어야 합니다.
  - 버전 필드의 내용은 v3이어야 합니다. 
  - serialNumber 필드의 내용은 FIPS(연방 정보 처리 표준) 140 승인된 난수 생성기에서 얻은 엔트로피의 8바이트 이상을 포함해야 합니다.<br>
    > [!IMPORTANT]
    > OPC Vault 일련 번호는 기본적으로 20바이트이며 운영 체제 암호화 난수 생성기에서 가져옵니다. 난수 생성기는 Windows 장치에서 승인된 FIPS 140이지만 Linux에서는 승인되지 않습니다. 기본 기술 OpenSSL이 FIPS 140이 승인되지 않은 Linux VM 또는 Linux 도커 컨테이너를 사용하는 서비스 배포를 선택할 때 이 점을 고려하십시오.
  - 발행자고유ID 및 제목UniqueID 필드가 없어야 합니다.
  - 최종 엔터티 인증서는 IETF RFC 5280에 따라 기본 제약 조건 확장으로 식별되어야 합니다.
  - 발급 CA 인증서에 대해 pathLen제약 필드를 0으로 설정해야 합니다. 
  - 확장 키 사용 확장이 있어야 하며 확장 키 사용 개체 식별자(DC)의 최소 집합을 포함해야 합니다. 확장키 사용 OID(2.5.29.37.0)는 지정할 수 없습니다. 
  - CRL 배포 지점(CDP) 확장은 발급자 CA 인증서에 있어야 합니다.<br>
    > [!IMPORTANT]
    > CDP 확장은 OPC Vault CA 인증서에 있습니다. 그럼에도 불구하고 OPC UA 장치는 사용자 지정 메서드를 사용하여 CRL을 배포합니다.
  - 기관 정보 액세스 확장은 구독자 인증서에 있어야 합니다.<br>
    > [!IMPORTANT]
    > 기관 정보 액세스 확장은 OPC Vault 가입자 인증서에 있습니다. 그럼에도 불구하고 OPC UA 장치는 사용자 지정 방법을 사용하여 발급자 CA 정보를 배포합니다.
- 승인된 비대칭 알고리즘, 키 길이, 해시 함수 및 패딩 모드를 사용해야 합니다.
  - RSA 및 SHA-2는 지원되는 유일한 알고리즘입니다.
  - RSA는 암호화, 키 교환 및 서명에 사용할 수 있습니다.
  - RSA 암호화는 OAEP, RSA-KEM 또는 RSA-PSS 패딩 모드만 사용해야 합니다.
  - 2048비트보다 크거나 같은 키 길이가 필요합니다.
  - SHA-2 해시 알고리즘(SHA256, SHA384 및 SHA512)을 사용합니다.
  - 일반적인 수명이 20년보다 크거나 같을 RSA 루트 CA 키는 4096 비트 이상이어야 합니다.
  - RSA 발급자 CA 키는 2048비트 이상이어야 합니다. CA 인증서 만료 날짜가 2030년 이후인 경우 CA 키는 4096비트 이상이어야 합니다.
- 인증서 수명
  - 루트 CA 인증서: 루트 CA의 최대 인증서 유효 기간은 25년을 초과할 수 없습니다.
  - 하위 CA 또는 온라인 발급자 CA 인증서: 온라인 및 구독자 인증서만 발급하는 CA의 최대 인증서 유효 기간은 6년을 초과해서는 안 됩니다. 이러한 카의 경우 관련 개인 서명 키를 새 인증서를 발급하는 데 3년 이상 사용해서는 안 됩니다.<br>
    > [!IMPORTANT]
    > 발급자 인증서는 외부 루트 CA가 없는 기본 OPC Vault 마이크로 서비스에서 생성되기 때문에 각 요구 사항 및 수명이 있는 온라인 하위 CA처럼 처리됩니다. 기본 수명은 5년으로 설정되어 있으며 키 길이는 2048보다 크거나 같습니다.
  - 모든 비대칭 키는 최대 5년의 수명과 권장 1년의 수명을 가져야 합니다.<br>
    > [!IMPORTANT]
    > 기본적으로 OPC Vault에서 발급된 응용 프로그램 인증서의 수명은 2년이며 매년 교체해야 합니다. 
  - 인증서가 갱신될 때마다 새 키로 갱신됩니다.
- 응용 프로그램 인스턴스 인증서의 OPC UA 별 확장
  - subjectAltName 확장 응용 프로그램 Uri 및 호스트 이름을 포함 합니다. 여기에는 FQDN, IPv4 및 IPv6 주소도 포함될 수 있습니다.
  - 키사용에는 디지털 서명, 비리, 키엔시퍼레이션 및 데이터 Encipherment가 포함됩니다.
  - extendedKey사용에는 서버Auth 및 clientAuth가 포함됩니다.
  - 기관KeyIdentifier는 서명된 인증서에 지정됩니다.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 키와 인증서는 최소 요구 사항을 충족해야 합니다.

- **개인 키**: RSA 키는 2048 비트 이상이어야합니다. CA 인증서 만료 날짜가 2030년 이후인 경우 CA 키는 4096비트 이상이어야 합니다.
- **수명:** 온라인 및 구독자 인증서만 발급하는 CO의 최대 인증서 유효 기간은 6년을 초과해서는 안 됩니다. 이러한 카의 경우 관련 개인 서명 키를 새 인증서를 발급하는 데 3년 이상 사용해서는 안 됩니다.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA 키는 하드웨어 보안 모듈을 사용하여 보호됩니다.

OpcVault는 Azure 키 볼트 프리미엄을 사용하며 키는 FIPS 140-2 레벨 2 하드웨어 보안 모듈(HSM)으로 보호됩니다. 

KEY Vault가 사용하는 암호화 모듈은 HSM이든 소프트웨어이든 관계없이 FIPS의 유효성을 검사합니다. HSM 보호로 생성되거나 가져온 키는 HSM 내부에서 처리되며 FIPS 140-2 수준 2로 검증됩니다. 소프트웨어 보호로 생성되거나 가져온 키는 FIPS 140-2 수준 1로 검증된 암호화 모듈 내에서 처리됩니다.

## <a name="operational-practices"></a>운영 관행

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>인증서 등록을 위한 표준 운영 PKI 관행 문서화 및 유지 관리

다음과 같은 인증서를 발급하는 방법에 대한 표준 운영 절차(SSO)를 문서화하고 유지 관리합니다. 
- 구독자를 식별하고 인증하는 방법 
- 인증서 요청이 처리되고 유효성을 검사하는 방법(해당하는 경우 인증서 갱신 및 리키 요청이 처리되는 방법도 포함). 
- 발급된 인증서가 구독자에게 배포되는 방식입니다. 

OPC Vault 마이크로 서비스 SOP는 [OPC 볼트 아키텍처](overview-opc-vault-architecture.md) 및 [OPC 볼트 인증서 서비스 관리에](howto-opc-vault-manage.md)설명되어 있습니다. "OPC 통합 아키텍처 사양 12부: 검색 및 글로벌 서비스"를 따릅니다.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>인증서 해지에 대한 표준 운영 PKI 관행을 문서화하고 유지 관리

인증서 취소 프로세스는 [OPC Vault 아키텍처](overview-opc-vault-architecture.md) 및 [OPC Vault 인증서 서비스 관리에](howto-opc-vault-manage.md)설명되어 있습니다.
    
### <a name="document-ca-key-generation-ceremony"></a>문서 CA 키 생성 행사 

Azure 키 볼트의 보안 저장소로 인해 OPC Vault 마이크로 서비스의 발급자 CA 키 생성이 간소화됩니다. 자세한 내용은 [OPC Vault 인증서 서비스 관리를](howto-opc-vault-manage.md)참조하십시오.

그러나 외부 루트 인증 기관을 사용하는 경우 CA 키 생성 행사는 다음 요구 사항을 준수해야 합니다.

CA 키 생성 행사는 최소한 다음 항목이 포함된 문서화된 스크립트에 대해 수행되어야 합니다. 
- 역할 및 참가자 책임의 정의.
- CA 키 생성 행사 의 실시 승인.
- 의식에 필요한 암호화 하드웨어 및 활성화 자료.
- 하드웨어 준비(자산/구성 정보 업데이트 및 사인오프 포함)
- 운영 체제 설치.
- CA 키 생성 행사 중에 수행되는 특정 단계는 다음과 같이 수행됩니다. 
  - CA 응용 프로그램 설치 및 구성.
  - CA 키 생성.
  - CA 키 백업.
  - CA 인증서 서명.
  - 서비스의 보호된 HSM에서 서명된 키를 가져옵니다.
  - CA 시스템 종료.
  - 저장을위한 재료의 준비.


## <a name="next-steps"></a>다음 단계

OPC Vault를 안전하게 관리하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 볼트를 갖춘 안전한 OPC UA 장치](howto-opc-vault-secure.md)
