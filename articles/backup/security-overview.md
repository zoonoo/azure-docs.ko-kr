---
title: 보안 기능 개요
description: 백업 데이터를 보호하고 비즈니스의 보안 요구 사항을 충족하는 데 도움이 되는 Azure Backup의 보안 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423185"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 백업의 보안 기능 개요

데이터를 보호하기 위해 취할 수 있는 가장 중요한 단계 중 하나는 신뢰할 수 있는 백업 인프라를 두는 것입니다. 그러나 데이터를 안전한 방식으로 백업하고 백업을 항상 보호하는 것이 중요합니다. Azure Backup은 데이터가 전송 중일 때와 미사용 모두 백업 환경에 보안을 제공합니다. 이 문서에서는 백업 데이터를 보호하고 비즈니스의 보안 요구 사항을 충족하는 데 도움이 되는 Azure Backup의 보안 기능을 나열합니다.

## <a name="management-and-control-of-identity-and-user-access"></a>ID 및 사용자 액세스 관리 및 제어

Azure Backup을 사용하면 [RBAC(Azure 역할 기반 액세스 제어)를](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)사용하여 세분화된 액세스를 관리할 수 있습니다. RBAC를 사용하면 팀 내에서 업무를 분리하고 작업을 수행하는 데 필요한 사용자에게 액세스 권한을 부여할 수 있습니다.

Azure Backup은 백업 관리 작업을 제어하는 세 가지 기본 제공 역할을 제공합니다.

* 백업 기여자 - 복구 서비스 자격 증명 모음을 삭제하고 다른 사람에게 액세스 권한을 부여하는 경우를 제외하고 백업을 만들고 관리합니다.
* 백업 연산자 - 백업 제거 및 백업 정책 관리를 제외한 기여자가 수행하는 모든 것
* 백업 판독기 - 모든 백업 관리 작업을 볼 수 있는 권한

[Azure 백업을 관리하기 위한 역할 기반 액세스 제어에](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)대해 자세히 알아봅니다.

