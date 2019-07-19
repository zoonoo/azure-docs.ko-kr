---
title: Azure 서비스에 대 한 보안 특성
description: Azure Service Fabric을 평가하기 위한 일반적인 보안 특성에 대한 검사 목록입니다.
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: d45e28175412b574432adb59cf700568c9a7fb39
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304260"
---
# <a name="security-attributes-for-azure-services"></a>Azure 서비스에 대 한 보안 특성

이 문서는 선택한 Azure 서비스에 대 한 일반적인 보안 특성을 수집 합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 인증서, 키 및 비밀 이름이 지정 된 값과 같은 중요 한 데이터는 서비스 인스턴스 키 당 서비스 관리로 암호화 됩니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | [Express 경로](../expressroute/index.yml) 및 VNet 암호화는 [Azure 네트워킹](../virtual-network/index.yml)에서 제공 됩니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 | 모든 암호화 키는 서비스 인스턴스당 서비스 관리 됩니다. |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | 관리 평면 호출은 TLS를 통해 [Azure Resource Manager](../azure-resource-manager/index.yml) 를 통해 수행 됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다.  데이터 평면 호출은 TLS를 사용 하 고 지원 되는 인증 메커니즘 (예: 클라이언트 인증서 또는 JWT) 중 하나를 사용 하 여 보안을 설정할 수 있습니다.
 |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 | NSG (네트워킹 보안 그룹) 및 Azure 애플리케이션 게이트웨이 (또는 다른 소프트웨어 어플라이언스)를 각각 사용 합니다. |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | |
| Authorization| 예 | |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [활동 로그 Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| 데이터 평면 로깅 및 감사| 예 | [진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md) 및 (선택 사항) [정보](../azure-monitor/app/app-insights-overview.md)를 Azure Monitor Azure 애플리케이션 합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) 사용 |

### <a name="vulnerability-scans-false-positives"></a>취약성 검색 거짓 긍정

이 섹션에서는 Azure API Management에 영향을 주지 않는 일반적인 취약성에 대해 설명 합니다.

