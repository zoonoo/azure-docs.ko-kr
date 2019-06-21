---
title: Azure App Service에 대 한 보안 권장 사항
description: Azure App Service에 대 한 보안 권장 사항입니다. 이러한 권장 사항을 구현 하는 공유 책임 모델 되며에 설명 된 대로 귀하의 보안 의무를 충족 하는 도움말 향상 됩니다 웹 앱 솔루션에 대 한 전반적인 보안.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.openlocfilehash: f50532a21d11d2f7142fa3ee7ed08f759df36d52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207542"
---
# <a name="security-recommendations-for-app-service"></a>App Service에 대 한 보안 권장 사항

이 문서에서는 Azure App Service에 대 한 보안 권장 사항을 포함합니다. 이러한 권장 사항을 구현 하는 공유 책임 모델 되며에 설명 된 대로 귀하의 보안 의무를 충족 하는 도움말 향상 됩니다 웹 앱 솔루션에 대 한 전반적인 보안. Microsoft에 대 한 자세한 내용은 서비스 공급자 임무를 완수 하는 대 한 읽을 [Azure 인프라 보안](../security/azure-security-infrastructure.md)합니다.

## <a name="general"></a>일반

| 권장 사항 | 설명 |
|-|-|----|
| 최신 상태로 유지 | 지원 되는 플랫폼 프로그래밍 언어, 프로토콜 및 프레임 워크의 최신 버전을 사용 합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 사항 | 설명 |
|-|----|
| 익명 액세스를 사용 하지 않도록 설정 | 익명 요청을 지 원하는 데 필요 하지 않은 경우 익명 액세스를 비활성화 합니다. Azure App Service 인증 옵션에 대 한 자세한 내용은 참조 하세요. [인증 및 Azure App Service에서 권한 부여](overview-authentication-authorization.md)합니다.|
| 인증 필요 | 가능 하면 인증 및 권한 부여를 처리 하는 App Service 인증 모듈 코드를 작성 하는 대신 사용 합니다. 참조 [인증 및 Azure App Service에서 권한 부여](overview-authentication-authorization.md)합니다. |
| 인증 된 액세스를 사용 하 여 백 엔드 리소스를 보호 합니다. | 사용자의 id를 사용 하거나 응용 프로그램 id를 사용 하 여 백 엔드 리소스에 인증 합니다. 응용 프로그램 id 사용을 사용 하도록 선택 하는 경우는 [관리 되는 id](overview-managed-identity.md)합니다.
| 클라이언트 인증서 인증을 요구 | 클라이언트 인증서 인증에만 제공 하는 인증서를 사용 하 여 인증할 수 있는 클라이언트에서 연결을 허용 하 여 보안이 향상 됩니다. |

## <a name="data-protection"></a>데이터 보호

| 권장 사항 | 설명 |
|-|-|
| HTTP에서 HTTPs로 리디렉션 | 기본적으로 클라이언트 둘 다 HTTP 또는 HTTPS를 사용 하 여 웹 앱에 연결할 수 있습니다. 암호화 및 인증 된 HTTPS는 보안 연결을 제공 하는 SSL/TLS 프로토콜을 사용 하기 때문에 HTTP에서 HTTPs로 리디렉션하는 것이 좋습니다. |
| Azure 리소스에 대 한 통신을 암호화 | 앱에 연결 될 때 Azure 리소스와 같은 [SQL Database](https://azure.microsoft.com/services/sql-database/) 하거나 [Azure Storage](/azure/storage/), Azure에서 연결 유지 합니다. Azure에서 공유 네트워킹을 통해 연결 되 면 되므로 항상 모든 통신을 암호화 해야 합니다. |
| 가능한 최신 TLS 버전 필요 | 2018부터 새 Azure App Service 앱은 TLS 1.2를 사용 합니다. 최신 버전의 TLS 이전 프로토콜 버전에 비해 향상 된 보안 기능을 포함 합니다. |
| FTPS를 사용 합니다. | App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 가능한 경우 FTP 대신 FTPS를 사용 합니다. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다. |
| 애플리케이션 데이터 보호 | 코드 또는 구성 파일에서 데이터베이스 자격 증명, API 토큰 또는 개인 키와 같은 응용 프로그램 비밀을 저장 하지 마십시오. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. Azure App Service에서 환경 변수를 정의할 수 있습니다 [앱 설정](web-sites-configure.md) 하 고 [연결 문자열](web-sites-configure.md)합니다. 앱 설정 및 연결 문자열과 Azure에서 암호화 되어 저장 됩니다. 앱 설정은 앱 시작 시 앱의 프로세스 메모리 주입 하기 전에 암호가 해독 됩니다. 암호화 키는 정기적으로 회전합니다. 또는 사용 하 여 Azure App Service 앱에 통합할 수 있습니다 [Azure Key Vault](/azure/key-vault/) 고급 암호 관리에 대 한 합니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/tutorial-web-application-keyvault.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다. |

## <a name="networking"></a>네트워킹

| 권장 사항 | 설명 |
|-|-|
| 정적 IP 제한을 사용 하 여 | Windows의 azure App Service를 사용 하면 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.  |
| 격리 가격 책정 계층을 사용 하 여 | 격리 가격 책정 계층을 제외 하 고 모든 계층 공유 네트워크 인프라에 Azure App Service에서 앱을 실행합니다. 격리 된 계층을 사용 하면 전체 네트워크 격리 전용 내부에 앱을 실행 하 여 [App Service environment](environment/intro.md)합니다. App Service 환경은 [Azure Virtual Network](/azure/virtual-network/)의 사용자 인스턴스에서 실행됩니다.|
| 때 액세스 온-프레미스 리소스 보안 연결 사용 | 사용할 수 있습니다 [하이브리드 연결](app-service-hybrid-connections.md)를 [가상 네트워크 통합](web-sites-integrate-with-vnet.md), 또는 [App Service environment의](environment/intro.md) 온-프레미스 리소스에 연결 합니다. |
| 인바운드 네트워크 트래픽에 대 한 노출을 제한합니다 | 네트워크 보안 그룹에 네트워크 액세스를 제한 하 고 노출 된 끝점의 수를 제어할 수 있습니다. 자세한 내용은 [하는 방법에 인바운드 트래픽을 제어 App Service Environment를](environment/app-service-app-service-environment-control-inbound-traffic.md)입니다. |

## <a name="monitoring"></a>모니터링

| 권장 사항 | 설명 |
|-|-|
|사용 하 여 Azure Security Center 표준 계층 | [Azure Security Center](../security-center/security-center-app-services.md) Azure App Service를 사용 하 여 고유 하 게 통합 됩니다. 평가 실행 하 고 보안 권장 사항을 제공할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

추가 보안 요구 사항이 있는지 응용 프로그램 공급 업체와 함께 확인 합니다. 보안 응용 프로그램 개발에 대 한 자세한 내용은 참조 하세요. [안전한 개발 설명서](../security/abstract-develop-secure-apps.md)합니다.
