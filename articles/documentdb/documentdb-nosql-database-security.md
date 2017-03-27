---
title: "NoSQL 데이터베이스 보안 - Azure DocumentDB | Microsoft Docs"
description: "Azure DocumentDB에서 NoSQL 데이터에 대해 데이터베이스 보호 및 데이터 보안을 어떻게 제공하는지 알아봅니다."
keywords: "nosql 데이터베이스 보안, 정보 보안, 데이터 보안, 데이터베이스 암호화, 데이터베이스 보호, 보안 정책, 보안 테스트"
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ca5f5696e9478d5087a09fff16742f65ea3e5589
ms.lasthandoff: 03/10/2017


---

# <a name="documentdb-nosql-database-security"></a>DocumentDB NoSQL 데이터베이스 보안

 이 문서에서는 NoSQL 데이터베이스 보안 모범 사례 및 데이터베이스 침해를 차단, 검색 및 대응하기 위해 Azure DocumentDB에서 제공하는 주요 기능에 대해 설명합니다.

## <a name="how-do-i-secure-my-nosql-database"></a>내 NoSQL 데이터베이스를 보호하려면 어떻게 해야 하나요? 

데이터 보안은 사용자, 고객 그리고 데이터베이스 공급자 간의 공동 책임입니다. 선택한 데이터베이스 공급자에 따라 담당하는 책임의 양이 달라질 수 있습니다. 온-프레미스 솔루션을 선택한 경우 끝점 보호부터 하드웨어의 물리적 보안까지 모든 항목을 제공해야 하며 이것은 쉬운 작업이 아닙니다. Azure DocumentDB와 같은 PaaS 클라우드 데이터베이스 공급자를 선택한 경우 관여해야 하는 부분이 상당히 줄어듭니다. 다음 이미지는 Microsoft의 [클라우드 컴퓨팅에 대한 공유 책임](https://aka.ms/sharedresponsibility) 백서를 인용한 것으로 Azure DocumentDB와 같은 PaaS 공급자를 사용할 경우 사용자가 작업할 부분이 어떻게 줄어드는지를 보여 줍니다.

![고객 및 데이터베이스 공급자 책임](./media/documentdb-nosql-database-security/nosql-database-security-responsibilities.png)

위의 다이어그램은 대략적인 클라우드 보안 구성 요소를 보여주지만 NoSQL 데이터베이스 솔루션에 대해 특히 고려해야 하는 항목은 무엇인가요? 또한 솔루션을 서로 어떻게 비교해볼 수 있나요? 

NoSQL 데이터베이스 시스템을 비교하는 데는 다음 요구 사항 검사 목록을 사용하는 것이 좋습니다.

- 네트워크 보안 및 방화벽 설정
- 사용자 인증 및 세분화된 사용자 제어
- 지역별 오류에 대해 전역으로 데이터를 복제하는 기능
- 데이터 센터 간에 장애 조치(failover)를 수행하는 기능
- 데이터 센터 내에서 로컬 데이터 복제
- 자동 데이터 백업
- 백업에서 삭제된 데이터 복원
- 중요한 데이터 보호 및 격리
- 공격 모니터링
- 공격에 대응
- 지오-펜스 데이터가 데이터 관리 제한을 준수하는 기능
- 보호된 데이터 센터에서 서버의 물리적 보호

명백해 보일지 모르지만, 최근의 [대규모 데이터베이스 침해](http://thehackernews.com/2017/01/mongodb-database-security.html)는 다음과 같은 요구 사항의 간단하지만 민감한 중요성을 상기시켜 줍니다.
- 최신 상태로 유지되는 패치된 서버
- HTTPS(기본값)/SSL 암호화
- 강력한 암호를 사용하는 관리 계정

## <a name="how-does-azure-documentdb-secure-my-database"></a>Azure DocumentDB는 데이터베이스를 어떻게 보호하나요?

이전 목록을 통해 Azure DocumentDB에서 제공하는 보안 요구 사항이 얼마나 되는지 다시 확인해 보겠습니다. 하나씩 모두 살펴봅니다.

구체적으로 자세히 살펴보겠습니다.

|보안 요구 사항|DocumentDB의 보안 접근 방식|
|---|---|---|
|네트워크 보안|IP 방화벽을 사용하는 것은 NoSQL 데이터베이스를 보호하기 위한 첫 번째 보호 계층입니다. DocumentDB는 인바운드 방화벽 지원에 대한 정책 중심 IP 기반 액세스 제어를 지원합니다. IP 기반 액세스 제어는 기존의 데이터베이스 시스템에 사용된 방화벽과 유사하지만 DocumentDB 데이터베이스 계정을 승인된 컴퓨터 집합 또는 클라우드 서비스에서만 액세스할 수 있도록 확장되었습니다. <br><br>DocumentDB를 통해 특정 IP 주소(168.61.48.0), IP 범위 (168.61.48.0/8), IP와 범위 조합을 사용하도록 할 수 있습니다. <br><br>이 허용된 목록 이외의 컴퓨터에서 보내는 모든 요청을 DocumentDB에서 차단합니다. 승인된 컴퓨터 및 클라우드 서비스에서 보내는 요청은 리소스에 대한 액세스 제어를 부여 받기 위해 인증 과정을 완료해야 합니다.<br><br>[DocumentDB 방화벽 지원](documentdb-firewall-support.md)에 대한 자세한 정보|
|권한 부여|DocumentDB는 권한 부여를 위해 해시 기반 메시지 인증 코드(HMAC)를 사용합니다. <br><br>각 요청은 비밀 계정 키를 사용하여 해시되고 이후 base-64 인코딩된 해시는 DocumentDB에 대한 각 호출과 함께 전송됩니다. 요청에 대한 유효성을 검사하려면 DocumentDB 서비스는 올바른 비밀 키와 속성을 사용하여 해시를 생성한 후 요청에 있는 값과 비교합니다. 두 값이 일치하면 작업이 성공적으로 인증되어 요청이 처리되고 그렇지 않은 경우 권한 부여 오류로 요청이 거부됩니다.<br><br>문서와 같은 리소스에 세분화된 액세스를 허용하는 [마스터 키](documentdb-secure-access-to-data.md#master-keys) 또는 [리소스 토큰](documentdb-secure-access-to-data.md#resource-tokens)을 사용할 수 있습니다.<br><br>[DocumentDB 리소스에 대한 액세스 보호](documentdb-secure-access-to-data.md)에 대한 자세한 정보|
|사용자 및 사용 권한|계정에 [마스터 키](#master-key)를 사용하여 데이터베이스당 사용자 리소스 및 권한 리소스를 만들 수 있습니다. [리소스 토큰](#resource-token)은 데이터베이스에 있는 권한과 연결되며 사용자가 데이터베이스에 있는 응용 프로그램 리소스에 대한 액세스 권한(읽기-쓰기, 읽기 전용 또는 액세스 권한 없음)을 보유하는지 확인합니다. 응용 프로그램 리소스에는 컬렉션, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF가 포함됩니다. 그리고 인증 중에는 리소스 토큰이 사용되어 리소스에 대한 액세스를 제공 또는 거부합니다.<br><br>[DocumentDB 리소스에 대한 액세스 보호](documentdb-secure-access-to-data.md)에 대한 자세한 정보|
|Active Directory 통합(RBAC)| Azure Portal에서 액세스 제어(IAM)를 사용하여 데이터베이스 계정에 대한 액세스도 제공할 수 있습니다. IAM은 역할 기반 액세스 제어를 제공하며 Active Directory와 통합됩니다. 다음 이미지처럼 개인 및 그룹에 대해 기본 제공 역할이나 사용자 지정 역할을 사용할 수 있습니다.<br><br>![Azure Portal에서 액세스 제어(IAM) - NoSQL 데이터베이스 보안 설명](./media/documentdb-nosql-database-security/nosql-database-security-identity-access-management-iam-rbac.png)|
|글로벌 복제|DocumentDB는 단추 클릭만으로 Azure 전 세계 데이터 센터 어디로나 데이터를 복제할 수 있는 턴키 전역 분산을 제공합니다. 글로벌 복제를 통해 전역적으로 크기를 조정하고 전 세계 데이터에 짧은 대기 시간으로 액세스할 수 있습니다.<br><br>보안 컨텍스트에서 글로벌 복제는 지역별 오류에 대해 데이터 보호를 보장해줍니다.<br><br>[데이터를 글로벌 배포](documentdb-distribute-data-globally.md)에 대한 자세한 정보|
|지역별 장애 조치|데이터를 둘 이상의 데이터 센터에 복제한 경우 DocumentDB는 자동으로 롤오버되고 사용자 작업을 통해 지역 데이터 센터가 오프라인으로 전환되어야 합니다. 데이터가 복제된 지역을 사용하여 장애 조치 지역의 우선 순위가 지정된 목록을 만들 수 있습니다. <br><br>[Azure DocumentDB의 지역별 장애 조치](documentdb-regional-failovers.md)에 대한 자세한 정보|
|로컬 복제|단일 데이터 센터 내에서도 DocumentDB는 원하는 [일관성 수준](documentdb-consistency-levels.md)을 부여하는 고가용성을 위해 데이터를 자동으로 복제합니다. 이렇게 하면  [99.99% 가동 시간 가용성 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/)가 보장되고 다른 NoSQL 데이터베이스 서비스가 제공할 수 없는 재정적 보장이 함께 제공됩니다.|
|자동 온라인 백업|DocumentDB 데이터베이스는 정기적으로 백업되며 georedundant 저장소에 저장됩니다. <br><br>[DocumentDB로 자동 온라인 백업 및 복원](documentdb-online-backup-and-restore.md)에 대한 자세한 정보|
|삭제된 데이터 복원|자동 온라인 백업을 사용하여 실수로 삭제한 데이터를 이벤트 후 최대&30;일 이내에 복구할 수 있습니다. <br><br>[DocumentDB로 자동 온라인 백업 및 복원](documentdb-online-backup-and-restore.md)에 대한 자세한 정보|
|중요한 데이터 보호 및 격리|PII 및 기타 기밀 데이터를 특정 컬렉션 및 읽기-쓰기로 격리할 수 있으며 읽기 전용 액세스를 특정 사용자로 제한할 수 있습니다.|
|공격 모니터|감사 로깅 및 활동 로그를 사용하여 계정에서 정상 및 비정상적인 활동을 모니터링할 수 있습니다. 리소스에 대해 어떤 작업이 누구에 의해 수행되었는지, 작업 상태 등을 확인할 수 있습니다.<br><br>![Azure DocumentDB에 대한 활동 로그](./media/documentdb-nosql-database-security/nosql-database-security-application-logging.png)|
|공격에 대응|잠재적인 공격을 보고하기 위해 Azure 지원에 연락한 경우 5단계 인시던트 대응 프로세스가 시작됩니다. 5단계 프로세스의 목표는 문제가 검색되어 조사가 시작된 후 정상적인 서비스 보안 및 작업을 가능한 신속히 복원하는 것입니다.<br><br>[클라우드에서 Microsoft Azure의 보안 대응](https://aka.ms/securityresponsepaper)에 대한 자세한 정보|
|지오-펜싱|DocumentDB는 독립적인 지역(예: 독일, 중국, US Gov)에 대해 데이터 거버넌스 및 준수를 보장합니다.|
|보호된 기능|DocumentDB의 데이터는 Azure의 보호된 데이터 센터에서 SSD에 저장됩니다.<br><br>[Microsoft 글로벌 데이터 센터](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)에 대한 자세한 정보|
|HTTPS/SSL/TLS 암호화|모든 클라이언트-서비스 DocumentDB 상호 작용에는 SSL/TLS 1.2가 적용됩니다. 또한 모든 데이터 센터 내부 및 데이터 센터 간 복제에는 SSL/TLS 1.2가 적용됩니다.|
|패치된 서버|관리되는 NoSQL 데이터베이스처럼 DocumentDB는 서버를 관리 및 패치 적용할 필요가 없이 자동으로 처리해 줍니다.|
|강력한 암호를 사용하는 관리 계정|이 요구 사항은 당연하게 여겨질 수 있지만, 일부 경쟁 업체와 달리 DocumentDB에서는 관리 계정에 반드시 암호가 있어야 합니다.<br><br> SSL 및 HMAC 비밀 기반 인증을 통한 보안이 기본적으로 반영됩니다.|
|보안 및 데이터 보호 인증서|DocumentDB에는 [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [EUMC(European Model Clauses)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) 및 [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) 인증서가 있습니다. 기타 인증서는 현재 작업 중입니다.|

## <a name="next-steps"></a>다음 단계

마스터 키 및 리소스 토큰에 대한 자세한 내용은 [DocumentDB 데이터에 대한 액세스 보호](documentdb-secure-access-to-data.md)를 참조하세요.

Microsoft 인증서에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/en-us/support/trust-center/)를 참조하세요.
