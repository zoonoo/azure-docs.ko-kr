---
title: Azure Security 벤치 마크 V2-데이터 보호
description: Azure Security 벤치 마크 V2 데이터 보호
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037118"
---
# <a name="security-control-v2-data-protection"></a>보안 제어 V2: 데이터 보호

데이터 보호는 미사용 데이터 보호, 전송 중 및 권한이 부여 된 액세스 메커니즘을 통한 제어를 포함 합니다. 여기에는 Azure에서 액세스 제어, 암호화 및 로깅을 사용 하 여 중요 한 데이터 자산을 검색, 분류, 보호 및 모니터링 하는 작업이 포함 됩니다.

해당 하는 기본 제공 Azure Policy를 보려면 [Azure 보안 벤치 마크 규정 준수 기본 제공 이니셔티브: 데이터 보호의 세부 정보](../../governance/policy/samples/azure-security-benchmark.md#data-protection) 를 참조 하세요.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: 중요한 데이터 검색, 분류 및 레이블 지정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| DP-1 | 13.1, 14.5, 14.7 | SC-28 |

중요 한 정보가 조직의 기술 시스템에서 안전 하 게 저장, 처리 및 전송 되도록 적절 한 컨트롤을 디자인 하기 위해 중요 한 데이터를 검색, 분류 및 레이블을 지정할 수 있습니다.

Azure, 온-프레미스, Office 365 및 기타 위치에 있는 Office 문서 내의 중요한 정보에 대해서는 Azure Information Protection(및 관련 검색 도구)을 사용합니다.

Azure SQL 정보 보호를 사용하여 Azure SQL Database에 저장된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure Information Protection을 사용하여 중요한 정보 태그 지정](/azure/information-protection/what-is-information-protection) 

- [Azure SQL 데이터 검색을 구현하는 방법](../../azure-sql/database/data-discovery-and-classification-overview.md)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| DP-2 | 13.2, 2.10 | SC-7, AC-4 |

Azure RBAC (역할 기반 액세스 제어), 네트워크 기반 액세스 제어 및 Azure 서비스의 특정 제어 (예: SQL 및 기타 데이터베이스의 암호화)를 사용 하 여 액세스를 제한 하 여 중요 한 데이터를 보호 합니다. 

일관된 액세스 제어를 보장하려면 모든 유형의 액세스 제어를 엔터프라이즈 조각화 전략에 맞게 조정해야 합니다. 또한 엔터프라이즈 조각화 전략은 중요 비즈니스용 데이터 및 시스템의 위치를 통해 알려야 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 몇 가지 기본 데이터 보호 제어 및 기능을 구현했습니다.

- [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)

- [Azure의 고객 데이터 보호 이해](../fundamentals/protection-customer-data.md)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: 중요한 데이터 무단 전송 모니터링

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| DP-3 | 13.3 | AC-4, SI-4 |

Enterprise visibility 및 control 외부의 위치로 데이터를 무단으로 전송 하는 방법을 모니터링 합니다. 여기에는 일반적으로 무단 데이터 반출을 나타낼 수 있는 비정상 활동(대량 또는 비정상 전송)에 대한 모니터링이 포함됩니다. 

Azure Storage ATP(Advanced Threat Protection) 및 Azure SQL ATP는 중요한 정보의 무단 전송을 나타낼 수 있는 비정상 정보 전송에 대해 경고할 수 있습니다. 

AIP(Azure Information Protection)는 분류되고 레이블이 지정된 정보에 대한 모니터링 기능을 제공합니다. 

DLP(데이터 손실 방지)를 준수하는 데 필요한 경우 데이터 반출을 방지하기 위해 호스트 기반 DLP 솔루션을 사용하여 탐지 및/또는 예방 제어를 적용할 수 있습니다.

- [Azure Defender for SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [스토리지용 Azure Defender](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security) 

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요한 정보 암호화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

액세스 제어를 보완 하기 위해 전송 중인 데이터는 공격자가 데이터를 쉽게 읽거나 수정할 수 없도록 암호화를 사용 하 여 "대역 외" 공격 (예: 트래픽 캡처) 으로부터 보호 되어야 합니다.

개인 네트워크의 트래픽에 대 한 옵션은 선택 사항 이지만 외부 및 공용 네트워크의 트래픽에 매우 중요 합니다. HTTP 트래픽의 경우 Azure 리소스에 연결 하는 모든 클라이언트에서 TLS v 1.2 이상을 협상할 수 있는지 확인 합니다. 원격 관리의 경우 암호화 되지 않은 프로토콜 대신 SSH (Linux) 또는 RDP/TLS (Windows 용)를 사용 합니다. 사용 되지 않는 SSL, TLS, SSH 버전 및 프로토콜, 약한 암호를 사용 하지 않도록 설정 해야 합니다.

기본적으로 Azure는 Azure 데이터 센터 간에 전송 중인 데이터에 대 한 암호화를 제공 합니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 보안에 대한 정보](/security/engineering/solving-tls1-problem)

- [전송 중인 Azure 데이터에 대 한 이중 암호화](../fundamentals/double-encryption.md#data-in-transit)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: 중요한 미사용 데이터 암호화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

액세스 제어를 보완 하려면 암호화를 사용 하 여 미사용 데이터를 ' 대역 외 ' 공격 (예: 기본 저장소 액세스) 으로부터 보호 해야 합니다. 이렇게 하면 공격자가 데이터를 쉽게 읽거나 수정할 수 없게 됩니다. 

Azure는 기본적으로 미사용 데이터에 대 한 암호화를 제공 합니다. 매우 중요 한 데이터의 경우 사용할 수 있는 모든 Azure 리소스에서 미사용 추가 암호화를 구현 하는 옵션을 사용할 수 있습니다. Azure는 기본적으로 암호화 키를 관리 하지만, Azure는 특정 Azure 서비스에 대 한 고유한 키 (고객 관리 키)를 관리 하는 옵션을 제공 합니다.

- [Azure의 저장 데이터 암호화 이해](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [고객 관리 암호화 키를 구성 하는 방법](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [암호화 모델 및 키 관리 테이블](../fundamentals/encryption-models.md)

- [Azure의 미사용 데이터 이중 암호화](../fundamentals/double-encryption.md#data-at-rest)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)