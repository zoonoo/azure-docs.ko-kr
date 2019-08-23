---
title: Azure 오스트레일리아의 Azure Key Vault
description: 오스트레일리아 지역 내에서 키 관리를 위해 Azure Key Vault를 구성 하 고 사용 하는 방법에 대 한 지침을 통해 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족할 수 있습니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602120"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure 오스트레일리아의 Azure Key Vault

암호화 키 수명 주기 관리와 암호화 키의 안전한 저장소는 암호화 시스템 내의 중요 한 요소입니다.  Azure에서이 기능을 제공 하는 서비스는 Azure Key Vault입니다. Key Vault는 IRAP 보안에 액세스 하 여 보호 된 ACSC에 대해 인증을 받았습니다.  이 문서에서는 Key Vault를 사용 하 여 오스트레일리아 신호(ASD) 를 사용 하는 경우의 [주요 고려 사항](https://acsc.gov.au/infosec/ism/)(ISM)에 대해 설명 합니다.

Azure Key Vault는 암호화 키 및 비밀을 보호 하는 클라우드 서비스입니다. 이 데이터는 중요 하 고 업무상 중요 하기 때문에 Key Vault 키 자격 증명 모음에 안전 하 게 액세스할 수 있으므로 권한 있는 사용자 및 응용 프로그램만 허용 합니다. Key Vault에서 관리 및 제어 하는 세 가지 주요 아티팩트가 있습니다.

- 키
- 비밀
- 인증서

이 문서에서는 Key Vault를 사용 하 여 키를 관리 하는 방법을 집중적으로 설명 합니다.

![Azure Key Vault](media/azure-key-vault-overview.png)

*다이어그램 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>주요 디자인 고려 사항

### <a name="deployment-options"></a>배포 옵션

Azure 키 자격 증명 모음을 만들기 위한 두 가지 옵션이 있습니다. 두 옵션 모두 Thales nShield 제품군 HSM (하드웨어 보안 모듈)을 사용 하 고, FIPS (연방 정보 처리 표준)의 유효성을 검사 하 고, 보호 된 환경에 키를 저장 하도록 승인 됩니다. 다음 옵션을 사용할 수 있습니다.

- **소프트웨어 보호 된 자격 증명 모음:** FIPS 140-2 수준 1의 유효성을 검사 했습니다. HSM에 저장 된 키입니다. 암호화 및 암호 해독 작업은 Azure에서 계산 리소스에서 수행 됩니다.
- **HSM으로 보호 되는 자격 증명 모음:** FIPS 140-2 수준 2의 유효성을 검사 했습니다. HSM에 저장 된 키입니다. 암호화 및 암호 해독 작업은 HSM에서 수행 됩니다.

Key Vault는 RSA (Rivest Rivest-shamir-adleman Rivest-shamir-adleman) 및 ECC (타원 Curve Cryptography) 키를 지원 합니다. 기본값은 RSA 2048 비트 키 이지만 RSA 3072 비트, RSA 4096 비트 및 ECC 키에 대 한 고급 옵션이 있습니다.  모든 키는 ISM 컨트롤을 충족 하지만 타원 곡선 키가 기본 설정 되어 있습니다.

### <a name="resource-operations"></a>리소스 작업

Azure Key Vault에는 여러 개의 가상 사용자가 있습니다.

- **Key Vault 관리자:** 자격 증명 모음의 수명 주기를 관리 합니다.
- **키 관리자:** 자격 증명 모음에서 키의 수명 주기를 관리 합니다.
- **개발자/운영자:** 자격 증명 모음에서 응용 프로그램 및 서비스에 키를 통합 합니다.
- **감사자** 키 사용 및 액세스 모니터링
- **애플리케이션:** 키를 사용 하 여 정보 보호

Azure Key Vault는 두 가지 별도의 인터페이스를 사용 하 여 보호 됩니다.

- **관리 평면:** 이 비행기는 자격 증명 모음 관리를 처리 하 고 RBAC를 통해 보안이 유지 됩니다.
- **데이터 평면:** 이 평면은 자격 증명 모음에서 아티팩트를 관리 하 고 액세스 하는 방법을 다룹니다.  Key Vault 액세스 정책을 사용 하 여 보호 됩니다.

ISM에서 요구 하는 대로 적절 한 인증 및 권한 부여는 호출자 (사용자 또는 응용 프로그램) 전에 두 평면에서 키 자격 증명 모음에 대 한 액세스 권한을 얻기 전에 필요 합니다.

Azure RBAC에는 키 자격 증명 모음 관리를 제어 하는 Key Vault [Key Vault 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor)를 위한 기본 제공 역할이 하나 있습니다. 키 자격 증명 모음을 관리 하기 위해 보다 세분화 된 역할에 맞춘 사용자 지정 역할을 만드는 것이 좋습니다.

>[!WARNING]
>Key Vault 액세스 정책을 통해 키에 대 한 액세스를 사용 하도록 설정 하면 사용자 또는 응용 프로그램이 키 자격 증명 모음의 모든 키에 액세스할 수 있습니다. 예를 들어 사용자에 게 ' 삭제 ' 액세스 권한이 있는 경우 모든 키를 삭제할 수 있습니다.  따라서 보안 도메인/경계에 맞게 여러 키 자격 증명 모음을 배포 해야 합니다.

### <a name="networking"></a>네트워킹

데이터 평면에 대 한 액세스를 제어 하도록 Key Vault 방화벽과 가상 네트워크를 구성할 수 있습니다.  다른 모든 네트워크의 사용자 또는 응용 프로그램에 대 한 액세스를 거부 하면서 지정 된 네트워크의 사용자 또는 응용 프로그램에 대 한 액세스를 허용할 수 있습니다. "신뢰할 수 있는 서비스 허용"을 사용 하는 경우 [신뢰할 수 있는 서비스](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) 는이 컨트롤의 예외입니다.  관리 평면에는 가상 네트워킹 컨트롤이 적용 되지 않습니다.

키 자격 증명 모음에 대 한 액세스는 키에 대 한 액세스를 필요로 하는 사용자 또는 응용 프로그램이 있는 최소 네트워크 집합으로 명시적으로 제한 되어야 합니다.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault는 BYOK를 지원 합니다.  BYOK를 사용 하면 사용자가 기존 키 인프라에서 키를 가져올 수 있습니다.  Thales는 외부 HSM (예: 오프 라인 워크스테이션에서 생성 된 키)에서 키의 보안 전송 및 가져오기를 지 원하는 [오스트레일리아 도구 집합](https://www.microsoft.com/download/details.aspx?id=45345) 을 Key Vault로 제공 합니다.

### <a name="key-vault-auditing-and-logging"></a>Key Vault 감사 및 로깅

ACSC를 사용 하려면 적절 한 Azure 서비스를 사용 하 여 Azure 워크 로드에 대 한 실시간 모니터링 및 보고를 수행 해야 합니다.

로깅은 키 값에 대해 **_"Auditevent"_** 진단 설정을 사용 하도록 설정 하 여 사용 하도록 설정 됩니다.  감사 이벤트는 지정 된 저장소 계정에 기록 됩니다.  데이터 보존 정책에 따라 "보존 기간 **_"_** 기간을 설정 해야 합니다.  관리 평면과 데이터 평면에 대 한 [작업](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) 을 감사 하 고 기록 합니다. [Azure Monitor의 Azure Key Vault 솔루션](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) 을 사용 하 여 Key Vault auditevent 로그를 검토할 수 있습니다.  여러 다른 Azure 서비스를 사용 하 여 Key Vault AuditEvents를 처리 하 고 배포할 수 있습니다.

### <a name="key-rotation"></a>키 회전

Key Vault에 키를 저장 하면 응용 프로그램의 동작에 영향을 주지 않고 키를 업데이트할 수 있는 응용 프로그램 외부의 키를 유지 관리할 수 있는 단일 지점이 제공 됩니다. Azure Key Vault에 키를 저장 하면 키 회전을 지 원하는 다양 한 전략을 사용할 수 있습니다.

- 수동으로
- Api를 통해 프로그래밍 방식으로
- 자동화 스크립트 (예: PowerShell 및 Azure Automation 사용)

이러한 옵션을 사용 하면 키가 손상 되었을 수 있는 문제가 있는 경우 규정 준수 요구 사항을 충족 하거나 임시 기반으로 키를 주기적으로 회전할 수 있습니다.

#### <a name="key-rotation-strategies"></a>키 회전 전략

KeyVault에 저장 된 키에 대 한 적절 한 키 회전 전략을 개발 하는 것이 중요 합니다.  잘못 된 키를 사용 하면 정보가 잘못 해독 되 고 키가 손실 되 면 정보에 대 한 액세스가 완전히 손실 될 수 있습니다.  다양 한 시나리오에 대 한 주요 회전 전략의 예는 다음과 같습니다.

- **Inflight 데이터:** volatile 정보는 두 당사자 간에 전송 됩니다.  키를 회전 하는 경우 두 당사자 모두 키 자격 증명 모음에서 업데이트 된 키를 동기식으로 검색 하는 메커니즘이 있어야 합니다.
- **Rest 인 데이터:** 파티는 암호화 된 데이터를 저장 하 고 나중에 사용할 수 있도록 암호를 해독 합니다.  키를 회전 하는 경우 이전 키를 사용 하 여 데이터의 암호를 해독 한 다음 새 회전 된 키로 암호화 해야 합니다.  키 암호화 키를 사용 하 여 암호 해독/암호화 프로세스의 영향을 최소화 하는 방법이 있습니다 (예제 참조).  Microsoft는 Azure Storage의 키 회전과 관련 된 대부분의 프로세스를 관리 합니다 (... 참조).
- **액세스 키:** 다양 한 Azure 서비스에는 Key Vault (예: CosmosDB)에 저장할 수 있는 액세스 키가 있습니다.  Azure 서비스에는 기본 및 보조 액세스 키가 있습니다.  두 키가 동시에 회전 되지 않는 것이 중요 합니다.  따라서 한 키를 순환 하 고 키 작업을 확인 한 다음 두 번째 키를 회전할 수 있습니다.

### <a name="high-availability"></a>고가용성

ISM에는 비즈니스 연속성에 관련 된 여러 컨트롤이 있습니다.
Azure Key Vault는 지역 내에서 복제 된 콘텐츠와 보조 쌍으로 연결 된 [지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에 여러 중복 계층이 있습니다.

키 자격 증명 모음이 장애 조치 (failover) 상태인 경우에는 읽기 전용 모드 이며 기본 서비스가 복원 되는 읽기-쓰기 모드로 돌아갑니다.

ISM에는 백업과 관련 된 몇 가지 컨트롤이 있습니다.  자격 증명 모음 및 해당 키에 대 한 적절 한 백업/복원 계획을 개발 하 고 실행 하는 것이 중요 합니다.

## <a name="key-lifecycle"></a>주요 수명 주기

### <a name="key-operations"></a>키 작업

키에 대 한 다음 작업은 Key Vault 지원 됩니다.

- **만드십시오** 클라이언트가 Key Vault에 키를 만들 수 있습니다. 키 값은 Key Vault에서 생성되고 저장되며 클라이언트에 릴리스되지 않습니다. Key Vault에 비대칭 키를 만들 수 있습니다.
- **마법사** 클라이언트가 기존 키를 Key Vault로 가져올 수 있습니다. JWK 구조 내에서 다양한 패키징 메서드를 사용하여 Key Vault로 비대칭 키를 가져올 수 있습니다.
- **고침** 충분한 권한이 있는 클라이언트는 이전에 Key Vault에 저장된 키와 연결된 메타데이터(키 특성)를 수정할 수 있습니다.
- **제거** 충분한 권한이 있는 클라이언트는 Key Vault에서 키를 삭제할 수 있습니다.
- **은** 클라이언트가 지정된 Key Vault의 모든 키를 나열할 수 있습니다.
- **버전 나열:** 클라이언트가 지정된 Key Vault의 지정된 키 버전을 모두 나열할 수 있습니다.
- **가져오기:** 클라이언트가 Key Vault에서 지정된 키의 공개 부분을 검색할 수 있습니다.
- **백** 키를 보호된 형식으로 내보냅니다.
- **복원** 이전에 백업한 키를 가져옵니다.

키 작업을 실행할 수 있도록 Key Vault 액세스 제어 항목을 사용 하 여 사용자, 서비스 주체 또는 응용 프로그램에 부여할 수 있는 해당 권한 집합이 있습니다.

Key Vault에는 삭제 된 자격 증명 모음 및 키의 복구를 허용 하는 일시 삭제 기능이 있습니다. 기본적으로 **_"일시 삭제"_** 는 사용 하도록 설정 되어 있지 않지만 일단 사용 하도록 설정 되 면 개체는 삭제 된 것으로 표시 되는 동안 90 일 (보존 기간) 동안 유지 됩니다.  **_"보호 제거"_** 옵션을 사용 하지 않도록 설정한 경우 추가 권한 **_"제거"_** 를 통해 키를 영구적으로 삭제할 수 있습니다.

기존 키를 만들거나 가져오면 키의 새 버전이 만들어집니다.

### <a name="cryptographic-operations"></a>암호화 작업

키를 사용 하는 암호화 작업도 지원 Key Vault

- **서명 및 확인:** 이 작업은 "해시 서명" 또는 "해시 확인"입니다. Key Vault는 서명 만들기의 일부로 콘텐츠 해시를 지원 하지 않습니다.
- **키 암호화/래핑:** 이 작업은 다른 키를 보호 하는 데 사용 됩니다.
- **암호화 및 암호 해독:** 저장 된 키를 사용 하 여 단일 데이터 블록을 암호화 하거나 암호를 해독 합니다.

암호화 작업을 실행할 수 있도록 Key Vault 액세스 제어 항목을 사용 하 여 사용자, 서비스 주체 또는 응용 프로그램에 부여할 수 있는 해당 권한 집합이 있습니다.

키를 설정 하 고 암호화 작업을 사용할 수 있는지 여부를 제어 하기 위해 설정할 수 있는 세 가지 주요 특성은 다음과 같습니다.

- **enabled**
- **nbf:** 지정 된 날짜 이전에 사용 하도록 설정 되지 않음
- **exp:** 만료 날짜

## <a name="storage-and-keys"></a>저장소 및 키

고객 관리 키는 유연성을 향상 하 고 제어 될 키를 평가 하 고 관리 하는 데 사용할 수 있습니다. 또한 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수 있습니다.
저장소 및 키에는 Key Vault에 저장 되는 세 가지 측면이 있습니다.

- Key Vault 관리 저장소 계정 키
- 휴지 상태의 데이터에 대 한 SSE (Azure Storage 서비스 암호화)
- 관리 디스크 및 Azure Disk Encryption

Key Vault의 Azure Storage 계정 키 관리는 저장소 계정 키의 동기화 및 다시 생성 (순환)을 지 원하는 Key Vault의 핵심 서비스에 대 한 확장입니다.  [Azure Active Directory와 Azure Storage 통합](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (미리 보기)는 뛰어난 보안과 사용 편의성을 제공 하므로 릴리스되는 경우에 권장 됩니다.
SSE는 미사용 데이터의 암호화를 관리 하는 데 두 가지 키를 사용 합니다.

- 키 암호화 키 (KEK)
- DEK (데이터 암호화 키)

Microsoft에서 DEKs를 관리 하는 동안 SSE에는 Key Vault에 저장할 수 있는 고객이 관리 하는 KEKs를 사용 하는 옵션이 있습니다. 그러면 적절 한 규정 준수 정책에 따라 Azure Key Vault에서 키를 회전할 수 있습니다. 키를 회전 하는 경우 Azure Storage 해당 저장소 계정에 대 한 계정 암호화 키를 다시 암호화 합니다. 이로 인해 모든 데이터가 다시 암호화 되는 것은 아니므로 다른 작업은 필요 하지 않습니다.

SSE는 관리 디스크에 사용 되지만 고객 관리 키는 지원 되지 않습니다.  Key Vault에서 고객이 관리 하는 KEK 키와 Azure Disk Encryption를 사용 하 여 관리 디스크의 암호화를 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Id 페더레이션](identity-federation.md) 에 대 한 문서 검토

[참조 라이브러리](reference-library.md) 의 추가 Azure Key Vault 설명서 및 자습서를 검토 합니다.
