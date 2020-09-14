---
title: Azure Security 벤치 마크 V2-데이터 보호
description: Azure Security 벤치 마크 V2 데이터 보호
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e377c6669b1db96f549958173aded4f29bae6713
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059254"
---
# <a name="security-control-data-protection"></a>보안 제어: 데이터 보호

데이터 보호는 미사용 데이터 보호, 전송 중 및 권한이 부여 된 액세스 메커니즘을 통한 제어를 포함 합니다. 여기에는 Azure에서 액세스 제어, 암호화 및 로깅을 사용 하 여 중요 한 데이터 자산을 검색, 분류, 보호 및 모니터링 하는 작업이 포함 됩니다.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: 검색, 중요 한 데이터 분류 및 레이블

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| DP-1 | 14.5, 14.7 | SI-4, SC-28 |

중요 한 정보가 조직의 기술 시스템에서 안전 하 게 저장, 처리 및 전송 되도록 적절 한 컨트롤을 디자인 하기 위해 중요 한 데이터를 검색, 분류 및 레이블을 지정할 수 있습니다. 

Azure, 온-프레미스, Office 365 및 기타 위치에서 Office 문서 내의 중요 한 정보에 대 한 Azure Information Protection 및 연결 된 검색 도구를 사용 합니다. 

Azure sql Information Protection를 사용 하 여 Azure SQL Database에 저장 된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure Information Protection를 사용 하 여 중요 한 정보에 태그](/azure/information-protection/what-is-information-protection) 

- [Azure SQL 데이터 검색을 구현 하는 방법](/azure/sql-database/sql-database-data-discovery-and-classification)

**책임**: 공유됨

**고객 보안 관련자**:

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요 한 데이터 보호

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| DP-2 | 13.2, 2.10 | SC-7, AC-4 |

Azure RBAC (azure 역할 기반 Access Control), 네트워크 기반 액세스 제어 및 Azure 서비스의 특정 제어 (예: SQL 및 기타 데이터베이스의 암호화)를 사용 하 여 액세스를 제한 하 여 중요 한 데이터를 보호 합니다. 

일관 된 액세스 제어를 보장 하기 위해 모든 종류의 액세스 제어를 기업의 조각화 전략에 맞춰야 합니다. 또한 기업 구분 전략은 중요 한 데이터 나 중요 한 데이터 및 시스템의 위치를 통해 알려 주어 야 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 몇 가지 기본 데이터 보호 제어 및 기능을 구현 했습니다.

- [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)

- [Azure의 고객 데이터 보호 이해](../fundamentals/protection-customer-data.md)

**책임**: 공유됨

**고객 보안 관련자**:

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: 중요 한 데이터의 무단 전송 모니터링

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| DP-3 | 13.3 | AC-4, SI-4 |

Enterprise visibility 및 control 외부의 위치로 데이터를 무단으로 전송 하는 방법을 모니터링 합니다. 이 작업에는 일반적으로 권한이 없는 데이터 exfiltration을 나타낼 수 있는 비정상 작업 (크거나 비정상적인 전송)에 대 한 모니터링이 포함 됩니다. 

ATP (Advanced Threat Protection) 및 Azure SQL ATP는 중요 한 정보를 무단으로 전송 하는 것을 나타낼 수 있는 비정상적인 정보 전송에 대해 경고할 수 있습니다. Azure Storage 

AIP (Azure Information protection)는 분류 되 고 레이블이 지정 된 정보에 대 한 모니터링 기능을 제공 합니다. 

DLP (데이터 손실 방지)의 호환성을 위해 필요한 경우 호스트 기반 DLP 솔루션을 사용 하 여 감지 및/또는 예방 제어를 적용 하 여 데이터 exfiltration을 방지할 수 있습니다.

- [Azure SQL ATP 사용](../../azure-sql/database/threat-detection-overview.md)

- [ATP Azure Storage 사용](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**책임**: 공유됨

**고객 보안 관련자**:

- [SOC(보안 운영 센터)](/azure/cloud-adoption-framework/organize/cloud-security) 

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요 한 정보 암호화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

액세스 제어를 보완 하기 위해 전송 중인 데이터를 암호화를 사용 하 여 ' 대역 외 ' 공격 (예: 트래픽 캡처) 으로부터 보호 하 여 공격자가 데이터를 쉽게 읽거나 수정할 수 없도록 해야 합니다. 

개인 네트워크의 트래픽에 대 한 옵션은 선택 사항 이지만 외부 및 공용 네트워크의 트래픽에 매우 중요 합니다. HTTP 트래픽의 경우 Azure 리소스에 연결 하는 모든 클라이언트에서 TLS v 1.2 이상을 협상할 수 있는지 확인 합니다. 원격 관리의 경우 암호화 되지 않은 프로토콜 대신 SSH (Linux) 또는 RDP/TLS (Windows 용)를 사용 합니다. 사용 되지 않는 SSL, TLS, SSH 버전 및 프로토콜, 약한 암호를 사용 하지 않도록 설정 해야 합니다.  

기본적으로 Azure는 Azure 데이터 센터 간에 전송 중인 데이터에 대 한 암호화를 제공 합니다. 

- [Azure를 사용 하 여 전송 중인 암호화 이해](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 보안에 대 한 정보](/security/engineering/solving-tls1-problem)

- [전송 중인 Azure 데이터에 대 한 이중 암호화](../fundamentals/double-encryption.md#data-in-transit)

**책임**: 공유됨

**고객 보안 관련자**:

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: 휴지 상태의 중요 한 데이터 암호화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

액세스 제어를 보완 하려면 암호화를 사용 하 여 미사용 데이터를 ' 대역 외 ' 공격 (예: 기본 저장소 액세스) 으로부터 보호 해야 합니다. 이렇게 하면 공격자가 데이터를 쉽게 읽거나 수정할 수 없게 됩니다. 

Azure는 기본적으로 미사용 데이터에 대 한 암호화를 제공 합니다. 매우 중요 한 데이터의 경우 사용할 수 있는 모든 Azure 리소스에서 미사용 추가 암호화를 구현 하는 옵션을 사용할 수 있습니다. Azure는 기본적으로 암호화 키를 관리 하지만, Azure는 특정 Azure 서비스에 대 한 고유한 키 (고객 관리 키)를 관리 하는 옵션을 제공 합니다.

- [Azure에서 미사용 암호화 이해](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [고객 관리 암호화 키를 구성 하는 방법](../../storage/common/storage-encryption-keys-portal.md)

- [암호화 모델 및 키 관리 테이블](../fundamentals/encryption-models.md)

- [Azure의 미사용 데이터 이중 암호화](../fundamentals/double-encryption.md#data-at-rest)

**책임**: 공유됨

**고객 보안 관련자**:

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

