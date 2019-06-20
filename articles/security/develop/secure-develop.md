---
title: Microsoft Azure에서 보안 응용 프로그램 개발
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 구현 및 검증 단계 고려해 야 할 모범 사례를 설명 합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: bcd66d1a8077b4cc87c184f34b43cc5846a83f2f
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144426"
---
# <a name="develop-secure-applications-on-azure"></a>Azure에서 보안 애플리케이션 개발
이 문서에서는 보안 작업 및 클라우드 용 응용 프로그램을 개발할 때 고려해 야 할 컨트롤 소개 합니다. 보안 질문 및 개념의 Microsoft 구현 및 검증 단계 중 고려해 야 [수명 주기 SDL (Security Development)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 나와 있습니다. 활동 및 보다 안전한 응용 프로그램을 개발 하는 데 사용할 수 있는 Azure 서비스를 정의 하는 데 목표가입니다.

SDL 단계는이 문서에 적용 됩니다.

- 구현
- 확인

## <a name="implementation"></a>구현
구현 단계 포커스가 초기 방지에 대 한 모범 사례를 설정 하 고을 검색 하 고 코드에서 보안 문제를 제거 합니다.
응용 프로그램 사용할 수 있도록 의도 하지 않은 방식으로 사용할를 가정 합니다. 이 기능을 사용 하면 응용 프로그램의 실수로 또는 의도적으로 악용 으로부터 보호할 수 있습니다.

### <a name="perform-code-reviews"></a>코드 검토를 수행 합니다.

코드를 체크 인하기 전에 수행 [코드 검토](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) 전반적인 코드 품질을 향상 및 버그를 만들 위험이 줄어듭니다. 사용할 수 있습니다 [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) 코드 검토 프로세스를 관리 합니다.

### <a name="perform-static-code-analysis"></a>정적 코드 분석 수행

[정적 코드 분석](https://www.owasp.org/index.php/Static_Code_Analysis) (라고도 *소스 코드 분석*)는 일반적으로 코드 검토의 일환으로 수행 됩니다. 정적 코드 분석을 일반적으로 정적 코드 분석 도구와 같은 기술을 사용 하 여 실행 되지 않는 코드에서 잠재적인 취약점을 찾으려는 실행 가리킵니다 [검사가](https://en.wikipedia.org/wiki/Taint_checking) 하 고 [데이터 흐름 분석](https://en.wikipedia.org/wiki/Data-flow_analysis)합니다.

Azure Marketplace 제품 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) 정적 코드 분석을 수행 하 고 코드 검토를 사용 하 여 지원 합니다.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>유효성 검사 및 응용 프로그램에 대 한 모든 입력을 삭제

가장 일반적인 웹 응용 프로그램 취약성 으로부터 응용 프로그램을 보호 하기 위해 신뢰할 수 없는 모든 입력을 처리 합니다. 신뢰할 수 없는 데이터는 삽입 공격 수단입니다. API 또는 데이터베이스에서 데이터는 사용자 로부터 입력 URL에 매개 변수를 포함 하는 응용 프로그램에 대 한 입력 및 잠재적으로 조작할 수 있는 사용자는 전달 되는 아무 것도 합니다. 응용 프로그램 해야 [유효성 검사](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) 응용 프로그램 등 사용자에 게 표시 하는 어떤 방식으로든에서 데이터를 사용 하기 전에 데이터는 구문 및 의미 체계가 잘못 되었습니다.

올바르게 형성 된 데이터만 워크플로 입력을 확인 하는 데이터 흐름의 초기 입력의 유효성을 검사 합니다. 잘못 된 데이터가 데이터베이스에 유지 하거나 다운스트림 구성 요소에서 오류를 트리거하지 하지 않으려고 합니다.

Blacklisting 및 허용 목록 두 가지 일반 방법 입력된 구문 유효성 검사를 수행 합니다.

  - 차단 목록 지정 된 사용자 입력을 "악성으로 알려진된" 콘텐츠를 포함 되어 있지 않은지를 확인 하려고 합니다.

  - 허용 목록 작성 "으로 성공한" 입력 집합을 지정 된 사용자 입력에 일치 하는지 확인 하려고 합니다. 문자 기반 허용 목록을 응용 프로그램 사용자 입력에만 "알려진된 양호한" 문자가 포함 되어 있는지 또는 알려진된 형식으로 입력에 일치 하는지 확인 하는 위치는 형태의 허용 목록입니다.
    예를 들어, 정확히 두 개의 숫자가 포함 되어 있는지 또는 사용자 이름에 영숫자 문자만 포함 되어 있는지 확인이 포함 될 수 있습니다.

허용 목록에 추가 안전한 소프트웨어 구축을 위한 기본 방법입니다.
블랙 리스트는 잠재적으로 잘못 된 입력의 전체 목록은 생각 하는 것이 불가능 하기 때문에 오류가 발생 하기 쉽습니다.

서버에서 클라이언트 쪽에 없는 (또는 서버 및 클라이언트 쪽)이이 작업을 수행 합니다.

### <a name="verify-your-applications-outputs"></a>응용 프로그램의 출력 확인

시각적으로 또는 문서 내의 제공 하는 모든 출력 인코딩 고 항상 이스케이프 해야 합니다. [이스케이프](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)이 라고도 *출력 인코딩*, 신뢰할 수 없는 데이터는 삽입 공격에 대 한 차량 없습니다 수 있도록 하는 데 사용 됩니다. 이스케이프 처리를 데이터 유효성 검사와 함께 전체적으로 시스템의 보안 강화 하기 위해 계층화 된 방어책을 제공 합니다.

모든 항목이로 표시 됩니다 이스케이프 되었는지 확인 *출력 합니다.* 또한 이스케이프 인터프리터를 데이터를 실행 하는 데 사용할 수 없습니다 및 작업에서 공격을 방지 하는이 알 수 있습니다. 이 호출 하는 또 다른 일반적인 공격 기법 *사이트 간 스크립팅* (XSS).

타사에서 웹 프레임 워크를 사용 하는 경우에 출력 웹 사이트에서 사용 하 여 인코딩 옵션을 확인할 수 있습니다 합니다 [OWASP XSS 방지 치트 시트](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)합니다.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>데이터베이스에 문의할 때 매개 변수가 있는 쿼리를 사용 하 여

코드에서 "즉석" 인라인 데이터베이스 쿼리를 만들지 하 고 데이터베이스에 직접 보냅니다. 응용 프로그램에 삽입 된 악성 코드가 도난, 초기화 또는 수정할 데이터베이스를 일으킬 수 있습니다. 응용 프로그램 데이터베이스를 호스팅하는 운영 체제에서 악의적인 운영 체제 명령을 실행 하도 사용할 수 있습니다.

대신, 매개 변수가 있는 쿼리 또는 저장된 프로시저를 사용 합니다. 매개 변수가 있는 쿼리를 사용 하는 경우에 코드에서 안전 하 게 프로시저를 호출 하 고 쿼리 문의 일부로 간주 됩니다는 걱정 하지 않고 문자열을 전달할 수 있습니다.

### <a name="remove-standard-server-headers"></a>표준 서버 헤더 제거

헤더 X-기반-에서 서버 등의 X-AspNet-버전 서버 및 기본 기술에 대 한 정보를 표시 합니다. 응용 프로그램의 지문 인식을 방지 하려면 이러한 헤더를 표시 하지 않는 것이 좋습니다.
참조 [Azure websites에서 표준 서버 헤더 제거](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)합니다.

### <a name="segregate-your-production-data"></a>프로덕션 데이터를 분리 합니다.

프로덕션 데이터 인지 또는 "실제" 데이터에 해서는 안 개발, 테스트 또는 비즈니스 의도 보다 다른 목적에 대 한 합니다. 마스크는 ([익명화](https://en.wikipedia.org/wiki/Data_anonymization)) 모든 개발 하는 데 사용 하 고 테스트 데이터 집합 이어야 합니다.

즉, 더 적은 직원 하면 공격 노출 영역이 줄어듭니다 실제 데이터에 액세스할 수 있습니다. 즉, 더 적은 직원 기밀성에서 잠재적 위반을 제거 하는 개인 데이터를 참조 하세요.

### <a name="implement-a-strong-password-policy"></a>강력한 암호 정책을 구현합니다

무차별 대입 및 사전 기반 추측을 방어, 사용자가 만든 (예: 최소 12 자 및 영숫자 및 특수 문자 필요) 하는 복잡 한 암호를 확인 하는 강력한 암호 정책을 구현 해야 합니다.

암호 정책을 만들고 적용 하는 id 프레임 워크를 사용할 수 있습니다. Azure AD B2C를 사용 하면 암호 관리를 사용 하 여 함으로써 [기본 제공 정책을](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy)를 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr), 등입니다.

기본 계정에 대 한 공격을 방어 하려면 모든 키와 암호는 교체 될 수 및 생성 되거나 리소스를 설치한 후 대체 하는 확인 합니다.

응용 프로그램은 암호 자동 생성 해야, 하는 경우에 생성 된 암호는 임의적 이며 높은 엔트로피 있는지 확인 합니다.

### <a name="validate-file-uploads"></a>파일 업로드 유효성 검사

응용 프로그램 허용 하는 경우 [파일 업로드](https://www.owasp.org/index.php/Unrestricted_File_Upload),이 위험한 작업을 수행할 수 있는 예방 조치는 것이 좋습니다. 많은 공격의 첫 번째 단계 공격을 받고 하는 시스템에 일부 악성 코드를 가져오는 것입니다. 파일 업로드를 사용 하 여 공격자에 게가 작업을 수행할 수 있습니다. OWASP는 업로드 중인 파일을 안전 하 게 되도록 파일의 유효성 검사에 대 한 솔루션을 제공 합니다.

맬웨어 방지 보호 식별 하 고 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 제거할 수 있습니다. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) 또는 Microsoft 파트너의 엔드포인트 보호 솔루션([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 및 [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))을 설치할 수 있습니다.

[Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware) 등 실시간 보호, 예약 된 검색, 맬웨어 치료, 서명 업데이트, 엔진 업데이트, 샘플 보고, 제외 이벤트 컬렉션 기능이 포함 되어 있습니다. 배포 및 기본 제공 검색(경고 및 인시던트)을 용이하게 하기 위해 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)와 Microsoft 맬웨어 방지 프로그램 및 파트너 솔루션을 통합할 수 있습니다.

### <a name="dont-cache-sensitive-content"></a>중요 한 콘텐츠를 캐시 하지 마십시오

브라우저에서 중요 한 콘텐츠를 캐시 하지 마십시오. 브라우저는 캐싱 및 기록에 대 한 정보를 저장할 수 있습니다. 캐시 된 파일, Internet Explorer의 경우 임시 인터넷 파일 폴더와 같은 폴더에 저장 됩니다. 이러한 페이지를 브라우저로 다시 참조 되는 경우 해당 캐시에서 페이지를 표시 합니다. 중요 한 정보 (예: 주소, 신용 카드 세부 정보, 주민 등록 번호, 사용자 이름)은 사용자에 게 표시 되 면 정보를 브라우저의 캐시에 저장 될 수 있습니다 하 고 브라우저의 캐시를 검사 해 또는 간단히 눌러 브라우저의 절대로검색할수 **다시** 단추입니다.

## <a name="verification"></a>확인
확인 단계에는 코드를 이전 단계에서 설정 된 보안 및 개인 정보 원칙을 충족 하는지 확인 하는 포괄적인 노력 포함 됩니다.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>취약점 찾기 및 수정에 응용 프로그램 종속성

모든 알려진된 취약 한 구성 요소를 식별 하는 응용 프로그램 및 해당 종속 라이브러리를 검색 합니다. 이 검색 하는 데 사용할 수 있는 제품에 포함 됩니다 [OWASP 종속성 확인](https://www.owasp.org/index.php/OWASP_Dependency_Check)하십시오[Snyk](https://snyk.io/), 및 [Black Duck](https://www.blackducksoftware.com/)합니다.

대 중적으로 취약성 스캔 [Tinfoil Security](https://www.tinfoilsecurity.com/) Azure App Service Web Apps에서 사용할 수 있습니다. [App Service를 통해 tinfoil Security 스캔](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) 개발자와 관리자가 검색 하 고 악의적인 행위자가 혜택을 얻을 수 전에 취약점으로 인 한 주소 지정의 통합 된 빠르고 경제적인 수단을 제공 합니다.

> [!NOTE]
> 할 수도 있습니다 [Tinfoil Security를 Azure AD와 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial)합니다. Tinfoil Security를 Azure AD와 통합 하면 다음과 같은 이점이 제공.
>  - Azure AD에서 Tinfoil Security에 대 한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
>  - 사용자가 수 자동으로 로그인 (single sign on) Tinfoil Security에 해당 Azure AD 계정을 사용 하 여 합니다.
>  - 단일 중앙 위치에서 Azure portal에서 계정을 관리할 수 있습니다.

### <a name="test-your-application-in-an-operating-state"></a>운영 상태에서 응용 프로그램을 테스트 합니다.

동적 응용 프로그램 보안 테스트 (DAST) 운영 상태로 보안 취약점을 찾기 위해 응용 프로그램을 테스트 하는 프로세스입니다. 메모리 손상, 안전 하지 않은 서버 구성, 교차 사이트 스크립팅, 사용자 권한 문제, SQL 삽입 및 기타 중요 한 보안 문제 등 보안 취약점을 찾기 위해 실행 하는 동안 DAST 도구 프로그램을 분석 합니다.

DAST 정적 응용 프로그램 보안 테스트 (SAST)와 다릅니다. SAST 도구 소스 코드 분석 또는 컴파일된 코드의 버전 코드는 보안 결함을 찾기 위해 실행할 수 없습니다.

보안 전문가의 지원을 받아 DAST, 가급적 수행 (을 [침투 테스터로](https://docs.microsoft.com/azure/security/azure-security-pen-testing) 또는 취약점으로 인 한 assessor). 보안 전문가 사용할 수 없는 경우 직접 수행할 수 있습니다 DAST 웹 프록시 스캐너와 몇 가지 학습 합니다. 초기에 코드에 확실 한 보안 문제를 소개 하지는 확인을 DAST 스캐너에 연결 합니다. 참조 된 [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) 웹 응용 프로그램 취약점 스캐너의 목록에 대 한 사이트입니다.

### <a name="perform-fuzz-testing"></a>퍼지 테스트를 수행 합니다.

[퍼지 테스트](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)를 의도적으로 응용 프로그램에 잘못 되었거나 임의 데이터를 도입 하 여 프로그램 오류를 유도 합니다. 프로그램 오류를 유도 응용 프로그램 해제 되기 전에 잠재적인 보안 문제를 표시 하는 데 도움이 됩니다.

[보안 위험 감지](https://docs.microsoft.com/security-risk-detection/) 은 Microsoft 고유 퍼지 소프트웨어의 보안에 중요 한 버그를 찾기 위한 서비스를 테스트 합니다.

### <a name="conduct-attack-surface-review"></a>공격 노출 검토 수행

코드 완성 기능 응용 프로그램을 변경 하는 모든 디자인 또는 구현 하거나 시스템을 고려 했습니다. 확인 후 공격 노출 영역을 검토 합니다. 위협 모델을 비롯 한 변경의 결과로 생성 된 모든 새로운 공격 벡터가 검토 되었으며 완화는 확인 수 있습니다.

응용 프로그램을 검색 하 여 공격 노출 영역에 대 한 그림을 빌드할 수 있습니다. Microsoft는 호출 하는 공격 노출 분석 도구를 제공 [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487)합니다. 많은 상용 동적 테스트 및 취약성 검색 도구 또는 포함 하 여 서비스에서 선택할 수 있습니다 [OWASP 제 드 공격 프록시 프로젝트](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/)하십시오 [Skipfish](http://code.google.com/p/skipfish/), 및 [w3af](http://w3af.sourceforge.net/)합니다. 이러한 검사 도구는 앱을 탐색 하 고 웹을 통해 액세스할 수 있는 응용 프로그램 부분을 매핑합니다. Azure Marketplace를 검색할 수도 있습니다와 비슷한 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)합니다.

### <a name="perform-security-penetration-testing"></a>보안 침투 테스트 수행

응용 프로그램 보안을 보장 하는 것은 다른 기능을 테스트 하는 만큼 중요 합니다. 확인 [침투 테스트](https://docs.microsoft.com/azure/security/azure-security-pen-testing) 빌드 및 배포 프로세스의 표준 부분입니다. 기본 보안 테스트 및 배포 된 응용 프로그램에 대 한 취약성 검사를 예약 하 고 열린 포트, 끝점 및 공격에 대 한 모니터링 합니다.

### <a name="run-security-verification-tests"></a>보안 확인 테스트를 실행 합니다.

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) SVTs Azure 플랫폼의 여러 서비스에 포함 되어 있습니다. Azure 구독 및 응용 프로그램을 구성 하는 다양 한 리소스 보안 상태에 있는지 확인 하려면 주기적으로 이러한 SVTs 실행 합니다. 또한 AzSK SVTs를 Visual Studio 확장으로 사용할 수 있도록 지속적인 통합/지속적인 배포 (CI/CD) 확장 기능을 사용 하 여 이러한 테스트를 자동화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 제어 것이 좋습니다 도움이 될 수 있는 활동 디자인 및 보안 응용 프로그램을 배포 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [안전한 응용 프로그램 배포](secure-deploy.md)
