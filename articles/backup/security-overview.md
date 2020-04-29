---
title: 보안 기능 개요
description: 백업 데이터를 보호 하 고 비즈니스의 보안 요구를 충족 하는 데 도움이 되는 Azure Backup의 보안 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585827"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure Backup의 보안 기능 개요

데이터를 보호 하기 위해 수행할 수 있는 가장 중요 한 단계 중 하나는 안정적인 백업 인프라를 보유 하는 것입니다. 그러나 데이터를 안전한 방식으로 백업 하 고 백업이 항상 보호 되도록 하는 것이 중요 합니다. Azure Backup은 데이터를 전송 중이거나 휴지 상태일 때 백업 환경에 대 한 보안을 제공 합니다. 이 문서에는 백업 데이터를 보호 하 고 비즈니스의 보안 요구를 충족 하는 데 도움이 되는 Azure Backup의 보안 기능이 나열 되어 있습니다.

## <a name="management-and-control-of-identity-and-user-access"></a>Id 및 사용자 액세스 관리 및 제어

Azure Backup를 사용 하면 [AZURE RBAC (역할 기반 Access Control)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)를 사용 하 여 세분화 액세스를 관리할 수 있습니다. RBAC를 사용 하면 팀 내에서 업무를 분리 하 고 사용자에 게 해당 작업을 수행 하는 데 필요한 만큼의 액세스 권한만 부여할 수 있습니다.

Azure Backup는 백업 관리 작업을 제어 하는 세 가지 기본 제공 역할을 제공 합니다.

* 백업 참여자-Recovery Services 자격 증명 모음을 삭제 하 고 다른 사용자에 게 액세스 권한을 부여 하는 경우를 제외 하 고 백업을 만들고 관리
* 백업 운영자-백업 제거 및 백업 정책 관리를 제외한 모든 참가자
* 백업 판독기-모든 백업 관리 작업을 볼 수 있는 권한

[역할 기반 액세스 제어에서 Azure Backup 관리](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)에 대해 자세히 알아보세요.

