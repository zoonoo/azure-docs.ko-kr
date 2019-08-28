---
title: Azure App Service에 대 한 보안 특성
description: Azure App Service 평가를 위한 보안 특성의 검사 목록
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442262"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service에 대 한 보안 특성

이 문서에서는 Azure App Service에 기본 제공 되는 보안 특성을 설명 합니다.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 미사용 암호화 (서버 쪽 암호화, 고객이 관리 하는 키를 사용 하는 서버 쪽 암호화 및 기타 암호화 기능) | 예 | 웹 사이트 파일 콘텐츠는 미사용 콘텐츠를 자동으로 암호화 하는 Azure Storage에 저장 됩니다. [휴지 상태의 데이터에 대 한 Azure Storage 암호화를](../storage/common/storage-service-encryption.md)참조 하세요.<br><br>고객 제공 암호는 미사용 암호화 됩니다. 비밀은 App Service 구성 데이터베이스에 저장 되어 있는 동안 암호화 되지 않습니다.<br><br>필요에 따라 로컬에 연결 된 디스크를 웹 사이트 (D:\local 및% TMP%)에서 임시 저장소로 사용할 수 있습니다. 로컬에 연결 된 디스크는 미사용 상태로 암호화 되지 않습니다. |
| 전송 중 암호화 (예: Express 경로 암호화, VNet 암호화 및 VNet-VNet 암호화)| 예 | 고객은 인바운드 트래픽에 대해 HTTPS를 요구 하 고 사용 하도록 웹 사이트를 구성할 수 있습니다. [AZURE APP SERVICE HTTPS만을 만드는 방법에 대해서](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)는 블로그 게시물을 참조 하세요. |
| 암호화 키 처리 (CMK, BYOK 등)| 예 | 고객은 Key Vault에 응용 프로그램 암호를 저장 하 고 런타임에 검색 하도록 선택할 수 있습니다. [App Service 및 Azure Functions에 대 한 Key Vault 참조 사용 (미리 보기)](app-service-key-vault-references.md)을 참조 하세요.|
| 열 수준 암호화 (Azure Data Services)| N/A | |
| API 호출 암호화| 예 | App Service를 구성 하는 관리 호출은 HTTPS를 통한 [Azure Resource Manager](../azure-resource-manager/index.yml) 호출을 통해 수행 됩니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | 현재 App Service 미리 보기에서 사용할 수 있습니다. [Azure App Service 액세스 제한](app-service-ip-restrictions.md)을 참조 하세요. |
| VNet 삽입 지원| 예 | App Service 환경은 고객의 가상 네트워크에 주입 된 단일 고객 전용 App Service의 개인 구현입니다. [App Service 환경 소개를](environment/intro.md)참조 하세요. |
| 네트워크 격리 및 방화벽 지원| 예 | 공용 다중 테 넌 트 변형 App Service의 경우 고객은 네트워크 Acl (IP 제한)을 구성 하 여 허용 되는 인바운드 트래픽을 잠글 수 있습니다.  [Azure App Service 액세스 제한](app-service-ip-restrictions.md)을 참조 하세요.  App Service 환경은 가상 네트워크에 직접 배포 되므로 NSGs를 사용 하 여 보안을 유지할 수 있습니다. |
| 강제 터널링 지원| 예 | 강제 터널링이 구성 된 고객의 가상 네트워크에 App Service 환경을 배포할 수 있습니다. 고객은 [강제 터널링을 사용 하 여 App Service Environment 구성](environment/forced-tunnel-support.md)의 지침을 따라야 합니다. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure 모니터링 지원 (Log analytics, App insights 등)| 예 | App Service는 Application Insights (전체 .NET Framework, .NET Core, Java 및 NODE.JS)를 지 원하는 언어에 대 한 Application Insights와 통합 됩니다.  [Azure App Service 성능 모니터링](../azure-monitor/app/azure-web-apps.md)을 참조 하세요. 또한 App Service는 응용 프로그램 메트릭을 Azure Monitor 보냅니다. [Azure App Service에서 앱 모니터링을](web-sites-monitor.md)참조 하세요. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | 고객은 [Azure Active Directory (AZURE AD)](../active-directory/index.yml) 및 기타 OAuth 호환 id 공급자와 자동으로 통합 되는 App Service에서 응용 프로그램을 빌드할 수 있습니다. [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 참조 하세요. App Service 자산에 대 한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 된 주체 및 Azure Resource Manager RBAC 역할의 조합에 의해 제어 됩니다. |
| Authorization| 예 | App Service 자산에 대 한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 된 주체 및 Azure Resource Manager RBAC 역할의 조합에 의해 제어 됩니다.  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | App Service 개체에서 수행 되는 모든 관리 작업은 [Azure Resource Manager](../azure-resource-manager/index.yml)를 통해 수행 됩니다. 이러한 작업의 기록 로그는 포털 및 CLI를 통해 사용할 수 있습니다. [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftweb) 및 [az monitor activity-log](/cli/azure/monitor/activity-log)를 참조 하세요. |
| 데이터 평면 로깅 및 감사 | 아니요 | App Service에 대 한 데이터 평면은 고객의 배포 된 웹 사이트 콘텐츠를 포함 하는 원격 파일 공유입니다.  원격 파일 공유에 대 한 감사는 없습니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성 관리 지원 (구성의 버전 관리 등)| 예 | 관리 작업의 경우 App Service 구성 상태를 Azure Resource Manager 템플릿으로 내보내고 시간이 지남에 따라 버전을 지정할 수 있습니다. 런타임 작업의 경우 고객은 배포 슬롯 App Service 기능을 사용 하 여 응용 프로그램의 여러 다른 라이브 버전을 유지할 수 있습니다. | 

