---
title: 샘플 - CIS Microsoft Azure Foundations 벤치마크 청사진 - 추천 매핑
description: Azure Policy에 대한 CIS Microsoft Azure Foundations 벤치마크 청사진 샘플 추천 매핑입니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 118ff46ca3b640c6ec24ab85fd598f213229417a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014018"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CIS Microsoft Azure Foundations 벤치마크 청사진 샘플에 대한 추천 매핑

다음 문서에서는 Azure Blueprints CIS Microsoft Azure Foundations 벤치마크 청사진 샘플을 CIS Microsoft Azure Foundations 벤치마크 추천에 매핑하는 방법을 자세히 설명합니다. 추천에 대한 자세한 내용은 [CIS Microsoft Azure Foundations 벤치마크](https://www.cisecurity.org/benchmark/azure/)를 참조하세요.

다음은 **CIS Microsoft Azure Foundations 벤치마크 v1.1.0** 추천에 대한 매핑입니다. 오른쪽의 탐색을 사용하면 특정한 추천 매핑으로 바로 이동할 수 있습니다.
매핑된 많은 권장 사항은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 통해 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\] CIS Microsoft Azure Foundations 벤치마크 v1.1.0 추천 감사 및 특정 VM 확장 배포를 통해 감사 요구 사항 지원** 기본 제공 정책 이니셔티브를 선택합니다.

> [!NOTE]
> 전체 청사진 샘플이 곧 제공될 예정입니다. 지금은 연결된 Azure Policy 이니셔티브를 사용할 수 있습니다.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 권한이 있는 모든 사용자에 대해 다단계 인증이 활성화되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 권한이 없는 모든 사용자에 대해 다단계 인증이 활성화되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 게스트 사용자가 없는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 ASC 기본 정책 설정 "시스템 업데이트 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 시스템 업데이트를 머신에 설치해야 합니다.

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 ASC 기본 정책 설정 "OS 취약성 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 머신 보안 구성의 취약성을 수정해야 합니다.

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 ASC 기본 정책 설정 "엔드포인트 보호 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 ASC 기본 정책 설정 "디스크 암호화 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 ASC 기본 정책 설정 "웹 애플리케이션 방화벽 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- IaaS에서 웹 애플리케이션에 대한 NSG 규칙을 강화해야 합니다.

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 ASC 기본 정책 설정 "취약성 평가 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 ASC 기본 정책 설정 "JIT 네트워크 액세스 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 ASC 기본 정책 설정 "SQL 암호화 모니터링"이 "비활성"이 아닌지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 '보안 전송 필요'가 '활성'으로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 스토리지 계정에 대한 기본 네트워크 액세스 규칙이 거부로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 '감사'가 '켬'으로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server의 고급 데이터 보안 설정에서 감사를 활성화해야 합니다.

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 SQL Server에 대한 '감사' 정책의 'AuditActionGroups'가 올바르게 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 감사 설정에는 중요한 작업을 캡처하도록 구성된 작업 그룹이 있어야 합니다.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 '감사' 보존이 '90일보다 많은지' 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 감사 보존 기간(일)을 90일보다 큰 값으로 설정하여 SQL 서버를 구성해야 합니다.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 SQL Server의 'Advanced Data Security'가 '켬'으로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 '위협 탐지 유형'이 '모두'로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL 관리형 인스턴스의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 '경고 전송 대상'이 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 '이메일 서비스 및 공동 관리자'가 '활성'인지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Azure Active Directory 관리자가 구성되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 SQL Database에서 '데이터 암호화'가 '켬'으로 설정되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 SQL 서버의 TDE 보호기가 BYOK(사용자 고유 키 사용)로 암호화되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- SQL Server TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- SQL 관리형 인스턴스 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Azure KeyVault에 대한 로깅이 '활성'인지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 'OS 디스크'가 암호화되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 '데이터 디스크'가 암호화되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 모든 Virtual Machines에 최신 OS 패치가 적용되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 시스템 업데이트를 머신에 설치해야 합니다.

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 모든 Virtual Machines에 대한 엔드포인트 보호가 설치되어 있는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Azure Kubernetes Services 내에서 RBAC(역할 기반 액세스 제어) 사용

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- \[미리 보기\]: Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Azure App Service에서 웹앱이 모든 HTTP 트래픽을 HTTPS로 리디렉션하는지 확인

이 청사진은 이 CIS 권장 사항에 맞는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능

## <a name="next-steps"></a>다음 단계

CIS Microsoft Azure Foundations 벤치마크 청사진에 대한 제어 매핑을 검토했으면, 다음 문서를 방문하여 청사진에 대해 알아보거나 Azure Portal의 Azure Policy를 방문하여 이니셔티브를 할당합니다.

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations 벤치마크 청사진 - 개요](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기