Azure Backup에는 보안 취약성을 예방, 감지 및 대응 하기 위해 서비스에 기본 제공 되는 몇 가지 보안 컨트롤이 있습니다. [Azure Backup에 대 한 보안 제어](https://docs.microsoft.com/azure/backup/backup-security-controls)에 대해 자세히 알아보세요.

## <a name="separation-between-guest-and-azure-storage"></a>게스트와 Azure 저장소 간의 분리

가상 컴퓨터 백업 및 SQL을 포함 하는 Azure Backup와 VM 백업에서의 SAP HANA 백업 데이터는 Azure storage에 저장 되며 게스트는 백업 저장소 또는 해당 콘텐츠에 직접 액세스할 수 없습니다.  가상 머신 백업을 사용 하 여 백업 스냅숏 만들기 및 저장소는 게스트에서 응용 프로그램 일치 백업에 대 한 워크 로드를 정지 하는 것 외에는 참여 하지 않는 Azure fabric에 의해 수행 됩니다.  SQL 및 SAP HANA를 사용 하는 경우 백업 확장 프로그램은 특정 blob에 쓰기 위한 임시 액세스 권한을 가집니다.  이러한 방식으로 손상 된 환경 에서도 기존 백업은 게스트에 의해 변조 또는 삭제 될 수 없습니다.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 백업에 인터넷 연결이 필요 하지 않음

Azure Vm을 백업 하려면 가상 머신의 디스크에서 Recovery Services 자격 증명 모음으로 데이터를 이동 해야 합니다. 그러나 모든 필수 통신 및 데이터 전송은 가상 네트워크에 액세스할 필요 없이 Azure 백본 네트워크 에서만 수행 됩니다. 따라서 보안 네트워크 내에 배치 된 Azure Vm을 백업 하려면 모든 Ip 또는 Fqdn에 대 한 액세스를 허용 하지 않아도 됩니다.

## <a name="private-endpoints-for-azure-backup"></a>Azure 백업에 대 한 개인 끝점

이제 [개인 끝점](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 을 사용 하 여 가상 네트워크 내의 서버에서 Recovery Services 자격 증명 모음으로 데이터를 안전 하 게 백업할 수 있습니다. 개인 끝점은 자격 증명 모음에 대 한 VNET 주소 공간의 IP를 사용 하므로 가상 네트워크를 공용 Ip에 노출할 필요가 없습니다. 개인 끝점은 Azure Vm 내에서 실행 되는 SQL 및 SAP HANA 데이터베이스를 백업 및 복원 하는 데 사용할 수 있습니다. MARS 에이전트를 사용 하 여 온-프레미스 서버에도 사용할 수 있습니다.

>[!NOTE]
> 이 기능은 현재 제한 된 가용성으로 제공 됩니다. Azure Backup에 대 한 개인 끝점을 사용 하려는 경우 [이 설문 조사](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) 를 작성 하 고 [전자 메일을 보내](mailto:azbackupnetsec@microsoft.com) 주시기 바랍니다. 이 기능을 사용 하는 기능에는 Azure Backup 서비스의 승인이 적용 됩니다.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>전송 중 및 미사용 데이터 암호화

암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. Azure 내에서 Azure storage와 자격 증명 모음 간의 전송 데이터는 HTTPS에 의해 보호 됩니다. 이 데이터는 Azure 백본 네트워크에 남아 있습니다.

* 백업 데이터는 Microsoft 관리 키를 사용 하 여 자동으로 암호화 됩니다. Azure Key Vault에 저장 된 [고객 관리 키](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) 를 사용 하 여 Recovery Services 자격 증명 모음에서 백업 된 관리 디스크 vm을 암호화할 수도 있습니다. 이 암호화를 사용 하도록 설정 하기 위해 명시적인 조치를 취할 필요는 없습니다. Recovery Services 자격 증명 모음에 백업 되는 모든 워크 로드에 적용 됩니다.

* Azure Backup은 Azure Disk Encryption (ADE)로 암호화 된 OS/데이터 디스크가 있는 Azure Vm의 백업 및 복원을 지원 합니다. [암호화 된 Azure vm 및 Azure Backup에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>의도 하지 않은 삭제 로부터 백업 데이터 보호

Azure Backup는 삭제 후에도 백업 데이터를 보호 하는 데 도움이 되는 보안 기능을 제공 합니다. 일시 삭제를 사용 하는 경우 사용자가 VM의 백업을 삭제 하면 백업 데이터는 14 일 동안 보존 되어 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 14 일의 추가 보존은 고객에 게 비용을 부과 하지 않습니다. [일시 삭제에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>의심 스러운 활동의 모니터링 및 경고

Azure Backup는 Azure Backup와 관련 된 이벤트에 대 한 작업을 보고 구성 하기 위한 [기본 제공 모니터링 및 경고 기능](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 을 제공 합니다. [백업 보고서](https://docs.microsoft.com/azure/backup/configure-reports) 는 사용량을 추적 하 고, 백업 및 복원을 감사 하 고, 다양 한 세분성 수준에서 키 추세를 식별 하는 데 사용 되는 일회성 대상 역할을 합니다. Azure Backup의 모니터링 및 보고 도구를 사용 하면 무단, 의심 스러운 또는 악의적인 활동을 발생 하는 즉시 사용자에 게 경고할 수 있습니다.

## <a name="security-features-to-help-protect-hybrid-backups"></a>하이브리드 백업을 보호 하는 데 도움이 되는 보안 기능

Azure Backup 서비스는 MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업 하 고 복원 합니다. 이제 MARS는 하이브리드 백업을 보호 하는 데 도움이 되는 보안 기능을 제공 합니다. 이러한 기능으로는 다음이 포함됩니다.

* 암호 변경과 같은 중요한 작업이 수행될 때마다 추가적인 인증 계층이 제공됩니다. 이 유효성 검사는 유효한 Azure 자격 증명을 가진 사용자만 이러한 작업을 수행할 수 있는지 확인합니다. [공격을 방지 하는 기능에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* 삭제 된 백업 데이터는 삭제 일 로부터 추가 14 일 동안 보존 됩니다. 이렇게 하면 지정 된 기간 내에 데이터를 복구할 수도 있으므로 공격이 발생 하더라도 데이터가 손실 되지 않습니다. 또한 데이터 손상으로부터 보호하기 위해 더 많은 수의 최소 복구 지점이 유지됩니다. [삭제 된 백업 데이터 복구에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 백업 된 데이터의 경우 Azure Backup에서 다운로드 한 후에만 Azure Backup에 업로드 하 고 해독 하기 전에 데이터를 암호화 하는 데 암호가 사용 됩니다. 암호 정보는 암호를 만든 사용자와 해당 암호를 사용 하 여 구성 된 에이전트에 대해서만 사용할 수 있습니다. 서비스와 함께 전송 되거나 공유 되지 않습니다. 이를 통해 실수로 노출 되는 데이터 (예: 네트워크에 대 한 메시지 가로채기 (man-in-the-middle) 공격)를 암호 없이 사용할 수 없고 암호가 네트워크에서 전송 되지 않으므로 데이터를 완벽 하 게 보안 할 수 있습니다.

## <a name="compliance-with-standardized-security-requirements"></a>표준화 된 보안 요구 사항 준수

조직에서 개인의 데이터 수집 및 사용을 제어 하는 국가, 지역 및 산업별 요구 사항을 준수 하는 데 도움이 되는 Microsoft Azure & Azure Backup는 포괄적인 인증 및 증명 집합을 제공 합니다. [준수 인증 목록 확인](compliance-offerings.md)

## <a name="next-steps"></a>다음 단계

* [Azure Backup를 사용 하는 클라우드 워크 로드를 보호 하는 데 도움이 되는 보안 기능](backup-azure-security-feature-cloud.md)
* [Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능](backup-azure-security-feature.md)
