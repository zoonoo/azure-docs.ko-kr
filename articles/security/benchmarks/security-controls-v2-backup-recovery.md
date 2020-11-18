---
title: Azure Security 벤치 마크 V2-백업 및 복구
description: Azure Security 벤치 마크 V2 백업 및 복구
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ed477c6cf069dbb402e5bda9c1f48f7bc208f18
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698804"
---
# <a name="security-control-v2-backup-and-recovery"></a>보안 제어 V2: 백업 및 복구

백업 및 복구는 서로 다른 서비스 계층의 데이터 및 구성 백업이 수행 되 고, 유효성을 검사 하 고, 보호 되도록 하는 컨트롤을 포함 합니다.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: 정기적으로 자동 백업 확인

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

예기치 않은 이벤트 후 비즈니스 연속성을 유지 하기 위해 시스템 및 데이터를 백업 하 고 있는지 확인 합니다. RPO (복구 지점 목표) 및 RTO (복구 시간 목표)의 모든 목표로 정의 해야 합니다.

Azure Backup를 사용 하도록 설정 하 고, 원하는 빈도 및 보존 기간 뿐만 아니라 백업 원본 (예: Azure Vm, SQL Server, HANA 데이터베이스 또는 파일 공유)을 구성 합니다.  

더 높은 수준의 보호를 위해 지역 중복 저장소 옵션을 사용 하 여 백업 데이터를 보조 지역으로 복제 하 고 지역 간 복원을 사용 하 여 복구할 수 있습니다.

- [엔터프라이즈 규모 비즈니스 연속성 및 재해 복구](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Azure Backup를 사용 하도록 설정 하는 방법](../../backup/index.yml)

- [지역 간 복원을 사용 하도록 설정 하는 방법](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [정책 및 표준](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: 백업 데이터 암호화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

백업이 공격 으로부터 보호 되는지 확인 합니다. 여기에는 기밀성의 손실을 방지 하기 위해 백업의 암호화가 포함 됩니다.   

Azure Backup를 사용 하는 온-프레미스 백업의 경우 제공 된 암호를 사용 하 여 미사용 암호화가 제공 됩니다. 정기 Azure 서비스 백업의 경우 백업 데이터는 Azure 플랫폼 관리 키를 사용 하 여 자동으로 암호화 됩니다. 고객 관리 키를 사용 하 여 백업을 암호화 하도록 선택할 수 있습니다. 이 경우 키 자격 증명 모음에서이 고객이 관리 하는 키도 백업 범위에 있는지 확인 합니다. 

Azure Backup, Azure Key Vault 또는 기타 리소스의 Azure 역할 기반 액세스 제어를 사용 하 여 백업 및 고객 관리 키를 보호 합니다. 또한 백업을 변경 하거나 삭제 하기 전에 고급 보안 기능을 사용 하도록 설정 하 여 MFA를 요구할 수 있습니다.

- [Azure Backup의 보안 기능 개요](../../backup/security-overview.md)

- [고객 관리 키를 사용 하 여 백업 데이터 암호화](../../backup/encryption-at-rest-with-cmk.md) 

- [Azure에서 Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [공격 으로부터 하이브리드 백업을 보호 하는 데 도움이 되는 보안 기능](../../backup/backup-azure-security-feature.md#prevent-attacks)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: 고객이 관리 하는 키를 포함 한 모든 백업 유효성 검사

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| BR-3 | 10.3 | CP-4, CP-9 |

정기적으로 백업 데이터 복원을 수행 합니다. 백업 된 고객 관리 키를 복원할 수 있는지 확인 합니다.

- [Azure Virtual Machine 백업에서 파일을 복구 하는 방법](../../backup/backup-azure-restore-files-from-vm.md)

- [Azure에서 Key Vault 키를 복원 하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: 분실 한 키의 위험 완화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

키의 손실을 방지 하 고 복구할 수 있는 측정값이 있는지 확인 합니다. Azure Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다.  

- [Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하는 방법](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [데이터 보안](/azure/cloud-adoption-framework/organize/cloud-security-data-security)