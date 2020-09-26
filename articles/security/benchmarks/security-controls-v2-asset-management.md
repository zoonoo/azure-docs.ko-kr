---
title: Azure Security 벤치 마크 V2-자산 관리
description: Azure Security 벤치 마크 V2 자산 관리
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318027"
---
# <a name="security-control-v2-asset-management"></a>보안 제어 V2: 자산 관리

자산 관리는 Azure 리소스에 대 한 보안 표시 및 관리를 보장 하는 컨트롤을 포함 합니다. 여기에는 보안 담당자, 자산 인벤토리에 대 한 보안 액세스, 서비스 및 리소스에 대 한 승인 관리 (인벤토리, 추적, 수정)에 대 한 권장 사항이 포함 됩니다.

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>오전-1: 보안 팀이 자산에 대 한 위험을 볼 권한이 있는지 확인

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-1 | 1.1, 1.2 | CM-8, PM-5 |

Azure Security Center를 사용 하 여 보안 위험을 모니터링할 수 있도록 Azure 테 넌 트 및 구독에서 보안 팀에 보안 읽기 권한자 권한이 부여 되었는지 확인 합니다. 

보안 팀의 책임이 구성 된 방식에 따라 보안 위험에 대 한 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임입니다. 즉, 보안 정보 및 위험은 항상 조직 내에서 중앙에서 집계 되어야 합니다. 

보안 읽기 권한자 권한은 전체 테 넌 트 (루트 관리 그룹)에 광범위 하 게 적용 하거나 관리 그룹 또는 특정 구독으로 범위를 지정할 수 있습니다. 

참고: 작업 및 서비스에 대 한 가시성을 확보 하려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../../governance/management-groups/overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>오전-2: 보안 팀에 asset inventory and metadata에 대 한 액세스 권한이 있는지 확인

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-2 | 1.1, 1.2, 1.4, 1.5, 9.1, 12.1 | CM-8, PM-5 |

보안 팀이 Azure에서 지속적으로 업데이트 되는 자산의 인벤토리에 액세스할 수 있는지 확인 합니다. 보안 팀은 조직의 잠재적인 노출을 새로운 위험에 대해 평가 하 고 지속적으로 향상 된 보안 기능에 대 한 입력으로이 인벤토리를 필요로 하는 경우가 많습니다. 

Azure Security Center inventory 기능 및 Azure 리소스 그래프는 Azure 서비스, 응용 프로그램 및 네트워크 리소스를 포함 하 여 구독에 있는 모든 리소스를 쿼리하고 검색할 수 있습니다.  

Azure의 다른 메타 데이터 (이름, 설명 및 범주)를 사용 하 여 조직의 분류에 따라 자산을 논리적으로 구성 합니다.  

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center asset inventory management](../../security-center/asset-inventory.md)

- [자산에 태그를 지정 하는 방법에 대 한 자세한 내용은 리소스 명명 및 태그 지정 결정 가이드를 참조 하세요.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>오전 3: 승인 된 Azure 서비스만 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-3 | 2.3, 2.4 | CM-7, CM-8 |

Azure Policy를 사용 하 여 사용자 환경에서 사용자가 프로 비전 할 수 있는 서비스를 감사 하 고 제한할 수 있습니다. Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다.  또한 Azure Monitor를 사용 하 여 승인 되지 않은 서비스가 검색 될 때 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy 구성 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-4 | 2.3, 2.4, 2.5 | CM-7, CM-8, CM-10, CM-11 |

잠재적으로 큰 영향을 주는 수정 작업을 위해 자산 수명 주기 관리 프로세스를 해결 하는 보안 정책을 설정 하거나 업데이트 합니다. 이러한 수정 사항에는 id 공급자 및 액세스, 데이터 민감도, 네트워크 구성 및 관리 권한 할당에 대 한 변경 내용이 포함 됩니다.

더 이상 필요 하지 않은 경우 Azure 리소스를 제거 합니다.

- [Azure 리소스 그룹 및 리소스 삭제](../../azure-resource-manager/management/delete-resource-group.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>오전 5: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-5 | 2.9 | AC-3 |

Azure AD 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: 계산 리소스에서 승인 된 응용 프로그램만 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| 오전-6 | 2.6, 2.7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

권한 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어는 Azure Virtual Machines에서 실행 되지 않도록 차단 합니다.

ASC (Azure Security Center) 적응 응용 프로그램 컨트롤을 사용 하 여 응용 프로그램 허용 목록을 검색 하 고 생성 합니다. ASC 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단할 수도 있습니다.

Azure Automation 변경 내용 추적 및 인벤토리를 사용 하 여 Windows 및 Linux Vm에서 인벤토리 정보 수집을 자동화할 수 있습니다. Azure Portal에서 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간을 사용할 수 있습니다. 소프트웨어 설치 날짜 및 기타 정보를 가져오려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 보냅니다.

스크립트의 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스에서 스크립트를 실행 하는 사용자의 기능을 제한할 수 있습니다. 

타사 솔루션을 사용 하 여 승인 되지 않은 소프트웨어를 검색 하 고 식별할 수도 있습니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../../security-center/security-center-adaptive-application.md)

- [Azure Automation 변경 내용 추적 및 인벤토리 이해](../../automation/change-tracking.md)

- [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

