---
title: Azure 서비스에 대 한 보안 특성
description: Azure Service Fabric을 평가하기 위한 일반적인 보안 특성에 대한 검사 목록입니다.
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474543"
---
# <a name="security-attributes-for-azure-services"></a>Azure 서비스에 대 한 보안 특성

이 문서는 선택한 Azure 서비스에 대 한 일반적인 보안 특성을 수집합니다. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 (서비스 쪽 암호화에만 해당) | 인증서, 키 및 비밀 이름의 값 같은 중요 한 데이터가 암호로 암호화는 서비스 관리, 서비스 인스턴스 키 당 합니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | [Express 경로](../expressroute/index.yml) VNet 암호화를 제공 하 고 [Azure 네트워킹](../virtual-network/index.yml)합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. | 모든 암호화 키는 서비스 인스턴스당 되며 관리 되는 서비스입니다. |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | 관리 평면 호출을 통해 이루어집니다 [Azure Resource Manager](../azure-resource-manager/index.yml) tls 합니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다.  TLS 및 지원 되는 인증 메커니즘 (예: 클라이언트 인증서 또는 JWT) 중 하나를 사용 하 여 데이터 평면 호출을 보호할 수 있습니다.
 |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 예 | 네트워킹 보안 그룹 (NSG)을 사용 하 고 Azure Application Gateway (또는 다른 소프트웨어 어플라이언스) 각각. |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | |
