---
title: Azure Security Center에서 모니터링되는 Azure 정책 정의 | Microsoft Docs
description: Azure Security Center에서 모니터링되는 Azure 정책 정의를 다룹니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 925bf325d128f1757d24d29013f4e27eb788d7e2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180673"
---
# <a name="azure-security-policies-monitored-by-azure-security-center"></a>Azure Security Center에서 모니터링되는 Azure 보안 정책
이 문서에서는 Security Center에서 모니터링할 수 있는 Azure Policy 정의 목록을 제공합니다. 보안 정책에 대한 자세한 내용은 [보안 정책 작업](tutorial-security-policy.md)을 참조하세요.

## <a name="available-security-policies"></a>사용 가능한 보안 정책

Security Center에서 모니터링되는 기본 제공 정책을 이해하려면 다음 표를 참조하세요.

| 정책 | 정책에서 수행하는 작업 |
| --- | --- |
|Service Fabric 및 Virtual Machine Scale Sets에서 진단 로그의 감사 사용|인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다.|
|이벤트 허브 네임스페이스의 권한 부여 규칙 감사|이벤트 허브 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞게 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다.|
|이벤트 허브 엔터티 권한 부여 규칙의 존재 감사|최소 권한 액세스를 부여하기 위해 이벤트 허브 엔터티 권한 부여 규칙의 존재를 감사합니다.|
|스토리지 계정에 대한 무제한 네트워크 액세스 감사|스토리지 계정 방화벽 설정에서 무제한 네트워크 액세스를 감사합니다. 또는, 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure Virtual Network에서의 트래픽 또는 공용 인터넷 IP 주소 범위에 액세스 권한을 부여할 수 있습니다.|
|사용자 지정 RBAC 규칙의 사용 감사|오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다.|
|Azure Stream Analytics에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.|
|스토리지 계정에 대한 보안 전송 감사|스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다.|
|SQL Server에 대한 Azure Active Directory 관리자의 프로비전 감사|SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다.|
|Service Bus 네임스페이스의 권한 부여 규칙 감사|Service Bus 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞게 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다.|
|Service Bus에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.|
|Service Fabric에서 EncryptAndSign에 대한 ClusterProtectionLevel 속성의 설정 감사|Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다.| 
|Service Fabric의 클라이언트 인증에 대한 Azure Active Directory의 사용 감사|Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사| 
|검색 서비스에 대해 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|Azure Cache for Redis에 대한 보안 연결의 사용만 감사|SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다.| 
|Logic Apps에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|Key Vault에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.|
|이벤트 허브에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|Azure Data Lake Store에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|Data Lake Analytics에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|클래식 스토리지 계정의 사용 감사|스토리지 계정에 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 키 자격 증명 모음에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다.| 
|클래식 가상 머신의 사용 감사|가상 머신에 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 키 자격 증명 모음에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다.| 
|배치 계정의 메트릭 경고 규칙 구성 감사|필요한 메트릭을 사용할 수 있도록 설정하는 배치 계정의 메트릭 경고 규칙 구성을 감사합니다.| 
|배치 계정의 메트릭 경고 규칙 구성 감사|필요한 메트릭을 사용할 수 있도록 설정하는 배치 계정의 메트릭 경고 규칙 구성을 감사합니다.| 
|배치 계정에서 진단 로그의 사용 감사|로그 사용을 감사하고 최대 1년까지 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|자동화 계정 변수 암호화의 사용 감사|중요한 데이터를 저장할 때 Automation 계정 변수 자산의 암호화를 사용하도록 설정해야 합니다.| 
|App Services에서 진단 로그의 사용 감사|앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다.| 
|투명한 데이터 암호화 상태 감사|SQL 데이터베이스에 대한 투명한 데이터 암호화 상태를 감사합니다.| 
|SQL 감사 서버 수준 감사 설정|서버 수준에서 SQL 감사의 존재를 감사합니다.| 
|[미리 보기]: Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링|암호화되지 않은 SQL Server 또는 데이터베이스는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 감사되지 않은 SQL 데이터베이스 모니터링|SQL 감사가 켜져 있지 않은 SQL Server 및 데이터베이스는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 누락된 시스템 업데이트 모니터링|서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다.| 
|[미리 보기]: 스토리지 계정에 대해 누락된 Blob 암호화 감사|이 정책은 BLOB 암호화 없이 스토리지 계정을 감사합니다. Microsoft.Storage 리소스 종류에만 적용되고 다른 스토리지 공급자에는 적용되지 않습니다. 가능한 네트워크 Just In Time 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 가능한 네트워크 JIT(Just-In-time) 액세스 모니터링|가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 가능한 앱 허용 목록 모니터링|가능한 애플리케이션 허용 목록 구성은 Azure Security Center에서 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 관대한 네트워크 액세스 모니터링|매우 관대한 규칙을 사용하는 네트워크 보안 그룹은 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 OS 취약성 모니터링|구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 누락된 Endpoint Protection 모니터링|Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 암호화되지 않은 VM 디스크 모니터링|디스크 암호화를 사용하도록 설정하지 않은 VM은 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 VM 취약성 모니터링|권장 구성에 따라 Azure Security Center에서 취약성 평가 솔루션이 발견한 취약성과 취약성 평가 솔루션 없이 VM이 발견한 취약성을 모니터링합니다.| 
|[미리 보기]: Azure Security Center에서 보호되지 않은 웹 애플리케이션 모니터링|웹 애플리케이션 방화벽 보호가 없는 웹 애플리케이션은 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 보호되지 않은 네트워크 엔드포인트 모니터링|차세대 방화벽의 보호가 없는 네트워크 엔드포인트는 Azure Security Center에서 권장 사항으로 모니터링됩니다.| 
|[미리 보기]: Azure Security Center에서 SQL 취약성 평가 결과 모니터링|취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다.| 
|[미리 보기]: 구독에 대한 최대 소유자 수 감사|보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다.| 
|[미리 보기]: 구독에 대한 최소 소유자 수 감사|관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다.| 
|[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 소유자 권한이 있는 계정 감사|계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다.| 
|[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 쓰기 권한이 있는 계정 감사|계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다.| 
|[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 읽기 권한이 있는 계정 감사|계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다.| 
|[미리 보기]: 구독에서 소유자 권한이 있는 사용되지 않는 계정 감사|소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다. 사용되지 않는 계정은 로그인이 차단된 계정입니다.| 
|[미리 보기]: 구독에서 사용되지 않는 계정 감사|더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다. 사용되지 않는 계정은 로그인이 차단된 계정입니다.| 
|[미리 보기]: 구독에서 소유자 권한이 있는 외부 계정 감사|모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다.| 
|[미리 보기]: 구독에서 쓰기 권한이 있는 외부 계정 감사|모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.| 
|[미리 보기]: 구독에서 읽기 권한이 있는 외부 계정 감사|모니터링되지 않는 액세스를 방지하려면 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.| 




## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에 대한 디자인 고려 사항을 계획하고 이해하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/): Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대해 자세히 알아보려면 [Azure Policy란?](../azure-policy/azure-policy-introduction.md)을 참조하세요.