Azure Backup에는 보안 취약점을 방지, 검색 및 대응하기 위해 서비스에 기본 제공된 몇 가지 보안 컨트롤이 있습니다. Azure 백업 에 [대한 보안 제어에 대해](https://docs.microsoft.com/azure/backup/backup-security-controls)자세히 알아보기.

## <a name="separation-between-guest-and-azure-storage"></a>게스트 저장소와 Azure 저장소 간의 구분

VM 백업에 가상 컴퓨터 백업 및 SQL 및 SAP HANA를 포함하는 Azure Backup을 사용하면 백업 데이터가 Azure 저장소에 저장되고 게스트는 백업 저장소 또는 해당 내용에 직접 액세스할 수 없습니다.  가상 시스템 백업을 사용하면 게스트가 응용 프로그램 일관된 백업을 위해 워크로드를 정지하는 것 외에는 관여하지 않는 Azure 패브릭에서 백업 스냅샷 생성 및 저장소를 수행합니다.  SQL 및 SAP HANA를 사용하면 백업 확장이 특정 Blob에 쓸 임시 액세스 권한을 얻게 됩니다.  이러한 방식으로 손상된 환경에서도 게스트가 기존 백업을 변조하거나 삭제할 수 없습니다.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 백업에 인터넷 연결이 필요하지 않습니다.

Azure VM을 백업하려면 가상 시스템의 디스크에서 복구 서비스 자격 증명 모음으로 데이터를 이동해야 합니다. 그러나 필요한 모든 통신 및 데이터 전송은 가상 네트워크에 액세스할 필요 없이 Azure 백본 네트워크에서만 발생합니다. 따라서 보안 네트워크 내에 배치된 Azure VM의 백업은 모든 IP 또는 FQDN에 대한 액세스를 허용할 필요가 없습니다.

## <a name="private-endpoints-for-azure-backup"></a>Azure 백업을 위한 개인 끝점

이제 프라이빗 [엔드포인트를](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 사용하여 가상 네트워크 내의 서버에서 복구 서비스 자격 증명 모음으로 데이터를 안전하게 백업할 수 있습니다. 개인 끝점은 볼트의 VNET 주소 공간의 IP를 사용하므로 가상 네트워크를 공용 IP에 노출할 필요가 없습니다. 개인 엔드포인트는 Azure VM 내에서 실행되는 SQL 및 SAP HANA 데이터베이스를 백업하고 복원하는 데 사용할 수 있습니다. MARS 에이전트를 사용하여 온-프레미스 서버에도 사용할 수 있습니다.

>[!NOTE]
> 이 기능은 현재 사용 가능 여부가 제한되어 있습니다. Azure 백업에 대한 비공개 엔드포인트 사용에 관심이 있으시면 [이 설문 조사를](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) 작성하고 이메일을 [보내주십시오.](mailto:azbackupnetsec@microsoft.com) 이 기능을 사용할 수 있는 기능은 Azure Backup 서비스의 승인을 받아야 합니다.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>전송 중 및 미사용 데이터의 암호화

암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. Azure 내에서 Azure 저장소와 볼트 간에 전송되는 데이터는 HTTPS로 보호됩니다. 이 데이터는 Azure 백본 네트워크에 남아 있습니다.

* 백업 데이터는 Microsoft에서 관리하는 키를 사용하여 자동으로 암호화됩니다. Azure Key Vault에 저장된 [고객 관리 키를](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) 사용하여 복구 서비스 볼트에서 백업된 관리 디스크 VM을 암호화할 수도 있습니다. 이 암호화를 활성화하기 위해 명시적 조치를 취할 필요가 없습니다. 복구 서비스 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.

* Azure Backup은 ADE(Azure 디스크 암호화)로 암호화된 OS/데이터 디스크가 있는 Azure VM의 백업 및 복원을 지원합니다. [암호화된 Azure VM 및 Azure 백업에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>의도하지 않은 삭제로부터 백업 데이터 보호

Azure Backup은 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다. 사용자가 VM의 백업을 삭제하면 백업 데이터가 14일 동안 유지되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "소프트 삭제" 상태에서 백업 데이터를 14일 더 보존해도 고객에게 비용이 발생하지 않습니다. [소프트 삭제 에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>의심스러운 활동 모니터링 및 경고

Azure Backup은 Azure [Backup과](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 관련된 이벤트에 대한 작업을 보고 구성하는 기본 제공 모니터링 및 경고 기능을 제공합니다. [백업 보고서는](https://docs.microsoft.com/azure/backup/configure-reports) 사용량 추적, 백업 및 복원 감사, 다양한 세분성 수준에서 주요 추세를 식별하기 위한 원스톱 대상 역할을 합니다. Azure Backup의 모니터링 및 보고 도구를 사용하면 무단, 의심스러운 또는 악의적인 활동이 발생하는 즉시 경고할 수 있습니다.

## <a name="security-features-to-help-protect-hybrid-backups"></a>하이브리드 백업을 보호하는 데 도움이 되는 보안 기능

Azure 백업 서비스는 Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원합니다. MARS는 이제 하이브리드 백업을 보호하는 데 도움이 되는 보안 기능을 제공합니다. 이러한 기능으로는 다음이 포함됩니다.

* 암호 변경과 같은 중요한 작업이 수행될 때마다 추가적인 인증 계층이 제공됩니다. 이 유효성 검사는 유효한 Azure 자격 증명을 가진 사용자만 이러한 작업을 수행할 수 있는지 확인합니다. [공격을 방지하는 기능에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)

* 삭제된 백업 데이터는 삭제일로부터 14일 동안 추가로 유지됩니다. 이렇게 하면 지정된 기간 내에 데이터를 복구할 수 있으므로 공격이 발생하더라도 데이터 손실이 발생하지 않습니다. 또한 데이터 손상으로부터 보호하기 위해 더 많은 수의 최소 복구 지점이 유지됩니다. [삭제된 백업 데이터 복구에 대해 자세히 알아봅니다.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)

## <a name="compliance-with-standardized-security-requirements"></a>표준화된 보안 요구 사항 준수

Microsoft Azure Azure & Azure Backup은 조직이 개인 데이터의 수집 및 사용에 적용되는 국가, 지역 및 산업별 요구 사항을 준수할 수 있도록 포괄적인 인증 및 증명 을 제공합니다. [규정 준수 인증 목록 보기](compliance-offerings.md)

## <a name="next-steps"></a>다음 단계

* [Azure Backup을 사용하는 클라우드 워크로드를 보호하는 데 도움이 되는 보안 기능](backup-azure-security-feature-cloud.md)
* [Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능](backup-azure-security-feature.md)