| 취약점               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed는 일부 F5 제품에서 발견 된 TLS SessionTicket 확장의 구현에 대 한 취약성입니다. 초기화 되지 않은 메모리에서 최대 31 바이트의 데이터를 누출 ("bleeding") 할 수 있습니다. 이는 TLS 스택이 클라이언트에서 전달 된 세션 ID를 32 비트 길이의 데이터로 전달 하기 때문에 발생 합니다. |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | 웹 사이트 파일 콘텐츠는 미사용 콘텐츠를 자동으로 암호화 하는 Azure Storage에 저장 됩니다. [휴지 상태의 데이터에 대 한 Azure Storage 암호화를](../storage/common/storage-service-encryption.md)참조 하세요.<br><br>고객 제공 암호는 미사용 암호화 됩니다. 비밀은 App Service 구성 데이터베이스에 저장 되어 있는 동안 암호화 되지 않습니다.<br><br>필요에 따라 로컬에 연결 된 디스크를 웹 사이트 (D:\local 및% TMP%)에서 임시 저장소로 사용할 수 있습니다. 로컬에 연결 된 디스크는 미사용 상태로 암호화 되지 않습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 고객은 인바운드 트래픽에 대해 HTTPS를 요구 하 고 사용 하도록 웹 사이트를 구성할 수 있습니다. [AZURE APP SERVICE HTTPS만을 만드는 방법에 대해서](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)는 블로그 게시물을 참조 하세요. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | 고객은 Key Vault에 응용 프로그램 암호를 저장 하 고 런타임에 검색 하도록 선택할 수 있습니다. [App Service 및 Azure Functions에 대 한 Key Vault 참조 사용 (미리 보기)](../app-service/app-service-key-vault-references.md)을 참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | App Service를 구성 하는 관리 호출은 HTTPS를 통한 [Azure Resource Manager](../azure-resource-manager/index.yml) 호출을 통해 수행 됩니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | 현재 App Service 미리 보기에서 사용할 수 있습니다. [Azure App Service 액세스 제한](../app-service/app-service-ip-restrictions.md)을 참조 하세요. |
| VNet 삽입 지원| 예 | App Service 환경은 고객의 가상 네트워크에 주입 된 단일 고객 전용 App Service의 개인 구현입니다. [App Service 환경 소개를](../app-service/environment/intro.md)참조 하세요. |
| 네트워크 격리 및 방화벽 지원| 예 | 공용 다중 테 넌 트 변형 App Service의 경우 고객은 네트워크 Acl (IP 제한)을 구성 하 여 허용 되는 인바운드 트래픽을 잠글 수 있습니다.  [Azure App Service 액세스 제한](../app-service/app-service-ip-restrictions.md)을 참조 하세요.  App Service 환경은 가상 네트워크에 직접 배포 되므로 NSGs를 사용 하 여 보안을 유지할 수 있습니다. |
| 강제 터널링 지원| 예 | 강제 터널링이 구성 된 고객의 가상 네트워크에 App Service 환경을 배포할 수 있습니다. 고객은 [강제 터널링을 사용 하 여 App Service Environment 구성](../app-service/environment/forced-tunnel-support.md)의 지침을 따라야 합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | App Service는 Application Insights (전체 .NET Framework, .NET Core, Java 및 NODE.JS)를 지 원하는 언어에 대 한 Application Insights와 통합 됩니다.  [Azure App Service 성능 모니터링](../azure-monitor/app/azure-web-apps.md)을 참조 하세요. 또한 App Service는 응용 프로그램 메트릭을 Azure Monitor 보냅니다. [Azure App Service에서 앱 모니터링을](../app-service/web-sites-monitor.md)참조 하세요. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 고객은 [Azure Active Directory (AZURE AD)](../active-directory/index.yml) 및 기타 OAuth 호환 id 공급자와 자동으로 통합 되는 App Service에서 응용 프로그램을 빌드할 수 있습니다. [Azure App Service의 인증 및 권한 부여](../app-service/overview-authentication-authorization.md)를 참조 하세요. App Service 자산에 대 한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 된 주체 및 Azure Resource Manager RBAC 역할의 조합에 의해 제어 됩니다. |
| Authorization| 예 | App Service 자산에 대 한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 된 주체 및 Azure Resource Manager RBAC 역할의 조합에 의해 제어 됩니다.  |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | App Service 개체에서 수행 되는 모든 관리 작업은 [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해 수행 됩니다. 이러한 작업의 기록 로그는 포털 및 CLI를 통해 사용할 수 있습니다. [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftweb) 및 [az monitor activity-log](/cli/azure/monitor/activity-log)를 참조 하세요. |
| 데이터 평면 로깅 및 감사 | 아니요 | App Service에 대 한 데이터 평면은 고객의 배포 된 웹 사이트 콘텐츠를 포함 하는 원격 파일 공유입니다.  원격 파일 공유에 대 한 감사는 없습니다. |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 관리 작업의 경우 App Service 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. 런타임 작업의 경우 고객은 배포 슬롯 App Service 기능을 사용 하 여 응용 프로그램의 여러 다른 라이브 버전을 유지할 수 있습니다. | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure 리소스 관리자](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | HTTPS/TLS. |
| 암호화 키 처리 (CMK, BYOK 등)| N/A | Azure Resource Manager는 고객 콘텐츠를 저장 하지 않고 데이터를 제어 합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | |
| API 호출 암호화| 예 | |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 아니요 |  |
| 강제 터널링 지원| 아니요 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 아니요 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | [Azure Active Directory](/azure/active-directory) 기반.|
| Authorization| 예 | |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그는 리소스에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)을 노출 합니다. 작업 [로그 보기를 참조 하 여 리소스에 대 한 작업을 감사](../azure-resource-manager/resource-group-audit.md)합니다. |
| 데이터 평면 로깅 및 감사| 해당 사항 없음 | |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능)| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 아니요 | HTTPS를 사용합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 |  |
| 열 수준 암호화 (Azure Data Services)| 아니요 |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 |  |
| VNet 삽입 지원| 아니요 |  |
| 네트워크 격리 및 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링 지원| 아니요 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 자세한 내용은 [Log Analytics를 사용 하 여 Azure Backup 보호 된 워크 로드 모니터링](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 을 참조 하세요. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| Authorization| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. 자세한 내용은 [역할 기반 Access Control를 사용 하 여 Azure Backup 복구 지점의 관리를](/azure/backup/backup-rbac-rs-vault) 참조 하세요. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아니요 | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[Cosmos DB](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | 모든 Cosmos DB 데이터베이스와 백업은 기본적으로 암호화 됩니다. [Azure Cosmos DB의 데이터 암호화를](../cosmos-db/database-encryption-at-rest.md)참조 하세요. 고객 관리 키를 사용 하는 서버 쪽 암호화는 지원 되지 않습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, Vnet 암호화 및 VNet-VNet 암호화)| 예 | 전송 시 모든 Azure Cosmos DB 데이터가 암호화 됩니다. |
| 암호화 키 처리(CMK, BYOK 등)| 아니요 |  |
| 열 수준 암호화(Azure Data Services)| 예 | 테이블 API Premium 에서만. 모든 Api에서이 기능을 지 원하는 것은 아닙니다. Azure Cosmos DB [소개를 참조 하세요. Table API](../cosmos-db/table-introduction.md). |
| API 호출 암호화| 예 | Azure Cosmos DB에 대 한 모든 연결은 HTTPS를 지원 합니다. Azure Cosmos DB는 TLS 1.2 연결도 지원 하지만 아직 적용 되지 않습니다. 고객이 더 낮은 수준의 TLS를 종료 하는 경우 Cosmos DB에 연결 하도록 보장할 수 있습니다.  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| 예 | VNet 서비스 끝점을 사용 하 여 가상 네트워크 (VNet)의 특정 서브넷 에서만 액세스할 수 있도록 Azure Cosmos DB 계정을 구성할 수 있습니다. 또한 VNet 액세스와 방화벽 규칙을 결합할 수 있습니다.  [가상 네트워크에서 Azure Cosmos DB 액세스를](../cosmos-db/vnet-service-endpoint.md)참조 하세요. |
| 네트워크 격리 및 방화벽 지원| 예 | 방화벽 지원을 통해 승인 된 IP 주소 집합, IP 주소 및/또는 클라우드 서비스 에서만 액세스할 수 있도록 Azure Cosmos 계정을 구성할 수 있습니다. [Azure Cosmos DB에서 IP 방화벽 구성을](../cosmos-db/how-to-configure-firewall.md)참조 하세요.|
| 강제 터널링 지원 | 예 | 가상 컴퓨터가 있는 VNET의 클라이언트 쪽에서 구성할 수 있습니다.   |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Azure Cosmos DB로 전송 되는 모든 요청이 로깅됩니다. Azure [모니터링](../azure-monitor/overview.md), azure 메트릭, Azure 감사 로깅이 지원 됩니다.  데이터 평면 요청, 쿼리 런타임 통계, 쿼리 텍스트, MongoDB 요청에 해당 하는 정보를 기록할 수 있습니다. 경고를 설정할 수도 있습니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 예, 데이터베이스 계정 수준에서 데이터 평면 수준에서 Cosmos DB는 리소스 토큰과 키 액세스를 사용 합니다. |
| Authorization| 예 | 마스터 키 (기본 및 보조) 및 리소스 토큰을 사용 하는 Azure Cosmos 계정에서 지원 됩니다. 마스터 키를 사용 하 여 데이터에 대 한 읽기/쓰기 또는 읽기 전용 액세스 권한을 얻을 수 있습니다. 리소스 토큰은 문서 및 컨테이너와 같은 리소스에 대 한 제한 된 시간 액세스를 허용 합니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | 방화벽, Vnet, 키 액세스, IAM 등의 계정 수준 작업에 대 한 Azure 활동 로그입니다. |
| 데이터 평면 로깅 및 감사 | 예 | 컨테이너 만들기, 처리량 프로 비전, 인덱싱 정책 및 문서에 대 한 CRUD 작업과 같은 컨테이너 수준 작업에 대 한 진단 모니터링 로깅입니다. |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 아니요  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| CORS (원본 간 리소스 공유) | 예 | [CORS (원본 간 리소스 공유) 구성을](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md)참조 하세요. |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[Event Hubs](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) |  예 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 |  |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| 아니요 | |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 아니요 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | |
| Authorization|  예 | |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사| 예 |   |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) |  해당 사항 없음 | Express 경로는 고객 데이터를 저장 하지 않습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 아니요 | |
| 암호화 키 처리 (CMK, BYOK 등)| 해당 사항 없음 |  |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A |  |
| vNET 삽입 지원| 해당 사항 없음 | |
| 네트워크 격리 및 방화벽 지원| 예 | 각 고객은 자체 라우팅 도메인에 포함 되 고 자체 VNet으로 터널링 됩니다. |
| 강제 터널링 지원| 해당 사항 없음 | Via Border Gateway Protocol (BGP). |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Express 경로 모니터링, 메트릭 및 경고](../expressroute/expressroute-monitoring-metrics-alerts.md)를 참조 하세요.|

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |
| Authorization|  예 |Microsoft 용 게이트웨이 서비스 계정 (GWM) (컨트롤러) Dev 및 OP에 대 한 JIT (just-in-time) 액세스 |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고| 
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사| 아니요 |   |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 네트워크 리소스 공급자 (NRP)를 통해. |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | 모든 개체가 암호화됩니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | 고객은 해당 Key Vault의 모든 키를 제어 합니다. HSM (하드웨어 보안 모듈) 지원 키가 지정 된 경우 FIPS 수준 2 HSM은 키, 인증서 또는 암호를 보호 합니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 |  |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | VNet (Virtual Network) 서비스 끝점을 사용 합니다. |
| VNet 삽입 지원| 아니요 |  |
| 네트워크 격리 및 방화벽 지원| 예 | VNet 방화벽 규칙을 사용 합니다. |
| 강제 터널링 지원| 아니요 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Log Analytics를 사용합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| Authorization| 예 | Key Vault 액세스 정책을 사용합니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

