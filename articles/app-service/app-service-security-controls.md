---
title: 보안 컨트롤
description: 조직에 대한 Azure 앱 서비스를 평가하기 위한 보안 컨트롤의 검사 목록을 찾습니다.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671445"
---
# <a name="security-controls-for-azure-app-service"></a>Azure 앱 서비스에 대한 보안 제어

이 문서에서는 Azure App Service에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 | 설명서
|---|---|--|
| 서비스 엔드포인트 지원| yes | 앱 서비스에 사용할 수 있습니다.| [Azure 앱 서비스 액세스 제한](app-service-ip-restrictions.md)
| VNet 주입 지원| yes | 앱 서비스 환경은 고객의 가상 네트워크에 주입된 단일 고객 전용 앱 서비스의 개인 구현입니다. | [App Service Environment 소개](environment/intro.md)
| 네트워크 격리 및 방화벽 지원| yes | 앱 서비스의 공용 다중 테넌트 변형의 경우 고객은 허용된 인바운드 트래픽을 잠그도록 네트워크 ACL(IP 제한)을 구성할 수 있습니다.  앱 서비스 환경은 가상 네트워크에 직접 배포되므로 NSG를 통해 보호할 수 있습니다. | [Azure 앱 서비스 액세스 제한](app-service-ip-restrictions.md)
| 강제 터널링 지원| yes | 앱 서비스 환경은 강제 터널링이 구성된 고객의 가상 네트워크에 배포할 수 있습니다. | [강제 터널링으로 App Service Environment 구성](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모 | 설명서
|---|---|--|
| Azure 모니터링 지원(로그 분석, 앱 인사이트 등)| yes | 앱 서비스는 응용 프로그램 인사이트(전체 .NET 프레임워크, .NET 코어, Java 및 Node.JS)를 지원하는 언어에 대한 응용 프로그램 인사이트와 통합됩니다.  [Azure 앱 서비스 성능 모니터링을](../azure-monitor/app/azure-web-apps.md)참조하십시오. 또한 앱 서비스는 응용 프로그램 메트릭을 Azure 모니터로 보냅니다. | [Azure App Service에서 앱 모니터링](web-sites-monitor.md)
| 제어 및 관리 평면 로깅 및 감사| yes | 앱 서비스 개체에서 수행된 모든 관리 작업은 [Azure 리소스 관리자를](../azure-resource-manager/index.yml)통해 수행됩니다. 이러한 작업의 기록 로그는 포털과 CLI를 통해 모두 사용할 수 있습니다. | [Azure 리소스 관리자 리소스 공급자 작업,](../role-based-access-control/resource-provider-operations.md#microsoftweb) [az 모니터 활동 로그](/cli/azure/monitor/activity-log)
| 데이터 평면 로깅 및 감사 | 예 | 앱 서비스의 데이터 평면은 고객의 배포된 웹 사이트 콘텐츠를 포함하는 원격 파일 공유입니다.  원격 파일 공유에 대한 감사가 없습니다. |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모 |  설명서
|---|---|--|
| 인증| yes | 고객은 [Azure Active Directory(Azure AD)](../active-directory/index.yml) 및 기타 OAuth 호환 ID 공급자와 자동으로 통합되는 앱 서비스에서 응용 프로그램을 빌드할 수 있습니다. | [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)
| 권한 부여| yes | 앱 서비스 자산에 대한 관리 액세스의 경우 모든 액세스는 Azure AD 인증 보안 주체 및 Azure 리소스 관리자 RBAC 역할의 조합으로 제어됩니다.  | [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 | 설명서
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | 웹 사이트 파일 콘텐츠는 Azure Storage에 저장되며 미사용 콘텐츠를 자동으로 암호화합니다. <br><br>고객이 제공한 비밀은 미사용 으로 암호화됩니다. 비밀은 앱 서비스 구성 데이터베이스에 저장되는 동안 미사용 으로 암호화됩니다.<br><br>로컬로 연결된 디스크는 선택적으로 웹 사이트에서 임시 저장소로 사용할 수 있습니다(D:\local 및 %TMP%). 로컬로 연결된 디스크는 미사용 으로 암호화되지 않습니다. | [미사용 데이터에 대한 Azure 저장소 암호화](../storage/common/storage-service-encryption.md)
| 미사용 서버 측 암호화: 고객 관리 키(BYOK) | yes | 고객은 키 볼트에 응용 프로그램 비밀을 저장하고 런타임에 검색하도록 선택할 수 있습니다. | [App Service 및 Azure Functions의 Key Vault 참조 사용(미리 보기)](app-service-key-vault-references.md)
| 열 수준 암호화(Azure 데이터 서비스)| 해당 없음 | |
| 전송 중 암호화(예: 익스프레스라우팅 암호화, VNet 암호화 및 VNet-VNet 암호화)| yes | 고객은 인바운드 트래픽에 HTTPS를 요구하고 사용하도록 웹 사이트를 구성할 수 있습니다.  | [Azure 앱 서비스 HTTPS만 만드는 방법(블로그](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) 게시물)
| API 호출 암호화| yes | 앱 서비스를 구성하기 위한 관리 호출은 HTTPS를 통해 [Azure 리소스 관리자](../azure-resource-manager/index.yml) 호출을 통해 발생합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모 | 설명서
|---|---|--|
| 구성 관리 지원(구성 버전 관리 등)| yes | 관리 작업의 경우 앱 서비스 구성의 상태를 Azure 리소스 관리자 템플릿으로 내보내고 시간에 따라 버전이 적용될 수 있습니다. 런타임 작업의 경우 고객은 App Service 배포 슬롯 기능을 사용하여 응용 프로그램의 여러 라이브 버전을 유지할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
