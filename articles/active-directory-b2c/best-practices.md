---
title: Azure AD B2C에 대한 모범 사례
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C(Azure AD B2C)로 작업할 때 고려해야 할 권장 사항 및 모범 사례입니다.
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136162"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대한 권장 사항 및 모범 사례

다음 모범 사례 및 권장 사항은 Azure Active Directory(Azure AD) B2C를 기존 또는 새 응용 프로그램 환경에 통합하는 몇 가지 주요 측면을 다룹니다.

## <a name="fundamentals"></a>기본 사항

|  |  |
|--|--|
| 대부분의 시나리오에 대한 사용자 흐름 선택 | Azure AD B2C의 ID 환경 프레임워크는 서비스의 핵심 강점입니다. 정책은 가입, 로그인 또는 프로필 편집과 같은 ID 환경을 완벽하게 설명합니다. 가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 사용자 흐름이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 사용자 흐름을 사용하면 몇 번의 클릭만으로 몇 분 만에 뛰어난 사용자 환경을 만들 수 있습니다. [사용자 흐름과 사용자 지정 정책을 사용하는 시기에 대해 알아봅니다.](custom-policy-overview.md#comparing-user-flows-and-custom-policies)|
| 앱 등록 | 보안되는 모든 응용 프로그램(웹, 네이티브) 및 API는 Azure AD B2C에 등록해야 합니다. 앱에 웹 버전과 기본 버전의 iOS 및 Android가 모두 있는 경우 동일한 클라이언트 ID를 가진 Azure AD B2C에서 하나의 응용 프로그램으로 등록할 수 있습니다. [OIDC, SAML, 웹 및 네이티브 앱을 등록하는](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)방법에 대해 알아봅니다. [Azure AD B2C에서 사용할 수 있는 응용 프로그램 형식에](https://docs.microsoft.com/azure/active-directory-b2c/application-types)대해 자세히 알아봅니다. |
| 월별 활성 사용자 청구로 이동 | Azure AD B2C가 월별 활성 인증에서 월별 활성 사용자(MAU) 청구로 이동했습니다. 대부분의 고객은 이 모델을 비용 효율적인 모델로 찾을 수 있습니다. [월별 활성 사용자 청구에 대해 자세히 알아보세요.](https://azure.microsoft.com/updates/mau-billing/) |

## <a name="planning-and-design"></a>계획 및 설계

응용 프로그램 및 서비스 아키텍처, 인벤토리 현재 시스템을 정의하고 Azure AD B2C로의 마이그레이션을 계획합니다.

|  |  |
|--|--|
| 엔드 투 엔드 솔루션 설계 | Azure AD B2C 통합을 계획할 때 모든 응용 프로그램의 종속성을 포함합니다. 현재 사용자 환경에 있거나 솔루션에 추가해야 할 수 있는 모든 서비스 및 제품(예: Azure Functions, CRM) 시스템, Azure API 관리 게이트웨이 및 저장소 서비스입니다. 모든 서비스의 보안 및 확장성을 고려하십시오. |
| 사용자 경험 문서화 | 고객이 애플리케이션에서 경험할 수 있는 모든 사용자 여정을 자세히 설명합니다. 응용 프로그램의 ID 및 프로필 측면과 상호 작용할 때 발생할 수 있는 모든 화면 및 분기 흐름을 포함합니다. 계획에 유용성, 접근성 및 지역화를 포함합니다. |
| 올바른 인증 프로토콜 선택 |  다양한 응용 프로그램 시나리오 및 권장 인증 흐름에 대한 분석은 [시나리오 및 지원되는 인증 흐름을](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)참조하십시오. |
| POC(개념 증명) 종단 간 사용자 환경 파일럿 | [Microsoft 코드 샘플](code-samples.md) 및 [커뮤니티 샘플로](https://github.com/azure-ad-b2c/samples)시작합니다. |
| 마이그레이션 계획 만들기 |미리 계획하면 마이그레이션이 보다 원활하게 진행될 수 있습니다. [사용자 마이그레이션에](user-migration.md)대해 자세히 알아봅니다.|
| 유용성 과 보안 | 솔루션은 애플리케이션 유용성과 조직의 허용 가능한 위험 수준 간의 적절한 균형을 유지해야 합니다. |
| 온-프레미스 종속성을 클라우드로 이동 | 복원력 있는 솔루션을 보장하려면 기존 응용 프로그램 종속성을 클라우드로 이동하는 것이 좋습니다. |
| 기존 앱을 b2clogin.com 마이그레이션 | login.microsoftonline.com 사용 중단은 2020년 12월 04일에 모든 Azure AD B2C 테넌트에 적용됩니다. [자세히 알아봅니다](b2clogin.md). |

## <a name="implementation"></a>구현

구현 단계에서는 다음 권장 사항을 고려하십시오.

|  |  |
|--|--|
| Visual Studio 코드에 대한 Azure AD B2C 확장을 통해 사용자 지정 정책 편집 | 비주얼 스튜디오 코드 [마켓플레이스에서](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)비주얼 스튜디오 코드 및 커뮤니티에서 구축한 확장을 다운로드합니다. 공식 Microsoft 제품은 아니지만 Visual Studio 코드용 Azure AD B2C 확장에는 사용자 지정 정책 작업을 더 쉽게 할 수 있는 몇 가지 기능이 포함되어 있습니다. |
| Azure AD B2C 문제 해결 방법 알아보기 | 개발 중 [사용자 지정 정책 문제를 해결하는](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) 방법을 알아봅니다. 일반적인 인증 흐름의 모양을 알아보고 이상 및 오류를 검색하는 데 도구를 사용합니다. 예를 들어 [응용 프로그램 통찰력을](troubleshoot-with-application-insights.md) 사용하여 사용자 여정의 출력 로그를 검토합니다. |
| 검증된 사용자 지정 정책 패턴 라이브러리 활용 | 몇 가지 향상된 Azure AD B2C 고객 ID 및 CIAM(액세스 관리) 사용자 여정에 대한 [샘플을](https://github.com/azure-ad-b2c/samples) 찾습니다. |


## <a name="testing"></a>테스트

Azure AD B2C 구현을 테스트하고 자동화합니다.

|  |  |
|--|--|
| 글로벌 트래픽 계정 | 다양한 글로벌 주소의 트래픽 소스를 사용하여 성능 및 지역화 요구 사항을 테스트합니다. 모든 HTML, CSS 및 종속성이 성능 요구 사항을 충족할 수 있는지 확인합니다. |
| 기능 및 UI 테스트 | 사용자 흐름을 종단 간 테스트합니다. 셀레늄, VS 웹 테스트 등을 사용하여 몇 분마다 합성 테스트를 추가합니다. |
| 펜 테스트 | 솔루션을 시작하기 전에 침투 테스트 연습을 수행하여 타사 종속성을 포함하여 모든 구성 요소가 안전한지 확인합니다. 액세스 토큰으로 API를 보호하고 응용 프로그램 시나리오에 적합한 인증 프로토콜을 사용했는지 확인합니다. [보급률 테스트](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) 및 [Microsoft 클라우드 통합 침투 테스트 규칙에](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)대해 자세히 알아보십시오. |
| A/B 테스트 | 전체 인구에 출시하기 전에 작고 임의의 사용자 집합으로 새 기능을 플라이드합니다. Azure AD B2C에서 자바스크립트를 사용하도록 설정하면 최적화, 선명도 및 기타 와 같은 A/B 테스트 도구와 통합할 수 있습니다. |
| 부하 테스트 | Azure AD B2C는 확장할 수 있지만 모든 종속성이 확장할 수 있는 경우에만 응용 프로그램의 크기를 조정할 수 있습니다. API 및 CDN을 로드 테스트합니다. |
| 스로틀 |  Azure AD B2C는 짧은 시간 동안 동일한 원본에서 너무 많은 요청을 보내는 경우 트래픽을 제한합니다. 로드 테스트 중에 여러 트래픽 소스를 `AADB2C90229` 사용하고 응용 프로그램에서 오류 코드를 정상적으로 처리합니다. |
| Automation | 지속적인 통합 및 제공(CI/CD) 파이프라인을 사용하여 테스트 및 배포를 자동화합니다(예: [Azure DevOps).](deploy-custom-policies-devops.md) |

## <a name="operations"></a>작업

Azure AD B2C 환경을 관리합니다.

|  |  |
|--|--|
| 여러 환경 만들기 | 보다 쉬운 운영 및 배포 롤아웃을 위해 개발, 테스트, 사전 프로덕션 및 프로덕션을 위한 별도의 환경을 만듭니다. 각각에 대해 Azure AD B2C 테넌을 만듭니다. |
| 사용자 지정 정책에 버전 제어 사용 | Azure AD B2C 사용자 지정 정책에 GitHub, Azure 리포지토리 또는 다른 클라우드 기반 버전 제어 시스템을 사용하는 것이 좋습니다. |
| Microsoft 그래프 API를 사용하여 B2C 테넌트 관리를 자동화합니다. | 마이크로 소프트 그래프 API :<br/>[ID 환경 프레임워크](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) 관리(사용자 지정 정책)<br/>[키](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[사용자 흐름](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Azure DevOps와 통합 | [CI/CD 파이프라인을](deploy-custom-policies-devops.md) 사용하면 서로 다른 환경 간에 코드를 쉽게 이동할 수 있으며 항상 프로덕션 준비 상태를 보장합니다.   |
| Azure Monitor와 통합 | [감사 로그 이벤트는](view-audit-logs.md) 7일 동안만 유지됩니다. [Azure Monitor와 통합하여](azure-monitor.md) 장기 사용을 위해 로그를 유지하거나 타사 보안 정보 및 SIEM(이벤트 관리) 도구와 통합하여 사용자 환경에 대한 통찰력을 얻을 수 있습니다. |
| 설정 활성 경고 및 모니터링 | 응용 프로그램 통찰력을 사용하여 Azure AD B2C에서 [사용자 동작을 추적합니다.](active-directory-b2c-custom-guide-eventlogger-appins.md) |


## <a name="support-and-status-updates"></a>지원 및 상태 업데이트

서비스 상태를 최신 상태로 유지하고 지원 옵션을 찾을 수 있습니다.

|  |  |
|--|--|
| [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C 제품 업데이트 및 공지 사항으로 최신 정보를 제공합니다. |
| [Microsoft 지원](support-options.md) | Azure AD B2C 기술 문제에 대한 지원 요청을 제출합니다. 청구 및 구독 관리 지원은 무료로 제공됩니다. |
| [Azure 상태](https://status.azure.com/status) | 모든 Azure 서비스의 현재 상태 상태를 봅니다. |