### <a name="access-controls"></a>액세스 제어

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Load Balancer](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 해당 사항 없음 | |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 해당 사항 없음 | |
| 암호화 키 처리 (CMK, BYOK 등)| 해당 사항 없음 | |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 해당 사항 없음 | |
| VNet 삽입 지원| N/A | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| 해당 사항 없음 |  |
| 강제 터널링 지원| 해당 사항 없음 | |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor logs for Public Basic Load Balancer을](../load-balancer/load-balancer-monitor-log.md)참조 하세요. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| N/A |  |
| Authorization| 해당 사항 없음 |  |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Monitor logs for Public Basic Load Balancer을](../load-balancer/load-balancer-monitor-log.md)참조 하세요. |
| 데이터 평면 로깅 및 감사 | 해당 사항 없음 |  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| N/A |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Service Bus 메시징](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) |  기본적으로 서버 쪽 암호화에 대 한 예입니다. | 고객 관리 키 및 BYOK는 아직 지원 되지 않습니다. 클라이언트 쪽 암호화는 클라이언트의 책임입니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 |   |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | API 호출은 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해 수행 됩니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 (프리미엄 계층만) | VNet 서비스 끝점은 [Service Bus 프리미엄 계층](../service-bus-messaging/service-bus-premium-messaging.md) 에 대해서만 지원 됩니다. |
| VNet 삽입 지원| 아니요 | |
| 네트워크 격리 및 방화벽 지원| 예 (프리미엄 계층만) |  |
| 강제 터널링 지원| 아니요 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor 및 경고를](../service-bus-messaging/service-bus-metrics-azure-monitor.md)통해 지원 됩니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | [Azure Active Directory 관리 서비스 ID](../service-bus-messaging/service-bus-managed-service-identity.md)를 통해 관리 [Service Bus 인증 및 권한 부여를](../service-bus-messaging/service-bus-authentication-and-authorization.md)참조 하세요.|
| Authorization| 예 | [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (미리 보기) 및 SAS 토큰을 통해 권한 부여를 지원 합니다. [Service Bus 인증 및 권한 부여를](../service-bus-messaging/service-bus-authentication-and-authorization.md)참조 하세요. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 작업 로그를 사용할 수 있습니다. [Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md)를 참조 하세요.  |
| 데이터 평면 로깅 및 감사| 아니요 |  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [AZURE RESOURCE MANAGER API](/rest/api/resources/)를 통해 리소스 공급자 버전 관리를 지원 합니다.|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) |  해당 사항 없음 | 릴레이는 웹 소켓 이며 데이터를 유지 하지 않습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 서비스에는 TLS가 필요 합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 | Microsoft TLS 인증서만 사용 합니다.  |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | HTTP. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 아니요 |  |
| 네트워크 격리 및 방화벽 지원| 아니요 |  |
| 강제 터널링 지원| N/A | 릴레이가 TLS 터널  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | SAS를 통해. |
| Authorization|  예 | SAS를 통해. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml). |
| 데이터 평면 로깅 및 감사| 예 | 연결 성공/실패 및 오류 및 기록 됨  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | 고객은 클러스터와 클러스터가 빌드되는 가상 머신 확장 집합을 소유 합니다. 가상 머신 확장 집합에서 Azure disk encryption을 사용 하도록 설정할 수 있습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 고객은 클러스터와 클러스터가 빌드되는 가상 머신 확장 집합을 소유 합니다. 가상 머신 확장 집합에서 Azure disk encryption을 사용 하도록 설정할 수 있습니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 |  |
| API 호출 암호화| 예 | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| VNet 삽입 지원| 예 |  |
| 네트워크 격리 및 방화벽 지원| 예 | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Azure 모니터링 지원 및 타사 지원 사용. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| Authorization| 예 | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 엔드포인트에 대한 직접 호출은 다음 두 역할을 지원합니다. 사용자 및 관리자. 고객은 API를 두 역할 중 하나에 매핑할 수 있습니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| 해당 사항 없음 | 고객은 클러스터를 소유합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../sql-database/sql-database-security-attributes.md)

