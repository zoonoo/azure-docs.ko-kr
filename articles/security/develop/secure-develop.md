---
title: Microsoft Azure에서 보안 응용 프로그램 개발
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 구현 및 확인 단계에서 고려해야 할 모범 사례에 대해 설명합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255653"
---
# <a name="develop-secure-applications-on-azure"></a>Azure에서 보안 애플리케이션 개발
이 문서에서는 클라우드용 응용 프로그램을 개발할 때 고려해야 할 보안 활동 및 컨트롤을 소개합니다. Microsoft [보안 개발 수명 주기(SDL)의](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 구현 및 확인 단계에서 고려해야 할 보안 질문 및 개념이 다룹니다. 목표는 보다 안전한 응용 프로그램을 개발하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의하는 데 도움을 주는 것입니다.

다음 SDL 단계는 이 문서에서 다룹니다.

- 구현
- 확인

## <a name="implementation"></a>구현
구현 단계의 초점은 조기 예방을 위한 모범 사례를 수립하고 코드에서 보안 문제를 감지하고 제거하는 것입니다.
응용 프로그램이 사용하지 않으려는 방식으로 사용된다고 가정합니다. 이렇게 하면 응용 프로그램의 우발적이거나 의도적인 오용을 방지할 수 있습니다.

### <a name="perform-code-reviews"></a>코드 검토 수행

코드를 체크 인하기 전에 [코드 검토를](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) 수행하여 전반적인 코드 품질을 높이고 버그를 만들 위험을 줄입니다. [Visual Studio를](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) 사용하여 코드 검토 프로세스를 관리할 수 있습니다.

### <a name="perform-static-code-analysis"></a>정적 코드 분석 수행

[정적 코드 분석(소스 코드](https://www.owasp.org/index.php/Static_Code_Analysis) *분석이라고도*함)은 일반적으로 코드 검토의 일부로 수행됩니다. 정적 코드 분석은 일반적으로 정적 코드 분석 도구를 실행하여 [오염 검사](https://en.wikipedia.org/wiki/Taint_checking) 및 데이터 흐름 분석과 같은 기술을 사용하여 실행되지 않는 코드에서 잠재적인 [취약점을](https://en.wikipedia.org/wiki/Data-flow_analysis)찾는 것을 말합니다.

Azure Marketplace는 정적 코드 분석을 수행하고 코드 검토를 지원하는 [개발자 도구를](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) 제공합니다.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>애플리케이션의 모든 입력 유효성 검사 및 소독

모든 입력을 신뢰할 수 없는 것으로 처리하여 가장 일반적인 웹 응용 프로그램 취약점으로부터 응용 프로그램을 보호합니다. 신뢰할 수 없는 데이터는 주입 공격을 위한 수단입니다. 응용 프로그램에 대한 입력에는 URL의 매개 변수, 사용자의 입력, 데이터베이스 또는 API의 데이터 및 사용자가 잠재적으로 조작할 수 있는 모든 항목이 포함됩니다. 응용 프로그램은 응용 프로그램이 어떤 식으로든 데이터를 사용하기 전에(사용자에게 다시 표시하는 등) 데이터가 구문적으로 그리고 시맨티컬적으로 유효한지 [확인해야](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) 합니다.

데이터 흐름의 초기에 입력의 유효성을 검사하여 제대로 형성된 데이터만 워크플로에 들어가지 않도록 합니다. 잘못된 형식의 데이터가 데이터베이스에 유지되지 않거나 다운스트림 구성 요소의 오작동을 유발하지 않도록 합니다.

블랙리스트 및 허용 목록은 입력 구문 유효성 검사를 수행하는 두 가지 일반적인 접근 방식입니다.

  - 블랙리스트는 지정된 사용자 입력에 "악의적인 것으로 알려진" 콘텐츠가 포함되어 있지 않은지 확인하려고 시도합니다.

  - 허용 목록 지정은 지정된 사용자 입력이 "알려진 양호" 입력 집합과 일치하는지 확인하려고 시도합니다. 문자 기반 허용 목록은 응용 프로그램에서 사용자 입력에 "알려진 양호" 문자만 포함하거나 입력이 알려진 형식과 일치하는지 확인하는 허용 목록의 한 형태입니다.
    예를 들어 사용자 이름에 는 숫자만 포함되어 있는지 또는 정확히 두 개의 숫자가 포함되어 있는지 확인하는 것이 포함될 수 있습니다.

화이트리스팅은 보안 소프트웨어를 구축하는 데 선호되는 접근 방식입니다.
블랙리스트는 잠재적으로 잘못된 입력의 전체 목록을 생각할 수 없기 때문에 오류가 발생하기 쉽습니다.

클라이언트 측(또는 서버 및 클라이언트 측)이 아닌 서버에서 이 작업을 수행합니다.

### <a name="verify-your-applications-outputs"></a>응용 프로그램의 출력 확인

시각적으로 또는 문서 내에서 표시하는 모든 출력은 항상 인코딩되고 이스케이프되어야 합니다. *출력 인코딩이라고도*하는 [이스케이프는](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)신뢰할 수 없는 데이터가 주입 공격의 수단이 아님을 확인하는 데 사용됩니다. 데이터 유효성 검사와 결합된 이스케이프는 계층화된 방어를 제공하여 시스템 전체의 보안을 향상시입니다.

이스케이프는 모든 것이 *출력으로* 표시되는지 확인합니다. 또한 이스케이프를 사용하면 인터프리터가 데이터를 실행하기 위한 것이 아님을 알 수 있으며, 이로 인해 공격이 작동하지 않습니다. 이것은 *크로스 사이트 스크립팅* (XSS)라는 또 다른 일반적인 공격 기술입니다.

타사의 웹 프레임워크를 사용하는 경우 [OWASP XSS 방지 치트 시트를](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)사용하여 웹 사이트에서 출력 인코딩 옵션을 확인할 수 있습니다.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>데이터베이스에 문의할 때 매개 변수화된 쿼리 사용

코드에서 "즉시" 인라인 데이터베이스 쿼리를 만들고 데이터베이스로 직접 보내지 마십시오. 응용 프로그램에 삽입된 악성 코드로 인해 데이터베이스를 도난당하거나 초기화하거나 수정할 수 있습니다. 데이터베이스를 호스팅하는 운영 체제에서 악의적인 운영 체제 명령을 실행하는 데도 응용 프로그램을 사용할 수 있습니다.

대신 매개 변수화된 쿼리 또는 저장 프로시저를 사용합니다. 매개 변수화된 쿼리를 사용하는 경우 코드에서 프로시저를 안전하게 호출하고 쿼리 문의 일부로 처리될 염려 없이 문자열을 전달할 수 있습니다.

### <a name="remove-standard-server-headers"></a>표준 서버 헤더 제거

서버, X-Powered-By 및 X-AspNet 버전과 같은 헤더는 서버 및 기본 기술에 대한 정보를 표시합니다. 응용 프로그램의 지문 이 시점을 방지하려면 이러한 헤더를 사용하지 않는 것이 좋습니다.
[Azure 웹 사이트에서 표준 서버 헤더 제거를](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)참조하십시오.

### <a name="segregate-your-production-data"></a>생산 데이터 분리

생산 데이터 또는 "실제" 데이터는 개발, 테스트 또는 비즈니스가 의도한 목적 이외의 다른 목적으로 사용해서는 안 됩니다. [마스크된(익명화된)](https://en.wikipedia.org/wiki/Data_anonymization)데이터 집합은 모든 개발 및 테스트에 사용해야 합니다.

즉, 실제 데이터에 액세스할 수 있는 사람이 줄어들어 공격 표면이 줄어듭니다. 또한 개인 데이터를 보는 직원의 수가 줄어들어 기밀 유지가 침해될 수 있습니다.

### <a name="implement-a-strong-password-policy"></a>강력한 암호 정책 구현

무차별 암호 대입 및 사전 기반 추측을 방지하려면 사용자가 복잡한 암호를 만들 수 있도록 강력한 암호 정책을 구현해야 합니다(예: 최소 길이 12자 및 영숫자 및 특수 문자 필요).

ID 프레임워크를 사용하여 암호 정책을 만들고 적용할 수 있습니다. Azure AD B2C는 [기본 제공 정책,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [셀프 서비스 암호 재설정](../../active-directory-b2c/user-flow-self-service-password-reset.md)등을 제공하여 암호 관리를 지원합니다.

기본 계정의 공격을 방어하려면 모든 키와 암호를 교체할 수 있고 리소스를 설치한 후 키와 암호가 생성되거나 대체되는지 확인합니다.

응용 프로그램에서 암호를 자동으로 생성해야 하는 경우 생성된 암호가 임의이고 엔트로피가 높은지 확인합니다.

### <a name="validate-file-uploads"></a>파일 업로드 유효성 검사

응용 프로그램에서 [파일 업로드를](https://www.owasp.org/index.php/Unrestricted_File_Upload)허용하는 경우 이 위험한 활동에 대해 취할 수 있는 예방 조치를 고려합니다. 많은 공격의 첫 번째 단계는 공격을 받고 있는 시스템에 일부 악성 코드를 얻는 것입니다. 파일 업로드를 사용하면 공격자가 이 작업을 수행하는 데 도움이 됩니다. OWASP는 업로드하는 파일이 안전한지 확인하기 위해 파일의 유효성을 검사하는 솔루션을 제공합니다.

맬웨어 방지 보호는 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 됩니다. [당신은 마이크로 소프트 맬웨어 방지](../fundamentals/antimalware.md) 또는 마이크로 소프트 파트너의 엔드 포인트 보호 솔루션을 설치할 수 있습니다[(트렌드 마이크로,](https://www.trendmicro.com/azure/) [브로드 컴,](https://www.broadcom.com/products) [맥아피](https://www.mcafee.com/us/products.aspx), [윈도우 디펜더,](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)엔드 [포인트 보호).](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)

[Microsoft 맬웨어 방지에는](../fundamentals/antimalware.md) 실시간 보호, 예약된 검색, 맬웨어 업데이트, 서명 업데이트, 엔진 업데이트, 샘플 보고 및 제외 이벤트 컬렉션과 같은 기능이 포함되어 있습니다. 배포 및 기본 제공 검색(경고 및 인시던트)을 용이하게 하기 위해 [Azure Security Center](../../security-center/security-center-partner-integration.md)와 Microsoft 맬웨어 방지 프로그램 및 파트너 솔루션을 통합할 수 있습니다.

### <a name="dont-cache-sensitive-content"></a>민감한 콘텐츠를 캐시하지 마세요.

브라우저에서 중요한 콘텐츠를 캐시하지 마십시오. 브라우저는 캐싱 및 기록에 대한 정보를 저장할 수 있습니다. 캐시된 파일은 인터넷 익스플로러의 경우 임시 인터넷 파일 폴더와 같은 폴더에 저장됩니다. 이러한 페이지를 다시 참조하면 브라우저는 캐시의 페이지를 표시합니다. 중요한 정보(주소, 신용 카드 정보, 사회 보장 번호, 사용자 이름)가 사용자에게 표시되는 경우, 정보는 브라우저의 캐시에 저장되고 브라우저의 캐시를 검사하거나 브라우저의 **뒤로** 버튼을 누르기만 하면 검색할 수 있습니다.

## <a name="verification"></a>확인
확인 단계에는 코드가 이전 단계에서 설정된 보안 및 개인 정보 보호 원칙을 충족하는지 확인하기 위한 포괄적인 노력이 포함됩니다.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>응용 프로그램 종속성의 취약점 찾기 및 수정

응용 프로그램 및 해당 종속 라이브러리를 검사하여 알려진 취약한 구성 요소를 식별합니다. 이 검사를 수행할 수 있는 제품에는 [OWASP 종속성 검사,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)및 [Black Duck이](https://www.blackducksoftware.com/)포함됩니다.

[Tinfoil 보안에](https://www.tinfoilsecurity.com/) 의해 구동 되는 취약점 검사 Azure 앱 서비스 웹 애플 리 케이 션에 사용할 수 있습니다. [App Service를 통한 Tinfoil 보안 검색은](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) 악의적인 행위자보다 취약점을 발견하고 해결하는 빠르고 통합된 경제적 수단을 개발자와 관리자에게 제공합니다.

> [!NOTE]
> [Tinfoil 보안을 Azure AD와 통합할](../../active-directory/saas-apps/tinfoil-security-tutorial.md)수도 있습니다. Tinfoil 보안을 Azure AD와 통합하면 다음과 같은 이점을 얻을 수 있습니다.
>  - Azure AD에서 Tinfoil 보안에 액세스할 수 있는 사람을 제어할 수 있습니다.
>  - 사용자는 Azure AD 계정을 사용하여 Tinfoil Security(단일 사인온)에 자동으로 로그인할 수 있습니다.
>  - 단일 중앙 위치인 Azure 포털에서 계정을 관리할 수 있습니다.

### <a name="test-your-application-in-an-operating-state"></a>운영 상태에서 응용 프로그램 테스트

동적 응용 프로그램 보안 테스트(DAST)는 운영 상태에서 응용 프로그램을 테스트하여 보안 취약점을 찾는 프로세스입니다. DAST 도구는 프로그램을 실행하는 동안 실행 중인 프로그램을 분석하여 메모리 손상, 안전하지 않은 서버 구성, 사이트 간 스크립팅, 사용자 권한 문제, SQL 주입 및 기타 중요한 보안 문제와 같은 보안 취약점을 찾습니다.

DAST는 정적 응용 프로그램 보안 테스트(SAST)와 다릅니다. SAST 도구는 보안 결함을 찾기 위해 코드가 실행되지 않을 때 소스 코드 또는 컴파일된 버전의 코드를 분석합니다.

DAST를 수행, 바람직하게는 보안 전문가 [(침투 테스터](../fundamentals/pen-testing.md) 또는 취약점 평가자)의 도움으로. 보안 전문가를 사용할 수 없는 경우 웹 프록시 스캐너 및 일부 교육을 통해 DAST를 직접 수행할 수 있습니다. 코드에 명백한 보안 문제가 발생하지 않도록 DAST 스캐너를 일찍 연결합니다. 웹 응용 프로그램 취약성 스캐너 목록은 [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) 사이트를 참조하십시오.

### <a name="perform-fuzz-testing"></a>퍼지 테스트 수행

[퍼지 테스트에서는](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)의도적으로 잘못된 데이터 또는 임의 데이터를 응용 프로그램에 도입하여 프로그램 오류를 유도합니다. 프로그램 오류를 유도하면 응용 프로그램이 릴리스되기 전에 잠재적인 보안 문제를 알 수 있습니다.

[보안 위험 검색은](https://docs.microsoft.com/security-risk-detection/) 소프트웨어에서 보안에 중요한 버그를 찾기 위한 Microsoft 고유의 퍼지 테스트 서비스입니다.

### <a name="conduct-attack-surface-review"></a>공격 표면 검토 수행

코드 완료 후 공격 표면을 검토하면 응용 프로그램이나 시스템에 대한 모든 디자인 또는 구현 변경 사항을 고려할 수 있습니다. 위협 모델을 포함하여 변경의 결과로 생성된 모든 새 공격 벡터를 검토하고 완화하는 데 도움이 됩니다.

응용 프로그램을 검색하여 공격 표면의 그림을 작성할 수 있습니다. Microsoft는 공격 표면 [분석기라는 공격 표면](https://www.microsoft.com/download/details.aspx?id=24487)분석 도구를 제공합니다. [OWASP Zed 공격 프록시 프로젝트,](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) [아라크니,](http://arachni-scanner.com/) [스킵피쉬](https://code.google.com/p/skipfish/)및 [w3af를](http://w3af.sourceforge.net/)포함한 많은 상용 동적 테스트 및 취약점 검색 도구 또는 서비스에서 선택할 수 있습니다. 이러한 검색 도구는 앱을 크롤링하고 웹을 통해 액세스할 수 있는 응용 프로그램의 일부를 매핑합니다. Azure 마켓플레이스에서 유사한 개발자 도구를 검색할 수도 [있습니다.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>보안 침투 테스트 수행

응용 프로그램의 보안을 보장하는 것은 다른 기능을 테스트하는 것만큼 중요합니다. [침투 테스트를](../fundamentals/pen-testing.md) 빌드 및 배포 프로세스의 표준 부분으로 만듭니다. 배포된 응용 프로그램에서 정기적인 보안 테스트 및 취약성 검색을 예약하고 열린 포트, 끝점 및 공격을 모니터링합니다.

### <a name="run-security-verification-tests"></a>보안 검증 테스트 실행

[Azure(AzSK)에 대한 보안 DevOps 키트에는](https://azsk.azurewebsites.net/index.html) Azure 플랫폼의 여러 서비스에 대한 SVT가 포함되어 있습니다. 이러한 SVT를 주기적으로 실행하여 Azure 구독및 응용 프로그램을 구성하는 다양한 리소스가 안전한 상태에 있는지 확인합니다. 또한 AzSK의 연속 통합/연속 배포(CI/CD) 확장 기능을 사용하여 이러한 테스트를 자동화할 수 있으므로 SVT를 Visual Studio 확장으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 디자인하고 배포하는 데 도움이 되는 보안 제어 및 활동을 권장합니다.

- [안전한 애플리케이션 설계](secure-design.md)
- [보안 애플리케이션 배포](secure-deploy.md)
