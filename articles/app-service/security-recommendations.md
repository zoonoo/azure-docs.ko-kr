---
title: 보안 권장 사항
description: 공동 책임 모델에 명시된 보안 의무를 이행하는 데 도움이 되는 보안 권장 사항을 구현합니다. 앱의 보안을 개선합니다.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 50e2666aa533a5111055a095c612b58bfe6f9db4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546695"
---
# <a name="security-recommendations-for-app-service"></a>앱 서비스에 대한 보안 권장 사항

이 문서에는 Azure 앱 서비스에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 되며 Web App 솔루션의 전반적인 보안을 개선할 수 있습니다. Microsoft가 서비스 공급자의 책임을 수행하기 위해 수행하는 일에 대한 자세한 내용은 [Azure 인프라 보안을](../security/fundamentals/infrastructure.md)참조하십시오.

## <a name="general"></a>일반

| 권장 | 주석 |
|-|-|----|
| 최신 정보 유지 | 지원되는 플랫폼, 프로그래밍 언어, 프로토콜 및 프레임워크의 최신 버전을 사용합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 |
|-|----|
| 익명 액세스 사용 안 함 | 익명 요청을 지원해야 하는 경우가 아니면 익명 액세스를 사용하지 않도록 설정합니다. Azure 앱 서비스 인증 옵션에 대한 자세한 내용은 Azure 앱 서비스 에서 [인증 및 권한 부여를](overview-authentication-authorization.md)참조하세요.|
| 인증 필요 | 가능하면 인증 및 권한 부여를 처리하기 위해 코드를 작성하는 대신 앱 서비스 인증 모듈을 사용합니다. [Azure 앱 서비스 에서 인증 및 권한 부여를](overview-authentication-authorization.md)참조하십시오. |
| 인증된 액세스를 통해 백 엔드 리소스 보호 | 사용자의 ID를 사용하거나 응용 프로그램 ID를 사용하여 백 엔드 리소스에 인증할 수 있습니다. 응용 프로그램 ID를 사용하도록 선택하면 [관리되는 ID를](overview-managed-identity.md)사용합니다.
| 클라이언트 인증서 인증 필요 | 클라이언트 인증서 인증은 사용자가 제공하는 인증서를 사용하여 인증할 수 있는 클라이언트의 연결만 허용하여 보안을 향상시킵니다. |

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 |
|-|-|
| HTTP를 HTTP로 리디렉션 | 기본적으로 클라이언트는 HTTP 또는 HTTPS를 모두 사용하여 웹 앱에 연결할 수 있습니다. HTTPS는 SSL/TLS 프로토콜을 사용하여 암호화되고 인증된 보안 연결을 제공하기 때문에 HTTP를 HTTP로 리디렉션하는 것이 좋습니다. |
| Azure 리소스에 대한 통신 암호화 | 앱이 [SQL Database](https://azure.microsoft.com/services/sql-database/) 또는 [Azure 저장소와](/azure/storage/)같은 Azure 리소스에 연결하면 연결은 Azure에 유지됩니다. 연결은 Azure의 공유 네트워킹을 거치므로 항상 모든 통신을 암호화해야 합니다. |
| 가능한 최신 TLS 버전 필요 | 2018년부터 새로운 Azure 앱 서비스 앱은 TLS 1.2를 사용합니다. 최신 버전의 TLS에는 이전 프로토콜 버전에 대한 보안 개선 사항이 포함되어 있습니다. |
| FTPS 사용 | App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 가능하면 FTP 대신 FTPS를 사용하십시오. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다. |
| 애플리케이션 데이터 보호 | 데이터베이스 자격 증명, API 토큰 또는 개인 키와 같은 응용 프로그램 암호를 코드 또는 구성 파일에 저장하지 마십시오. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. Azure 앱 서비스에서 [앱 설정](web-sites-configure.md) 및 연결 문자열을 통해 환경 [변수를 정의할](web-sites-configure.md)수 있습니다. 앱 설정 및 연결 문자열은 Azure에서 암호화된 저장됩니다. 앱 설정은 앱이 시작될 때 앱의 프로세스 메모리에 삽입되기 전에만 해독됩니다. 암호화 키는 정기적으로 회전합니다. 또는 고급 비밀 관리를 위해 Azure 앱 서비스 앱을 [Azure 키 자격 증명 모음과](/azure/key-vault/) 통합할 수 있습니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/tutorial-web-application-keyvault.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다. |

## <a name="networking"></a>네트워킹

| 권장 | 주석 |
|-|-|
| 정적 IP 제한 사용 | Windows의 Azure 앱 서비스를 사용하면 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.  |
| 격리된 가격 책정 계층 사용 | 격리된 가격 책정 계층을 제외한 모든 계층은 Azure App Service의 공유 네트워크 인프라에서 앱을 실행합니다. 격리된 계층은 전용 [앱 서비스 환경](environment/intro.md)내에서 앱을 실행하여 완전한 네트워크 격리를 제공합니다. App Service 환경은 [Azure Virtual Network](/azure/virtual-network/)의 사용자 인스턴스에서 실행됩니다.|
| 온-프레미스 리소스에 액세스할 때 보안 연결 사용 | [하이브리드 연결,](app-service-hybrid-connections.md) [가상 네트워크 통합](web-sites-integrate-with-vnet.md)또는 앱 서비스 [환경을](environment/intro.md) 사용하여 온-프레미스 리소스에 연결할 수 있습니다. |
| 인바운드 네트워크 트래픽에 대한 노출 제한 | 네트워크 보안 그룹을 사용하면 네트워크 액세스를 제한하고 노출된 끝점 수를 제어할 수 있습니다. 자세한 내용은 [앱 서비스 환경에 대한 인바운드 트래픽을 제어하는 방법을](environment/app-service-app-service-environment-control-inbound-traffic.md)참조하세요. |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 |
|-|-|
|Azure 보안 센터 표준 계층 사용 | [Azure 보안 센터는](../security-center/security-center-app-services.md) 기본적으로 Azure 앱 서비스와 통합됩니다. 평가를 실행하고 보안 권장 사항을 제공할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

추가 보안 요구 사항이 있는지 확인하려면 응용 프로그램 공급자에게 문의하십시오. 보안 응용 프로그램 개발에 대한 자세한 내용은 [보안 개발 문서를](../security/fundamentals/abstract-develop-secure-apps.md)참조하십시오.