SQL Database에는 [단일 데이터베이스](../sql-database/sql-database-single-index.yml) 와 [관리 되는 인스턴스가](../sql-database/sql-database-managed-instance.md)모두 포함 됩니다. 다음 항목은 별도로 명시 된 경우를 제외 하 고 두 제품 모두에 적용 됩니다.

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | [Always Encrypted](../sql-database/sql-database-always-encrypted.md)문서에 설명 된 대로 "사용 중인 암호화" 라고 합니다. 서버 쪽 암호화는 [투명 한 데이터 암호화](../sql-database/transparent-data-encryption-azure-sql.md)를 사용 합니다.|
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | HTTPS를 사용합니다. |
| 암호화-키 처리 (예: CMK 또는 BYOK)| 예 | 서비스 관리 및 고객 관리 키 처리가 모두 제공 됩니다. 후자는 [Azure Key Vault](../key-vault/index.yml)를 통해 제공 됩니다. |
| Azure 데이터 서비스에서 제공 하는 열 수준 암호화| 예 | [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| 암호화 된 API 호출| 예 | HTTPS/SSL 사용. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | [단일 데이터베이스](../sql-database/sql-database-single-index.yml) 에만 적용 됩니다. |
| Azure Virtual Network 주입 지원| 예 | [관리 되는 인스턴스에만](../sql-database/sql-database-managed-instance.md) 적용 됩니다. |
| 네트워크 격리 및 방화벽 지원| 예 | 데이터베이스 수준 및 서버 수준 모두에서의 방화벽. 네트워크 격리는 관리 되는 [인스턴스에만](../sql-database/sql-database-managed-instance.md) 해당 됩니다. |
| 강제 터널링 지원| 예 | [Express](../expressroute/index.yml) 경로 VPN을 통해 [관리 되는 인스턴스입니다](../sql-database/sql-database-managed-instance.md) . |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Log Analytics 또는 Application Insights와 같은 Azure 모니터링 지원| 예 | Imperva의 SIEM 솔루션인 SecureSphere는 [SQL 감사](../sql-database/sql-database-auditing.md)를 통해 [Azure Event Hubs](../event-hubs/index.yml) 통합을 통해서도 지원 됩니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Azure AD(Azure Active Directory) |
| Authorization| 예 | 없음 |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 평면 및 관리-평면 로깅 및 감사| 예 | 일부 이벤트에 대해서만 예 |
| 데이터 평면 로깅 및 감사 | 예 | [SQL 감사](../sql-database/sql-database-auditing.md) 를 통해 |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (예: 구성의 버전 관리)| 아니요  | 없음 |

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 예방: 취약성 평가 | 예 | [SQL 취약성 평가 서비스를 참조 하 여 데이터베이스 취약성을 식별할 수 있습니다](../sql-database/sql-vulnerability-assessment.md). |
| 예방: 데이터 검색 및 분류  | 예 | [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 & 분류를](../sql-database/sql-database-data-discovery-and-classification.md)참조 하세요. |
| 검색: 위협 검색 | 예 | [Azure SQL Database에 대 한 Advanced Threat Protection을](../sql-database/sql-database-threat-detection-overview.md)참조 하세요. |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[저장소](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 |  |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화) | 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자는 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | [Azure Key Vault에서 고객이 관리 하는 키를 사용 하 저장소 서비스 암호화를](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| VNet 삽입 지원| 해당 사항 없음 |  |
| 네트워크 격리 및 방화벽 지원| 예 | |
| 강제 터널링 지원| 해당 사항 없음 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | 현재 사용할 수 있는 Azure Monitor 메트릭, 로그 시작 미리 보기 |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Azure Active Directory, 공유 키, 공유 액세스 토큰입니다. |
| Authorization| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통한 인증 지원 |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사 | 예 | 활동 로그 Azure Resource Manager |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그 및 Azure Monitor 로깅 시작 미리 보기  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | Azure Resource Manager Api를 통한 리소스 공급자 버전 관리 지원 |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines 및 Virtual Machine Scale Sets

[Linux vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [Windows vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)


### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | [Azure에서 Linux 가상 머신을 암호화](/azure/virtual-machines/linux/encrypt-disks) 하 고 [Windows VM에서 가상 디스크를 암호화](/azure/virtual-machines/windows/encrypt-disks)하는 방법을 참조 하세요. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | Azure Virtual Machines는 [express](/azure/expressroute) 경로 및 VNET 암호화를 지원 합니다. [Vm의 전송 중 암호화를](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)참조 하세요. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | 고객 관리 키는 지원 되는 Azure 암호화 시나리오입니다. [Azure 암호화 개요](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)를 참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | HTTPS 및 SSL을 통해. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | |
| VNet 삽입 지원| 예 | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| 예 |  |
| 강제 터널링 지원| 예 | [Azure Resource Manager 배포 모델을 사용 하 여 강제 터널링 구성을](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)참조 하세요. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | Azure에서 [Linux 가상 머신 모니터링 및 업데이트](/azure/virtual-machines/linux/tutorial-monitoring) 및 [azure에서 Windows 가상 머신 모니터링 및 업데이트](/azure/virtual-machines/windows/tutorial-monitoring)를 참조 하세요. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 |  |
| Authorization| 예 |  |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 |  |
| 데이터 평면 로깅 및 감사 | 아니요 |  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[VPN Gateway](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 해당 사항 없음 | VPN gateway에서 고객 데이터를 전송 하 고 고객 데이터를 저장 하지 않음 |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | VPN gateway는 Azure VPN 게이트웨이와 고객 온-프레미스 VPN 장치 (S2S) 또는 VPN 클라이언트 (P2S) 간에 고객 패킷을 암호화 합니다. VPN 게이트웨이는 VNet 간 암호화도 지원 합니다. |
| 암호화 키 처리 (CMK, BYOK 등)| 아니요 | 사용자가 지정한 미리 공유한 키는 미사용 시 암호화 됩니다. 하지만 CMK와는 아직 통합 되지 않았습니다. |
| 열 수준 암호화 (Azure Data Services)| 해당 사항 없음 | |
| API 호출 암호화| 예 | [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS를 통해  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| N/A | |
| VNet 삽입 지원| N/A | 을 선택합니다. |
| 네트워크 격리 및 방화벽 지원| 예 | VPN 게이트웨이는 각 고객에 대 한 전용 VM 인스턴스입니다 Virtual Network  |
| 강제 터널링 지원| 예 |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | [Azure Monitor 진단 로그/경고](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 메트릭/경고](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)를 참조 하세요.  |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 서비스 관리 및 Azure VPN gateway 구성에 대 한 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 합니다. |
| Authorization| 예 | [RBAC](../role-based-access-control/overview.md)를 통해 권한 부여를 지원 합니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그를 Azure Resource Manager 합니다. |
| 데이터 평면 로깅 및 감사 | 예 | VPN 연결 로깅 및 감사에 대 한 [진단 로그를 Azure Monitor](../azure-resource-manager/resource-group-audit.md) 합니다. |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 관리 작업의 경우 Azure VPN gateway 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. | 

