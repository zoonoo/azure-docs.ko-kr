---
title: Azure App Service에 대 한 보안 특성
description: Azure App Service를 평가 하는 것에 대 한 보안 특성에 대 한 검사 목록
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475086"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service에 대 한 보안 특성

이 문서에서는 Azure App Service에 기본 제공 되는 일반적인 보안 특성을 설명 합니다.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | 예 | 웹 사이트 파일 콘텐츠는 자동으로 미사용 콘텐츠를 암호화 하는 Azure Storage에 저장 됩니다. 참조 [미사용 데이터에 대 한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)합니다.<br><br>고객이 제공한 암호는 미사용 시 암호화 됩니다. 암호는 App Service 구성 데이터베이스에 저장 하는 동안 미사용 암호화 됩니다.<br><br>필요에 따라 로컬로 연결 된 디스크 (D:\local 및 % TMP %) 웹 사이트에서 임시 저장소로 사용할 수 있습니다. 로컬로 연결 된 디스크는 미사용 시 암호화 되지 않습니다. |
| (예: ExpressRoute 암호화 VNet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 예 | 고객은 HTTPS를 사용 하 여 인바운드 트래픽을 위해 필요로 하는 웹 사이트를 구성할 수 있습니다. 블로그 게시물을 참조 하세요 [Azure 앱 서비스 HTTPS만 확인 하는 방법을](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 고객은 응용 프로그램 비밀을 Key Vault에 저장 하 고 런타임에 검색할 수 있습니다. 참조 [Key Vault를 사용 하 여 App Service 및 Azure Functions (미리 보기)에 대 한 참조](app-service-key-vault-references.md)합니다.|
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | App Service를 구성 하려면 management 호출을 통해 발생할 [Azure Resource Manager](../azure-resource-manager/index.yml) HTTPS 통해 호출 합니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 | App Service에 대 한 미리 보기에서 현재 사용할 수 있습니다. 참조 [Azure App Service 액세스 제한](app-service-ip-restrictions.md)합니다. |
| VNet 삽입 지원| 예 | App Service Environment는 App Service 고객의 가상 네트워크에 삽입 하는 단일 고객 전용된의 개인 구현입니다. 참조 [App Service Environment 소개](environment/intro.md)합니다. |
| 네트워크 격리 및 Firewalling 지원| 예 | App Service의 공용 다중 테 넌 트 변형에 대 한 고객 네트워크 Acl (IP 제한) 허용 되는 인바운드 트래픽을 잠글 수를 구성할 수 있습니다.  참조 [Azure App Service 액세스 제한](app-service-ip-restrictions.md)합니다.  App Service Environment 가상 네트워크에 직접 배포 되 고 따라서 Nsg를 사용 하 여 보호할 수 있습니다. |
| 강제 터널링 지원| 예 | 강제 터널링 구성 되어 있는 고객의 가상 네트워크에 app Service Environment은 배포할 수 있습니다. 고객의 지침에 따라 필요가 [강제 터널링으로 App Service Environment 구성](environment/forced-tunnel-support.md)합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Application Insights (전체.NET Framework,.NET Core, Java 및 Node.JS)를 지 원하는 언어에 대 한 Application Insights를 사용 하 여 app Service 통합 됩니다.  참조 [Azure App Service 모니터 성능](../azure-monitor/app/azure-web-apps.md)합니다. App Service는 Azure Monitor로 응용 프로그램 메트릭도를 보냅니다. 참조 [Azure App Service에서 앱 모니터링](web-sites-monitor.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 고객은 자동으로 통합 하는 App Service에서 응용 프로그램을 빌드할 수 있습니다 [Azure Active Directory (Azure AD)](../active-directory/index.yml) 다른 OAuth 호환 id 공급자; 뿐만 아니라 참조 [인증 및 권한 부여 Azure App Service](overview-authentication-authorization.md)합니다. App Service 자산에 대 한 관리 액세스를 인증 하는 Azure AD 보안 주체 및 Azure Resource Manager RBAC 역할의 조합에 대 한 모든 액세스 제어 됩니다. |
| 권한 부여| 예 | App Service 자산에 대 한 관리 액세스를 인증 하는 Azure AD 보안 주체 및 Azure Resource Manager RBAC 역할의 조합에 대 한 모든 액세스 제어 됩니다.  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | App Service 개체에서 수행 된 모든 관리 작업을 통해 발생할 [Azure Resource Manager](../azure-resource-manager/index.yml)합니다. 이러한 작업의 기록 로그는 포털 및 CLI;를 통해 사용할 수 있습니다. 참조 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftweb) 하 고 [az monitor 활동 로그](/cli/azure/monitor/activity-log)합니다. |
| 데이터 평면 로깅 및 감사 | 아닙니다. | App Service에 대 한 데이터 평면 고객 배포 웹 사이트 콘텐츠가 포함 된 원격 파일 공유 위치입니다.  원격 파일 공유의 감사 안 있습니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 예 | 관리 작업에 대 한 App Service 구성의 상태가 시간이 지남에 따라 Azure Resource Manager 템플릿으로 및 버전을 내보낼 수 있습니다. 런타임 작업에 대 한 고객은 App Service 배포 슬롯 기능을 사용 하 여 응용 프로그램의 여러 다른 라이브 버전을 유지할 수 있습니다. | 

