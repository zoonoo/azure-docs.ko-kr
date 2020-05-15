---
title: CIS Microsoft Azure Foundations 벤치마크 청사진 샘플 컨트롤
description: Azure Policy에 대한 CIS Microsoft Azure Foundations 벤치마크 청사진 샘플 추천 매핑입니다.
ms.date: 05/06/2020
ms.topic: sample
ms.openlocfilehash: 2163162f52eb4ad7f580c01d6539c242bd332645
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863963"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations 벤치마크 청사진 샘플에 대한 추천 매핑

다음 문서에서는 Azure Blueprints CIS Microsoft Azure Foundations 벤치마크 청사진 샘플을 CIS Microsoft Azure Foundations 벤치마크 추천에 매핑하는 방법을 자세히 설명합니다. 추천에 대한 자세한 내용은 [CIS Microsoft Azure Foundations 벤치마크](https://www.cisecurity.org/benchmark/azure/)를 참조하세요.

다음은 **CIS Microsoft Azure Foundations 벤치마크 v1.1.0** 추천에 대한 매핑입니다. 오른쪽의 탐색을 사용하면 특정한 추천 매핑으로 바로 이동할 수 있습니다.
매핑된 많은 권장 사항은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 통해 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\] CIS Microsoft Azure Foundations 벤치마크 v1.1.0 추천 감사 및 특정 VM 확장 배포를 통해 감사 요구 사항 지원** 기본 제공 정책 이니셔티브를 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)을 참조하세요.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 권한이 있는 모든 사용자에 대해 다단계 인증이 활성화되어 있는지 확인

이 청사진은 권한 있는 Azure Active Directory 계정에서 다단계 인증을 사용하지 않는 경우를 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 권한이 없는 모든 사용자에 대해 다단계 인증이 활성화되어 있는지 확인

이 청사진은 비 Active Azure Active Directory 계정에서 다단계 인증을 사용하지 않는 경우를 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 게스트 사용자가 없는지 확인

이 청사진은 제거해야 할 수 있는 게스트 계정을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 사용자 지정 구독 소유자 역할이 만들어지지 않았는지 확인

이 청사진은 제거해야 할 수 있는 사용자 지정 구독 소유자 역할을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 사용자 지정 구독 소유자 역할이 없어야 합니다.

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 표준 가격 책정 계층이 선택되어 있는지 확인

이 청사진은 Security Center 표준 계층을 사용하지 않는 네트워크와 가상 머신을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Security Center 표준 가격 책정 계층을 선택해야 합니다.

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 '모니터링 에이전트의 자동 프로비저닝'이 '켜기'로 설정되어 있는지 확인

이 청사진은 Log Analytics 에이전트의 자동 프로비저닝을 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에서 Log Analytics 모니터링 에이전트의 자동 프로비저닝을 사용하도록 설정해야 합니다.

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 ASC 기본 정책 설정 "시스템 업데이트 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 시스템 업데이트를 가상 머신에 설치할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 시스템 업데이트를 머신에 설치해야 합니다.

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 ASC 기본 정책 설정 "OS 취약성 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 수정되지 않은 가상 머신 취약성을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신 보안 구성의 취약성을 수정해야 합니다.

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 ASC 기본 정책 설정 "엔드포인트 보호 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 가상 머신에서 엔드포인트 보호를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 ASC 기본 정책 설정 "디스크 암호화 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 가상 머신 디스크를 암호화할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 ASC 기본 정책 설정인 "네트워크 보안 그룹 모니터링"이 "사용 안 함"이 아닌지 확인

이 청사진은 인터넷 연결 가상 머신을 보호할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 ASC 기본 정책 설정인 "차세대 방화벽(NGFW) 모니터링을 사용하도록 설정"이 "사용 안 함"이 아닌지 확인

이 청사진은 액세스를 제한하여 서브넷과 가상 머신을 위협으로부터 보호할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이 CIS Microsoft Azure Foundations Benchmark 추천 사항에서 참조하는 Security Center 정책이 새로운 두 가지 추천 사항으로 대체되었습니다. 아래에 언급한 정책은 새 추천 사항을 처리합니다.

- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.
- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 ASC 기본 정책 설정 "취약성 평가 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 취약성을 탐지하고 수정할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 ASC 기본 정책 설정인 "Storage Blob 암호화 모니터링"이 "사용 안 함"이 아닌지 확인

