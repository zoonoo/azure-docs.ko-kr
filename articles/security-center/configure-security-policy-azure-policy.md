---
title: REST API을 사용 하 여 Azure Policy 보안 정책 만들기 및 편집 | Microsoft Docs
description: REST API를 통해 Azure Policy 정책 관리에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522022"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>REST API를 사용 하 여 Azure Policy에서 보안 정책 구성

Azure Policy와 네이티브 통합의 일환으로, Azure Security Center를 사용하면 Azure Policy의 REST API를 활용하여 정책 할당을 생성할 수 있습니다. 다음 지침은 기존 할당에 대한 사용자 지정은 물론 정책 할당 생성 과정을 안내합니다. 

Azure Policy의 중요 개념: 

- **정책 정의** 는 규칙입니다. 

- **이니셔티브** 는 정책 정의 (규칙)의 컬렉션입니다. 

- **할당** 은 특정 범위 (관리 그룹, 구독 등)에 대 한 이니셔티브 또는 정책의 응용 프로그램입니다. 

Security Center에는 모든 보안 정책을 포함하는 기본 제공 이니셔티브가 있습니다. Azure 리소스에 대 한 Security Center의 정책을 평가 하려면 평가 하려는 관리 그룹 또는 구독에 대 한 할당을 만들어야 합니다.

기본 제공 이니셔티브에는 모든 Security Center의 정책이 기본적으로 사용하도록 설정되어 있습니다. 기본 제공 이니셔티브에서 특정 정책을 사용 하지 않도록 선택할 수 있습니다. 예를 들어 **웹 응용 프로그램 방화벽**을 제외한 모든 Security Center 정책을 적용 하려면 정책 효과 매개 변수의 값을 **사용 안 함으로**변경 합니다. 

## <a name="api-examples"></a>API 예제

다음 예제에서 다음 변수를 바꿉니다.

- **{scope}** 정책을 적용 중인 관리 그룹 또는 구독의 이름을 입력 합니다.
- **{policyAssignmentName}** - [관련 정책 할당의 이름](#policy-names)을 입력합니다.
- **{name}** 이름을 입력하거나 정책 변경을 승인한 관리자의 이름을 입력합니다.

이 예제는 구독 또는 관리 그룹에 기본 제공 Security Center 이니셔티브를 할당하는 방법을 보여줍니다.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

이 예제는 다음 정책을 사용하지 않도록 설정하고, 기본 제공 Security Center 이니셔티브를 구독에 할당하는 방법을 보여줍니다. 

- 시스템 업데이트(“systemUpdatesMonitoringEffect”) 

- 보안 구성("systemConfigurationsMonitoringEffect") 

- 엔드포인트 보호("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
이 예제는 할당을 제거하는 방법을 보여줍니다.
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## 정책 이름 참조 <a name="policy-names"></a>

|Security Center의 정책 이름|Azure Policy에 표시되는 정책 이름 |정책 효과 매개 변수 이름|
|----|----|----|
|SQL 암호화 |Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링 |sqlEncryptionMonitoringEffect| 
|SQL 감사 |Azure Security Center에서 감사되지 않은 SQL 데이터베이스 모니터링 |sqlAuditingMonitoringEffect|
|시스템 업데이트 |Azure Security Center에서 누락된 시스템 업데이트 모니터링 |systemUpdatesMonitoringEffect|
|스토리지 암호화 |스토리지 계정에 대해 누락된 Blob 암호화 감사 |storageEncryptionMonitoringEffect|
|JIT 네트워크 액세스 |Azure Security Center에서 가능한 네트워크 JIT (just-in-time) 액세스를 모니터링 합니다. |jitNetworkAccessMonitoringEffect |
|적응 애플리케이션 컨트롤 |Azure Security Center에서 가능한 앱 허용 목록 모니터링 |adaptiveApplicationControlsMonitoringEffect|
|네트워크 보안 그룹 |Azure Security Center에서 관대한 네트워크 액세스 모니터링 |networkSecurityGroupsMonitoringEffect| 
|보안 구성 |Azure Security Center에서 OS 취약성 모니터링 |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Azure Security Center에서 누락된 Endpoint Protection 모니터링 |endpointProtectionMonitoringEffect |
|디스크 암호화 |Azure Security Center에서 암호화되지 않은 VM 디스크 모니터링 |diskEncryptionMonitoringEffect|
|취약점 평가 |Azure Security Center에서 VM 취약성 모니터링 |vulnerabilityAssessmentMonitoringEffect|
|웹 애플리케이션 방화벽 |Azure Security Center에서 보호되지 않은 웹 애플리케이션 모니터링 |webApplicationFirewallMonitoringEffect |
|차세대 방화벽 |Azure Security Center에서 보호되지 않은 네트워크 엔드포인트 모니터링| |


## <a name="next-steps"></a>다음 단계

기타 관련 자료는 다음 문서를 참조 하세요. 

- [사용자 지정 보안 정책](custom-security-policies.md)
- [보안 정책 개요](tutorial-security-policy.md)