| 권한 부여| 예 | |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | [Azure Monitor 활동 로그](../azure-monitor/platform/activity-logs-overview.md) |
| 데이터 평면 로깅 및 감사| 예 | [Azure Monitor 진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md) (선택 사항) 및 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 사용 하 여 [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | 예 | 웹 사이트 파일 콘텐츠는 자동으로 미사용 콘텐츠를 암호화 하는 Azure Storage에 저장 됩니다. 참조 [미사용 데이터에 대 한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)합니다.<br><br>고객이 제공한 암호는 미사용 시 암호화 됩니다. 암호는 App Service 구성 데이터베이스에 저장 하는 동안 미사용 암호화 됩니다.<br><br>필요에 따라 로컬로 연결 된 디스크 (D:\local 및 % TMP %) 웹 사이트에서 임시 저장소로 사용할 수 있습니다. 로컬로 연결 된 디스크는 미사용 시 암호화 되지 않습니다. |
| (예: ExpressRoute 암호화 VNet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 예 | 고객은 HTTPS를 사용 하 여 인바운드 트래픽을 위해 필요로 하는 웹 사이트를 구성할 수 있습니다. 블로그 게시물을 참조 하세요 [Azure 앱 서비스 HTTPS만 확인 하는 방법을](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 고객은 응용 프로그램 비밀을 Key Vault에 저장 하 고 런타임에 검색할 수 있습니다. 참조 [Key Vault를 사용 하 여 App Service 및 Azure Functions (미리 보기)에 대 한 참조](../app-service/app-service-key-vault-references.md)합니다.|
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | App Service를 구성 하려면 management 호출을 통해 발생할 [Azure Resource Manager](../azure-resource-manager/index.yml) HTTPS 통해 호출 합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 | App Service에 대 한 미리 보기에서 현재 사용할 수 있습니다. 참조 [Azure App Service 액세스 제한](../app-service/app-service-ip-restrictions.md)합니다. |
| VNet 삽입 지원| 예 | App Service Environment는 App Service 고객의 가상 네트워크에 삽입 하는 단일 고객 전용된의 개인 구현입니다. 참조 [App Service Environment 소개](../app-service/environment/intro.md)합니다. |
| 네트워크 격리 및 Firewalling 지원| 예 | App Service의 공용 다중 테 넌 트 변형에 대 한 고객 네트워크 Acl (IP 제한) 허용 되는 인바운드 트래픽을 잠글 수를 구성할 수 있습니다.  참조 [Azure App Service 액세스 제한](../app-service/app-service-ip-restrictions.md)합니다.  App Service Environment 가상 네트워크에 직접 배포 되 고 따라서 Nsg를 사용 하 여 보호할 수 있습니다. |
| 강제 터널링 지원| 예 | 강제 터널링 구성 되어 있는 고객의 가상 네트워크에 app Service Environment은 배포할 수 있습니다. 고객의 지침에 따라 필요가 [강제 터널링으로 App Service Environment 구성](../app-service/environment/forced-tunnel-support.md)합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Application Insights (전체.NET Framework,.NET Core, Java 및 Node.JS)를 지 원하는 언어에 대 한 Application Insights를 사용 하 여 app Service 통합 됩니다.  참조 [Azure App Service 모니터 성능](../azure-monitor/app/azure-web-apps.md)합니다. App Service는 Azure Monitor로 응용 프로그램 메트릭도를 보냅니다. 참조 [Azure App Service에서 앱 모니터링](../app-service/web-sites-monitor.md)합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 고객은 자동으로 통합 하는 App Service에서 응용 프로그램을 빌드할 수 있습니다 [Azure Active Directory (Azure AD)](../active-directory/index.yml) 다른 OAuth 호환 id 공급자; 뿐만 아니라 참조 [인증 및 권한 부여 Azure App Service](../app-service/overview-authentication-authorization.md)합니다. App Service 자산에 대 한 관리 액세스를 인증 하는 Azure AD 보안 주체 및 Azure Resource Manager RBAC 역할의 조합에 대 한 모든 액세스 제어 됩니다. |
| 권한 부여| 예 | App Service 자산에 대 한 관리 액세스를 인증 하는 Azure AD 보안 주체 및 Azure Resource Manager RBAC 역할의 조합에 대 한 모든 액세스 제어 됩니다.  |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | App Service 개체에서 수행 된 모든 관리 작업을 통해 발생할 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다. 이러한 작업의 기록 로그는 포털 및 CLI;를 통해 사용할 수 있습니다. 참조 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftweb) 하 고 [az monitor 활동 로그](/cli/azure/monitor/activity-log)합니다. |
| 데이터 평면 로깅 및 감사 | 아닙니다. | App Service에 대 한 데이터 평면은 고객 배포 된 웹 사이트 콘텐츠를 포함 하는 원격 파일 공유 합니다.  원격 파일 공유의 감사 안 있습니다. |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 관리 작업에 대 한 App Service 구성의 상태가 시간이 지남에 따라 Azure Resource Manager 템플릿으로 및 버전을 내보낼 수 있습니다. 런타임 작업에 대 한 고객은 App Service 배포 슬롯 기능을 사용 하 여 응용 프로그램의 여러 다른 라이브 버전을 유지할 수 있습니다. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure 리소스 관리자](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | HTTPS/TLS. |
| 암호화 키 처리 (예: CMK, BYOK)| N/A | Azure Resource Manager 고객 콘텐츠 없음만 컨트롤 데이터를 저장합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | |
| API 호출 암호화| 예 | |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. | |
| VNet 삽입 지원| 예 | |
| 네트워크 격리 및 방화벽 지원| 아닙니다. |  |
| 강제 터널링 지원| 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 아닙니다. | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | [Azure Active Directory](/azure/active-directory) 기반 합니다.|
| 권한 부여| 예 | |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 활동 로그에 리소스에서 수행 된 작업 (PUT, POST, DELETE)를 작성 하는 모든 노출 참조 [리소스에 대 한 작업을 감사 하려면 활동 로그 보기](../azure-resource-manager/resource-group-audit.md)합니다. |
| 데이터 평면 로깅 및 감사| N/A | |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 스토리지 계정에 대해 스토리지 서비스 암호화 사용 |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 아닙니다. | HTTPS를 사용합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. |  |
| 열 수준 암호화 (Azure Data Services)| 아닙니다. |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. |  |
| VNet 삽입 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 예 | VM 백업에 대해 강제 터널링이 지원됩니다. VM 내부에서 실행되는 워크로드에는 강제 터널링이 지원되지 않습니다. |
| 강제 터널링 지원| 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Log Analytics는 진단 로그를 통해 지원됩니다. 자세한 내용은 Log Analytics를 사용하여 Azure Backup 보호 워크로드 모니터링(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)을 참조하세요. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | 고객이 생성하고 기본 제공된 RBAC 역할이 사용됩니다. Azure Backup 복구 지점 관리에 관한 액세스 제어를 참조 하세요. (/ azure/backup/백업-rbac-rs-자격 증명 모음)에 대 한 자세한 내용은 합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | Azure Portal에서 모든 고객이 트리거한 작업은 활동 로그에 기록됩니다. |
| 데이터 평면 로깅 및 감사| 아닙니다. | Azure Backup 데이터 평면은 직접 연결할 수 없습니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 모든 개체가 암호화됩니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | 모든 통신은 암호화된 API 호출을 통해 수행됩니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 고객이 Key Vault의 모든 키를 제어합니다. 하드웨어 보안 모듈 (HSM) 지원 키를 지정한 경우에 FIPS 수준 2 HSM 키, 인증서 또는 암호를 보호 합니다. |
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| API 호출 암호화| 예 | HTTPS를 사용합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 | 가상 네트워크 (VNet) 서비스 끝점을 사용합니다. |
| VNet 삽입 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 예 | VNet 방화벽 규칙을 사용합니다. |
| 강제 터널링 지원| 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Log Analytics를 사용합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | Key Vault 액세스 정책을 사용합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |
| 데이터 평면 로깅 및 감사| 예 | Log Analytics를 사용합니다. |

### <a name="access-controls"></a>액세스 제어

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 평면 액세스 컨트롤 | 예 | Azure Resource Manager 역할 기반 Access Control(RBAC) |
| 데이터 평면 액세스 컨트롤(모든 서비스 수준에서) | 예 | Key Vault 액세스 정책 |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus 메시징](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>|  기본적으로 서버 쪽 암호화 미사용 예입니다. | 고객 관리 키 및 BYOK 아직 지원 되지 않습니다. 클라이언트 쪽 암호화는 클라이언트의 책임 |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. |   |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | API 호출을 통해 이루어집니다 [Azure Resource Manager](../azure-resource-manager/index.yml) 및 HTTPS입니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 (프리미엄 계층에만 해당) | VNet 서비스 끝점에 대해 지원 됩니다 [Service Bus 프리미엄 계층](../service-bus-messaging/service-bus-premium-messaging.md) 만 합니다. |
| VNet 삽입 지원| 아닙니다. | |
| 네트워크 격리 및 방화벽 지원| 예 (프리미엄 계층에만 해당) |  |
| 강제 터널링 지원| 아닙니다. |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 통해 지원 [Azure Monitor 및 경고](../service-bus-messaging/service-bus-metrics-azure-monitor.md)합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 통해 관리 되는 [Azure Active Directory 관리 서비스 Id](../service-bus-messaging/service-bus-managed-service-identity.md); 참조 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)합니다.|
| 권한 부여| 예 | 통해 권한 부여를 지 원하는 [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (미리 보기) 및 SAS 토큰을 참조 하세요 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)합니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 작업 로그를 사용할 수 있습니다. 참조 [Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md)합니다.  |
| 데이터 평면 로깅 및 감사| 아닙니다. |  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 통해 리소스 공급자의 버전 관리를 지원 합니다 [Azure Resource Manager API](/rest/api/resources/)합니다.|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>|  N/A | 릴레이 웹 소켓을 이며 데이터는 유지 되지 않습니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | 서비스에서는 TLS 합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 아닙니다. | Microsoft TLS 인증서만 사용합니다.  |
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | HTTPS입니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 아닙니다. |  |
| 네트워크 격리 및 방화벽 지원| 아닙니다. |  |
| 강제 터널링 지원| N/A | 릴레이 TLS 터널  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 통해 SAS입니다. |
| 권한 부여|  예 | 통해 SAS입니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다. |
| 데이터 평면 로깅 및 감사| 예 | 연결 성공 / 실패 및 오류 기록 합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 통해 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다.|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. 가상 머신 확장 집합에서 azure 디스크 암호화를 사용할 수 있습니다. |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 |  |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 고객은 클러스터 및 클러스터가 빌드된 VM(가상 머신) 확장 세트를 소유합니다. 가상 머신 확장 집합에서 azure 디스크 암호화를 사용할 수 있습니다. |
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| API 호출 암호화| 예 | Service Fabric API 호출은 Azure Resource Manager를 통해 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 |  |
| VNet 삽입 지원| 예 |  |
| 네트워크 격리 및 방화벽 지원| 예 | NSG(네트워크 보안 그룹) 사용 |
| 강제 터널링 지원| 예 | Azure 네트워킹은 강제 터널링을 제공합니다. |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Azure 및 타사 지원 모니터링을 사용 합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 인증은 Azure Active Directory를 통해 수행됩니다. |
| 권한 부여| 예 | SFRP를 통한 호출의 IAM(ID 및 액세스 관리). 클러스터 엔드포인트에 대한 직접 호출은 다음 두 역할을 지원합니다. 사용자 및 관리자. 고객은 API를 두 역할 중 하나에 매핑할 수 있습니다. |

### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 모든 제어 평면 작업은 감사 및 승인 프로세스를 통해 실행합니다. |
| 데이터 평면 로깅 및 감사| N/A | 고객은 클러스터를 소유합니다.  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 서비스 구성은 Azure 배포를 사용하여 버전 지정되고 배포됩니다. 코드(애플리케이션 및 런타임)는 Azure 빌드를 사용하여 버전 지정됩니다.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 라고 "암호화 사용", 문서에 설명 된 대로 [Always Encrypted](../sql-database/sql-database-always-encrypted.md)합니다. 서비스 쪽 암호화를 사용 하 여 [투명 한 데이터 암호화](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| 전송 중 암호화:<ul><li>ExpressRoute 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | HTTPS를 사용합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 서비스 관리 및 고객 관리 키 처리에 제공 됩니다 (통해 후자 [Azure Key Vault](../key-vault/index.yml)합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | 통해 [상시 암호화](../sql-database/sql-database-always-encrypted.md)합니다. |
| API 호출 암호화| 예 | HTTPS/SSL을 사용합니다. |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 | 적용 대상 [단일 데이터베이스](../sql-database/sql-database-single-index.yml) 만 합니다. |
| VNet 삽입 지원| 예 | 적용 대상 [관리 되는 인스턴스](../sql-database/sql-database-managed-instance.md) 만 합니다. |
| 네트워크 격리 및 방화벽 지원| 예 | 방화벽에서 두 데이터베이스 및 서버 수준의; 네트워크에 대 한 격리 [관리 되는 인스턴스](../sql-database/sql-database-managed-instance.md) 만 |
| 강제 터널링 지원| 예 | [관리 되는 인스턴스](../sql-database/sql-database-managed-instance.md) 를 통해 [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Imperva (SecureSphere)에서 타사 SIEM 솔루션도 지원 되며 통해 [Azure Event Hubs](../event-hubs/index.yml) 통합을 통해 [SQL audit](../sql-database/sql-database-auditing.md)합니다. |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | Azure Active Directory. |
| 권한 부여| 예 |  |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 일부 이벤트에만 예입니다. |
| 데이터 평면 로깅 및 감사 | 예 | 통해 [SQL audit](../sql-database/sql-database-auditing.md)합니다. |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 아닙니다.  | | 

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 취약성 평가 위한 예방 합니다. | 예 | 참조 [서비스를 사용 하면 데이터베이스 취약성을 식별 하는 SQL Vulnerability Assessment](../sql-database/sql-vulnerability-assessment.md)합니다. |
| 예방: 데이터 검색 및 분류  | 예 | 참조 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 및 분류](../sql-database/sql-database-data-discovery-and-classification.md)합니다. |
| 감지: 위협 감지 | 예 | 참조 [Advanced Threat Protection Azure SQL Database에 대 한](../sql-database/sql-database-threat-detection-overview.md)합니다. |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 |  |
| 전송 중 암호화:<ul><li>기본 경로 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | 표준 HTTPS/TLS 메커니즘을 지원 합니다.  사용자가 서비스에 전송 되기 전에 데이터를 암호화할 수도 있습니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 참조 [Azure Key Vault에서 고객 관리 키를 사용 하 여 Storage 서비스 암호화](../storage/common/storage-service-encryption-customer-managed-keys.md)합니다.|
| 열 수준 암호화 (Azure Data Services)| N/A |  |
| API 호출 암호화| 예 |  |

### <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 |  |
| VNet 삽입 지원| N/A |  |
| 네트워크 격리 및 방화벽 지원| 예 | |
| 강제 터널링 지원| N/A |  |

### <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | 사용 가능한 azure Monitor 메트릭 시작 미리 보기를 이제 로그 |

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | Azure Active Directory: 공유 키, 공유 액세스 토큰. |
| 권한 부여| 예 | RBAC, POSIX Acl 및 SAS 토큰을 통해 권한 부여를 지원 합니다. |


### <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사 | 예 | Azure Resource Manager 활동 로그 |
| 데이터 평면 로깅 및 감사| 예 | 서비스 진단 로그 및 Azure Monitor 로깅 시작 미리 보기  |

### <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | Azure Resource Manager Api를 통해 리소스 공급자 버전 관리를 지원 합니다. |