Azure Storage 암호화는 모든 새 스토리지 계정 및 기존 스토리지 계정에 대해 사용하도록 설정되며, 사용하지 않도록 설정할 수 없습니다. (기본 Azure 기능이며, 할당되는 정책이 없습니다.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 ASC 기본 정책 설정 "JIT 네트워크 액세스 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 가상 머신에 대한 액세스를 제어할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 ASC 기본 정책 설정인 "적응형 애플리케이션 허용 목록 모니터링"이 "사용 안 함"이 아닌지 확인

이 청사진은 가상 머신에서 적응형 애플리케이션 제어를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 ASC 기본 정책 설정 "SQL 감사 모니터링"이 "사용 안 함"이 아닌지 확인

이 청사진은 SQL Server 감사를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 ASC 기본 정책 설정 "SQL 암호화 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 SQL 데이터베이스에서 투명한 데이터 암호화를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 '보안 연락처 이메일'이 설정되어 있는지 확인

이 청사진은 보안 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에 대한 보안 연락처 이메일 주소를 제공해야 합니다.

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 보안 연락처 '전화 번호'가 설정되어 있는지 확인

이 청사진은 보안 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에 대한 보안 연락처 전화 번호를 제공해야 합니다.

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 '높은 심각도 경고의 이메일 알림 보내기'가 '켜기'로 설정되어 있는지 확인

이 청사진은 보안 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 '구독 소유자에게도 이메일 보내기'가 '켜기'로 설정되어 있는지 확인

이 청사진은 보안 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 '보안 전송 필요'가 '활성'으로 설정되어 있는지 확인

이 청사진은 보안되지 않은 연결을 허용하는 스토리지 계정을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 스토리지 계정에 대한 기본 네트워크 액세스 규칙이 거부로 설정되어 있는지 확인

이 청사진은 무제한 액세스를 허용하는 스토리지 계정을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 '신뢰할 수 있는 Microsoft 서비스'를 스토리지 계정 액세스에 사용하도록 설정되어 있는지 확인

이 청사진은 신뢰할 수 있는 Microsoft 서비스에서 액세스할 수 없는 스토리지 계정을 모니터링할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 스토리지 계정은 신뢰할 수 있는 Microsoft 서비스의 액세스를 허용해야 합니다.

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 '감사'가 '켬'으로 설정되어 있는지 확인

이 청사진은 SQL Server 감사를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 

- SQL Server에 대한 감사가 사용되도록 설정되어야 함

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 SQL Server에 대한 '감사' 정책의 'AuditActionGroups'가 올바르게 설정되어 있는지 확인

이 청사진은 SQL Server 감사를 올바르게 구성할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 감사 설정에는 중요한 작업을 캡처하도록 구성된 작업 그룹이 있어야 합니다.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 '감사' 보존이 '90일보다 많은지' 확인

이 청사진은 SQL Server 로그를 90일 이상 유지할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 감사 보존 기간(일)을 90일보다 큰 값으로 설정하여 SQL 서버를 구성해야 합니다.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 SQL Server의 'Advanced Data Security'가 '켬'으로 설정되어 있는지 확인

이 청사진은 SQL Server 및 SQL 관리형 인스턴스에서 Advanced Data Security를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 '위협 탐지 유형'이 '모두'로 설정되어 있는지 확인

이 청사진은 SQL Server 및 SQL 관리형 인스턴스에서 Advanced Threat Protection을 올바르게 구성할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL 관리형 인스턴스의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 '경고 전송 대상'이 설정되어 있는지 확인

이 청사진은 Advanced Data Security 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 '이메일 서비스 및 공동 관리자'가 '활성'인지 확인

이 청사진은 Advanced Data Security 알림을 올바르게 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스 Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Azure Active Directory 관리자가 구성되어 있는지 확인

이 청사진은 Azure Active Directory 관리자를 SQL Server에 프로비저닝할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 SQL Database에서 '데이터 암호화'가 '켬'으로 설정되어 있는지 확인

이 청사진은 SQL 데이터베이스에서 투명한 데이터 암호화를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 SQL 서버의 TDE 보호기가 BYOK(사용자 고유 키 사용)로 암호화되어 있는지 확인

이 청사진은 SQL Server 및 SQL 관리형 인스턴스에 대한 투명한 데이터 암호화 보호기를 사용자 고유의 키로 암호화할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- SQL 관리형 인스턴스 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 MySQL Database Server에 대해 'SSL 연결 적용'이 '사용'으로 설정되어 있는지 확인

이 청사진은 MySQL 데이터베이스 서버에서 TLS/SSL 연결을 적용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 PostgreSQL Database Server에 대해 'log_checkpoints' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 검사점을 기록할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대해 로그 검사점을 사용하도록 설정해야 합니다.

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 PostgreSQL Database Server에 대해 'SSL 연결 적용'이 '사용'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 TLS/SSL 연결을 적용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 PostgreSQL Database Server에 대해 'log_connections' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 연결을 기록할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대해 로그 연결을 사용하도록 설정해야 합니다.

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 PostgreSQL Database Server에 대해 'log_disconnections' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 연결 끊김을 기록할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대한 연결 끊김을 기록해야 합니다.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 PostgreSQL Database Server에 대해 'log_duration' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 완료된 명령문의 기간을 기록할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대해 로그 기간을 사용하도록 설정해야 합니다.

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 PostgreSQL Database Server에 대해 'connection_throtling' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

이 청사진은 PostgreSQL 데이터베이스 서버에서 무차별 암호 대입 공격을 완화할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- PostgreSQL 데이터베이스 서버에 대해 연결 제한을 사용하도록 설정해야 합니다.

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Azure Active Directory 관리자가 구성되어 있는지 확인

이 청사진은 Azure Active Directory 관리자를 SQL Server에 프로비저닝할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. CIS Microsoft Azure Foundations Benchmark에는 이 추천 사항이 포함되어 있습니다. 그러나 이는 [4.8 추천 사항](#48-ensure-that-azure-active-directory-admin-is-configured)과 중복됩니다.

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 로그 프로필이 있는지 확인

이 청사진은 모든 Azure 구독에 대해 로그 프로필이 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 

- Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 합니다.

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 활동 로그 보존이 365일 이상으로 설정되어 있는지 확인

이 청사진은 작업 로그를 1년 이상 유지할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 활동 로그는 1년 이상 보존되어야 합니다.

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 감사 프로필에서 모든 활동을 캡처하는지 확인

이 청사진은 로그 프로필을 올바르게 구성할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Monitor 로그 프로필은 'write'(쓰기), 'delete'(삭제) 및 'action'(작업) 범주에 대한 로그를 수집해야 합니다.

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 로그 프로필에서 글로벌을 포함하여 모든 지역의 활동 로그를 캡처하는지 확인

이 청사진은 로그 프로필을 올바르게 구성할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Monitor는 모든 지역의 활동 로그를 수집해야 합니다.

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 활동 로그가 있는 컨테이너를 포함하는 스토리지 계정은 BYOK로 암호화해야 합니다(사용자 고유 키 사용).

이 청사진은 활동 로그를 포함하는 스토리지 계정이 BYOK로 암호화되도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 활동 로그가 있는 컨테이너를 포함하는 스토리지 계정은 BYOK로 암호화해야 합니다.

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Azure KeyVault에 대한 로깅이 '활성'인지 확인

이 청사진은 진단 로그를 Key Vault에 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 정책 할당 만들기에 대한 활동 로그 경고가 있는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 정책 작업의 활동 로그 경고가 있어야 합니다.

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 네트워크 보안 그룹 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 관리 작업의 활동 로그 경고가 있어야 합니다.

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 네트워크 보안 그룹 삭제에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 관리 작업의 활동 로그 경고가 있어야 합니다.

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 네트워크 보안 그룹 규칙 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 관리 작업의 활동 로그 경고가 있어야 합니다.

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 네트워크 보안 그룹 규칙 삭제에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 관리 작업의 활동 로그 경고가 있어야 합니다.

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 보안 솔루션 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 보안 작업의 활동 로그 경고가 있어야 합니다.

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 보안 솔루션 삭제에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 보안 작업의 활동 로그 경고가 있어야 합니다.

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 SQL Server 방화벽 규칙 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 관리 작업의 활동 로그 경고가 있어야 합니다.

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 보안 정책 업데이트에 대한 활동 로그 경고가 존재하는지 확인합니다.

이 청사진은 특정 활동 로그 경고가 존재하는지 확인하는 데 도움이 되는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 특정 보안 작업의 활동 로그 경고가 있어야 합니다.

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6.1 RDP 액세스가 인터넷에서 제한되는지 확인

이 청사진은 RDP 액세스를 제어할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 인터넷에서 RDP 액세스를 차단해야 함

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6.2 SSH 액세스가 인터넷에서 제한되는지 확인

이 청사진은 SSH 액세스를 제어할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 인터넷에서 SSH 액세스를 차단해야 함

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Network Watcher가 '사용'인지 확인

이 청사진은 Network Watcher를 리소스가 배포된 모든 지역에 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이 정책에는 해당되는 모든 지역을 지정하는 매개 변수 배열이 필요합니다. 이 정책 이니셔티브 정의의 기본값은 'eastus'입니다.

- Network Watcher를 사용하도록 설정해야 합니다.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 'OS 디스크'가 암호화되어 있는지 확인

이 청사진은 가상 머신에서 디스크 암호화를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 '데이터 디스크'가 암호화되어 있는지 확인

이 청사진은 가상 머신에서 디스크 암호화를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 '연결되지 않은 디스크'가 암호화되어 있는지 확인

이 청사진은 연결되지 않은 디스크를 암호화할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 연결되지 않은 디스크는 암호화되어야 합니다.

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 승인된 확장만 설치되어 있는지 확인

이 청사진은 승인된 가상 머신 확장만 설치할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이 정책에는 모든 승인된 가상 머신 확장을 지정하는 매개 변수 배열이 필요합니다. 이 정책 이니셔티브 정의에는 고객이 유효성을 검사해야 하는 제안된 기본값이 포함되어 있습니다. 

- 승인된 VM 확장만 설치해야 합니다.

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 모든 Virtual Machines에 최신 OS 패치가 적용되어 있는지 확인

이 청사진은 시스템 업데이트를 가상 머신에 설치할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 시스템 업데이트를 머신에 설치해야 합니다.

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 모든 Virtual Machines에 대한 엔드포인트 보호가 설치되어 있는지 확인

이 청사진은 가상 머신에서 엔드포인트 보호를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Key Vault를 복구할 수 있는지 확인

이 청사진은 실수로 삭제한 경우 Key Vault 개체를 복구할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Key Vault 개체를 복구할 수 있어야 합니다.

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Azure Kubernetes Services 내에서 RBAC(역할 기반 액세스 제어) 사용

이 청사진은 Kubernetes 서비스 클러스터에서 역할 기반 액세스 제어를 사용하여 권한을 관리할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9.1 App Service 인증이 Azure App Service에서 설정되어 있는지 확인

이 청사진은 App Service 앱에 대한 요청을 인증할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱에서 인증을 사용하도록 설정해야 합니다.
- 함수 앱에서 인증을 사용하도록 설정해야 합니다.
- 웹앱에서 인증을 사용하도록 설정해야 합니다.

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Azure App Service에서 웹앱이 모든 HTTP 트래픽을 HTTPS로 리디렉션하는지 확인

이 청사진은 보안 연결을 통해서만 웹 애플리케이션에 액세스할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 웹앱에서 최신 버전의 TLS 암호화를 사용하고 있는지 확인

이 청사진은 웹앱에서 최신 TLS 버전을 사용할 수 있도록 지원하는 [Azure 정책](../../../policy/overview.md) 정의를 할당합니다.

- API 앱에서 최신 TLS 버전을 사용해야 합니다.
- 함수 앱에서 최신 TLS 버전을 사용해야 합니다.
- 웹앱에서 최신 TLS 버전을 사용해야 합니다.

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인

이 청사진은 올바른 인증서를 사용하는 클라이언트만 웹앱에 연결할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.
- 함수 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.
- 웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 App Service에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인

이 청사진은 웹앱에서 관리 ID를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.
- 함수 앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.
- 웹앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 웹앱의 일부로 사용되는 경우 최신의 '.Net Framework' 버전인지 확인

이 청사진은 웹앱에서 최신 버전의 .Net Framework를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱의 일부로 사용되는 경우 최신의 '.Net Framework' 버전인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 '.Net Framework' 버전인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 '.Net Framework' 버전인지 확인합니다.

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 웹앱을 실행하는 데 사용되는 경우 최신의 'PHP 버전'인지 확인

이 청사진은 웹앱에서 최신 버전의 PHP를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 웹앱을 실행하는 데 사용되는 경우 최신의 'Python 버전'인지 확인

이 청사진은 웹앱에서 최신 버전의 Python을 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 웹앱을 실행하는 데 사용되는 경우 최신의 'Java 버전'인지 확인

이 청사진은 웹앱에서 최신 버전의 Java를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인

이 청사진은 웹앱에서 최신 버전의 HTTP를 사용할 수 있도록 지원하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- API 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.
- 함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.
- 웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.

## <a name="next-steps"></a>다음 단계

CIS Microsoft Azure Foundations Benchmark 청사진에 대한 제어 매핑을 검토했으면, 다음 문서를 방문하여 청사진에 대해 알아보거나 Azure Portal의 Azure Policy를 방문하여 이니셔티브를 할당합니다.

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark 청사진- 개요](./index.md)
> [CIS Microsoft Azure Foundations Benchmark 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기