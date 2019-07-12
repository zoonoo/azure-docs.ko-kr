---
title: Microsoft Azure에서 보안 응용 프로그램 디자인
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 요구 사항 및 디자인 단계에서 고려해 야 할 모범 사례를 설명 합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653293"
---
# <a name="design-secure-applications-on-azure"></a>Azure에서 보안 응용 프로그램 디자인
이 문서에서는 보안 작업 및 클라우드 용 응용 프로그램을 디자인할 때 고려해 야 할 컨트롤 소개 합니다. 보안 질문 요구 하는 동안 고려할 및 Microsoft의 단계를 디자인 하는 개념와 리소스를 교육 [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 나와 있습니다. 활동 및 보다 안전한 응용 프로그램을 디자인 하는 데 사용할 수 있는 Azure 서비스를 정의 하는 데 목표가입니다.

SDL 단계는이 문서에 적용 됩니다.

- 교육
- 요구 사항
- 디자인

## <a name="training"></a>교육
클라우드 응용 프로그램 개발을 시작 하기 전에 보안 및 Azure에서 개인 정보를 이해 하는 데 시간이 있습니다. 이 단계를 수행 하 여 응용 프로그램에서 악용 가능한 취약점의 심각도 수를 줄일 수 있습니다. 더 끊임없이 변화 하는 위협 환경에 적절 하 게 대응 하도록 준비 됩니다.

Azure에서 보안 모범 사례를 사용 하 여 개발자에 게 사용할 수 있는 Azure 서비스와 기능을 살펴보고 교육 단계 중 다음 리소스를 사용 합니다.

  - [Azure 개발자 가이드](https://azure.microsoft.com/campaigns/developer-guide/) Azure를 사용 하 여 시작 하는 방법을 보여 줍니다. 이 가이드는 응용 프로그램을 실행, 데이터를 저장, 인텔리전스를 통합, IoT 앱을 빌드할 및 더 효율적이 고 안전한 방식으로 솔루션을 배포 하 여 서비스를 보여 줍니다.

  - [Azure 개발자를 위한 시작된 가이드](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) 자신의 개발 요구에 대 한 Azure 플랫폼을 사용 하 여 시작 하려는 개발자를 위한 필수 정보를 제공 합니다.

  - [Sdk 및 도구](https://docs.microsoft.com/azure/index#pivot=sdkstools) Azure에서 사용할 수 있는 도구를 설명 합니다.

  - [Azure DevOps 서비스](https://docs.microsoft.com/azure/devops/) 개발 공동 작업 도구를 제공 합니다. 고성능 파이프라인, 무료 Git 리포지토리, 구성 가능한 Kanban 보드 및 광범위 한 자동화 되 고 클라우드 기반 부하 테스트 도구에 포함 됩니다.
    합니다 [DevOps 리소스 센터](https://docs.microsoft.com/azure/devops/learn/) DevOps 사례, Git 버전 제어, agile 방법, Microsoft의 DevOps 협력 하는 방법 및 사용자 고유의 DevOps 진행 하는 방법을 평가할 수 있습니다 학습 리소스를 결합 합니다.

  - [상위 5 개 보안 항목 프로덕션에 푸시하기 전에 고려해 야 할](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) Azure에서 웹 응용 프로그램을 보호 하 고 가장 일반적이 고 위험한 웹 응용 프로그램 공격에 대 한 앱을 보호 하는 방법을 보여 줍니다.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) 포괄적인 Azure 구독 및 리소스의 보안 요구 한 광범위 한 자동화를 사용 하는 DevOps 팀에 게 제공 하는 스크립트, 도구, 확장 및 자동화의 컬렉션인 경우. Azure에 대 한 Secure DevOps Kit 원활 하 게 하 여 네이티브 DevOps 워크플로에 보안을 통합 하는 방법을 표시할 수 있습니다. 키트 보안 확인 테스트 (SVTs) 개발자가 도움이 될 수 있는 안전한 코드를 작성 하 고 코딩 및 초기 개발 단계에서 클라우드 응용 프로그램의 보안 구성을 테스트와 같은 도구를 해결 합니다.

  - [Azure 솔루션에 대 한 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) 모범 사례를 디자인, 배포 및 Azure를 사용 하 여 클라우드 솔루션을 관리 하는 대로 사용 하 여 보안의 컬렉션을 제공 합니다.

## <a name="requirements"></a>요구 사항
요구 사항 정의 단계는 응용 프로그램 이란 무엇 이며는 어떻게이 릴리스되면 정의에서 중요 한 단계입니다. 요구 사항 단계 응용 프로그램에 빌드하는 것은 보안 제어에 대해 생각 하는 데 시간이 이기도 합니다. 이 단계에서 릴리스 하는 안전한 응용 프로그램을 배포 하도록 SDL에서 수행할 단계를 시작 합니다.

### <a name="consider-security-and-privacy-issues"></a>보안 및 개인 정보 문제를 고려 합니다.
이 단계는 기본 보안 및 개인 정보 보호 문제를 고려 하는 것이 좋습니다. 팀을 통해 허용 가능한 수준의 보안 및 개인 정보는 프로젝트의 시작 부분에 정의 합니다.

- 보안 문제를 사용 하 여 관련 된 위험을 이해 합니다.
- 확인 하 고 개발 하는 동안 보안 버그를 해결 합니다.
- 설정 된 수준의 보안 및 개인 정보 전체 프로젝트 전반에 걸쳐 적용 됩니다.

응용 프로그램에 대 한 요구 사항을 작성 하는 경우 응용 프로그램 및 데이터를 안전 하 게 유지 하는 데 도움이 되는 보안 제어를 고려해 야 합니다.

### <a name="ask-security-questions"></a>보안 질문
다음과 같은 보안 질문을 요청 합니다.

  - 내 응용 프로그램에 중요 한 데이터가 포함 되어 있습니까?

  - 응용 프로그램 수집 또는 업계 표준 및 규정 준수 프로그램 등을 준수 하도록 요구 하는 데이터를 저장 합니다 [Federal 금융 기관 심사 위원회 (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) 또는 [결제 카드 산업 Data Security Standards (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - 내 응용 프로그램 수집 사용할 수 있는 중요 한 개인 또는 고객 데이터가 포함, 자체적으로 또는 다른 정보를 식별 하려면에 문의 하거나 단일 사용자를 찾을?

  - 응용 프로그램 수집 또는 고용 정보나 개인의 의료, 교육, 금융, 액세스를 사용할 수 있는 데이터를 포함할? 요구 사항 단계 데이터의 민감도 식별 하면 데이터를 분류 하 고 응용 프로그램에 사용할 데이터 보호 방법을 식별할 수 있습니다.

  - 위치와 방법을 데이터 저장 되나요? 응용 프로그램 (예: 느린 응답 시간) 예기치 않은 변경 내용을 사용 하는 저장소 서비스 모니터링 하는 방법을 고려해 야 합니다. 보다 자세한 데이터를 수집 하 고 문제를 자세하게 분석 로깅에 영향을 할 수 있습니다?

  - 내 응용 프로그램 또는 됩니다 (인터넷)에서 공개적으로 사용할 수 있는 유일한 내부적으로? 응용 프로그램 공개적으로 사용 가능한 경우을 잘못 된 방식으로 사용 하는 수집 될 수 있는 데이터가 어떻게 보호 합니까? 응용 프로그램은 내부적으로, 조직에서 응용 프로그램에 액세스 해야 하 고 액세스할 수는 얼마나 오래 것이 좋습니다.

  - 응용 프로그램을 디자인 하기 전에 사용자 id 모델 알고 계십니까? 확인 방법을 사용자 본인이 있는 사용자는 사용자는 할 수 있는 권한이 있습니까?

  - 내 응용 프로그램 중요 하거나 중요 한 작업 (예: 자금 이체, 도어를 잠금 해제 또는 의학 제공)를 수행 하나요?
    작업을 수행 하는 중요 한 작업을 수행 하는 사용자 권한이 있는 확인 하는 방법 및을 인증할 방법을 사용자 본인이는 것이 좋습니다. 권한 부여 (인증)는 작업을 수행 하는 인증 된 보안 주체 권한을 부여 하는 행위. (인증) 인증은 합법적인 자격 증명에 대 한 파티 까다로운 작업입니다.

  - 내 응용 프로그램을 업로드 하거나 다운로드할 파일 또는 기타 데이터는 사용자와 같은 모든 위험한 소프트웨어 작업을 수행 하나요? 응용 프로그램은 위험한 작업을 수행 하는 경우에 응용 프로그램 악의적인 파일 또는 데이터 처리에서 사용자는 보호 하는 방법을 하는 것이 좋습니다.

### <a name="review-owasp-top-10"></a>검토 OWASP 상위 10 개
검토할 합니다 [ <span class="underline">OWASP Top 10 응용 프로그램 보안 위험</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)합니다.
OWASP 상위 10 개는 웹 응용 프로그램에 중요 한 보안 위험을 해결합니다.
이러한 보안 위험에 대 한 인식을 응용 프로그램에서 이러한 위험을 최소화 하는 요구 사항 및 디자인 결정을 내릴 수 있습니다.

보안 위반을 방지 하는 컨트롤에 생각 하는 것이 중요 합니다.
그러나 하고자 [침입 가정](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) 발생 합니다. 보안 위험 가정 보안에 대 한 몇 가지 중요 한 질문을 미리 응답는 긴급 상황에서 답변을 얻을 수 있으며:

  - 공격을 감지는 방법

  - 공격 또는 위반 하는 경우 수행 됩니다.

  - 데이터 누출 또는 변조와 같은 공격 으로부터 복구할 이죠?

## <a name="design"></a>디자인

디자인 단계에서이 디자인 및 기능 사양에 대 한 모범 사례를 설정 하는 데 중요 합니다. 또한 반드시 프로젝트 전체에서 보안 및 개인 정보 문제를 줄이는 데 도움이 되도록 위험 분석을 수행 합니다.

곳에서 보안 요구 사항이 보안 디자인 개념을 사용 하는 경우에 방지 하거나 보안 결함에 대 한 기회를 최소화할 수 있습니다. 보안 결함은 사용자 응용 프로그램 출시 되 면 악의적인 하거나 예기치 않은 작업을 수행할 수 있도록 응용 프로그램의 디자인에서 감독 합니다.

디자인 단계 중에 대해서는 고려 계층;의 보안을 적용 하는 방법 한 수준의 defense 반드시 충분 하지 않습니다. 공격자는 웹 응용 프로그램 방화벽 (WAF) 지난 경우 어떻게 되나요? 다른 보안 제어는 공격을 방어 하기 위해 준비에서 해야 합니다.

이 점을 염두에서를 사용 하 여 다음과 같은 보안 디자인 개념 및 보안 응용 프로그램을 디자인할 때 해결 해야 하는 보안 컨트롤을 설명 합니다.

- 보안 코딩 라이브러리 및 소프트웨어 프레임 워크를 사용 합니다.
- 취약 한 구성 요소를 검색 합니다.
- 위협 모델링 과정 응용 프로그램 디자인을 사용 합니다.
- 사용자의 공격 노출 영역을 줄입니다.
- 기본 보안 경계로 서 id의 정책을 채택 합니다.
- 중요 한 트랜잭션에 대 한 다시 인증 해야 합니다.
- 키, 자격 증명 및 기타 비밀을 보호 하는 키 관리 솔루션을 사용 합니다.
- 중요 한 데이터를 보호 합니다.
- 유사 시 대기 조치를 구현 합니다.
- 오류 및 예외 처리의 활용 합니다.
- 로깅 및 경고를 사용 합니다.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>보안 코딩 라이브러리 및 소프트웨어 프레임 워크 사용

개발에 대 한 보안 코딩 라이브러리 및 보안에 포함 된 소프트웨어 프레임 워크를 사용 합니다. 개발자가 사용할 수 기존부터 보안 컨트롤을 개발 하는 대신 기능 (암호화, 예방 조치 입력된, 출력 인코딩, 키 또는 연결 문자열 및 명제는 보안 컨트롤을 고려할 수)를 입증 합니다. 이렇게 하면 보안 관련 디자인 및 구현 결함을 방지할 수 있습니다.

최신 버전의 프레임 워크 및 프레임 워크에서 사용할 수 있는 모든 보안 기능을 사용 하는 확인 해야 합니다. Microsoft에서 제공 하는 포괄적인 [개발 도구 집합](https://azure.microsoft.com/product-categories/developer-tools/) 모든 개발자가 모든 플랫폼 또는 언어에서 작업할 클라우드 응용 프로그램을 제공 합니다. 다양 한 선택 하 여 사용자가 선택한 언어를 사용 하 여 코딩할 수 있습니다 [Sdk](https://azure.microsoft.com/downloads/)합니다.
모든 기능을 갖춘 통합된 개발 환경 (Ide) 이용할 수 있는 고급 디버깅 기능 및 기본 제공 Azure 지원이 있는 및 편집기.

Microsoft는 다양 한 제공 [언어, 프레임 워크 및 도구](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) Azure에서 응용 프로그램 개발에 사용할 수 있습니다. 예로 [.NET 및.NET Core 개발자를 위한 Azure](https://docs.microsoft.com/dotnet/azure/)합니다. 각 언어와 프레임 워크를 제공 하는 빠른 시작, 자습서 및 API 참조를 빠르게 시작할 수 있습니다.

Azure는 다양 한 호스트 웹 사이트와 웹 응용 프로그램에 사용할 수 있는 서비스를 제공 합니다. .NET,.NET Core, Java, Ruby, Node.js, PHP 또는 Python를 인지 하는 이러한 서비스는 원하는 언어로 개발할 수 있습니다.
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (Web Apps)는 이러한 서비스 중 하나입니다.

Web Apps는 응용 프로그램에 Microsoft Azure의 강력한 기능을 추가합니다. 보안, 부하 분산, 자동 크기 조정 및 자동화 된 관리를 포함합니다. 또한 수 있습니다 DevOps 기능을 웹 앱의 패키지 관리와 같은 환경, 사용자 지정 도메인, SSL/TLS 인증서 및 Azure DevOps, GitHub, Docker 허브 및 기타 원본에서 연속 배포를 준비 합니다.

Azure 웹 사이트와 웹 응용 프로그램을 호스트 하는 데 사용할 수 있는 다른 서비스를 제공 합니다. 대부분의 시나리오의 경우 Web Apps를 사용하는 것이 좋습니다. 마이크로 서비스 아키텍처를 사용 하는 것이 좋습니다 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)합니다.
코드가 실행되는 VM을 자세히 제어해야 하는 경우 [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)를 사용하는 것이 좋습니다.
이러한 Azure 서비스 중에서 선택 하는 방법에 대 한 자세한 내용은 참조는 [Azure App Service, Virtual Machines, Service Fabric 및 Cloud Services 비교](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm)합니다.

### <a name="apply-updates-to-components"></a>구성 요소에 업데이트를 적용 합니다.

취약성을 방지 하려면 클라이언트 쪽 및 서버 쪽 구성 요소 (예를 들어, 프레임 워크 및 라이브러리) 및 업데이트에 대 한 해당 종속성을 모두 지속적으로 인벤토리 해야 합니다. 새 취약점 및 업데이트 된 소프트웨어 버전은 지속적으로 해제 됩니다. 모니터링, 심사, 업데이트를 적용 하는 진행 중인 플랜이 라이브러리를 사용 하는 구성 요소 구성 변경 확인 합니다.

참조를 [OWASP Open Web Application Security Project ()](https://www.owasp.org/index.php/Main_Page) 페이지에서 [구성 요소를 사용 하 여 알려진된 취약점을 사용 하 여](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) 도구 제안에 대 한 합니다. 사용할 구성 요소와 관련 된 보안 취약점에 대 한 전자 메일 경고를 구독할 수 있습니다.

### <a name="use-threat-modeling-during-application-design"></a>애플리케이션 디자인 과정에서 위협 모델링 사용

위협 모델링은 비즈니스 및 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 하 고 적절 한 완화 방법을 적절 한지를 확인 한 다음는 프로세스입니다. SDL 팀 위협 모델링 비교적 쉽고 비용 효율적인 경우는 잠재적인 문제를 해결 하는 디자인 단계 과정에 참여 해야를 지정 합니다. 위협 모델링 디자인 단계에서는 개발의 총 비용을 크게 줄일 수 있습니다를 사용 합니다.

위협 모델링 프로세스를 편리 하 게 설계할 합니다 [SDL Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) 염두에서 비보안 전문가 함께 합니다. 이 도구를 통해 위협 모델링 쉽게 모든 개발자를 위한 만들고 위협 모델 분석 하는 방법에 대 한 명확한 지침을 제공 하 여 합니다.

응용 프로그램 디자인을 모델링 하 고 열거 [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) 위협-스푸핑, 변조, 거부, 정보 공개, 서비스 거부 및 권한 상승-모든 트러스트 간에 경계를 효과적으로 입증 된 초기에 디자인 오류를 catch 합니다. 다음 표에서 STRIDE 위협을 목록과 Azure에서 제공 하는 기능을 사용 하는 몇 가지 예제 완화 방법을 제공 합니다. 이러한 완화 방식을 모든 상황에서 사용할 수 있는 것은 아닙니다.

| 위협 | 보안 속성 | Azure 플랫폼의 잠재적인 문제 완화 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 스푸핑               | 인증        | [HTTPS 연결이 필요](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)합니다. |
| 변조              | 무결성             | SSL/TLS 인증서의 유효성을 검사 합니다. SSL/TLS를 사용 하는 응용 프로그램에 연결 하는 엔터티의 X.509 인증서를 완전히 확인 해야 합니다. Azure Key Vault 인증서를 사용 하 여 [x509 관리 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)합니다. |
| 거부            | 거부 없음       | Azure [모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)을 사용하도록 설정합니다.|
| 정보 공개 | 기밀성       | 중요 한 데이터 암호화 [미사용](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) 하 고 [전송에서](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit)합니다. |
| 서비스 거부      | 가용성          | 잠재적 서비스 거부 조건에 대 한 성능 메트릭을 모니터링 합니다. 연결 필터를 구현합니다. [Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps)응용 프로그램 설계 모범 사례와 결합, DDoS 공격 으로부터 방어를 제공 합니다.|
| 권한 상승 | Authorization         | Azure Active Directory를 사용 하 여 <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)합니다.|

### <a name="reduce-your-attack-surface"></a>사용자의 공격 취약 영역 축소

공격 노출 합계는 잠재적 보안 문제가 발생할 수 있는 합계입니다. 이 문서에서는 응용 프로그램의 공격 노출 영역에 초점입니다.
초점은 응용 프로그램의 공격 으로부터 보호 합니다. 하면 공격 노출 영역이 최소화 하는 쉽고 빠른 방법은 응용 프로그램에서 사용 되지 않는 리소스 및 코드를 제거 것입니다. 더 작은 응용 프로그램에 더 작은 하면 공격 노출 영역이 있습니다. 예를 들어 제거 합니다.

- 아직 릴리스되지 않은 기능에 대 한 코드입니다.
- 지원 코드를 디버깅 합니다.
- 네트워크 인터페이스 및 프로토콜 사용 되지 않습니다는 또는 사용 되지 않습니다.
- 가상 컴퓨터 및 사용 하지 않는 다른 리소스입니다.

리소스의 일반 정리를 수행 하 고 사용 되지 않는 코드를 제거 하는 편리 하 게 공격에 대 한 악의적인 행위자에 대 한 가능성을 줄일 수 있는지 확인 하십시오.

프로그램 공격 표면을 줄이기 위해 더 자세 하 고 심도 있는 방식으로 공격 노출 영역 분석 방법은입니다. 공격 노출 영역 분석을 사용 하 여 검토 하 고 보안 취약점에 대 한 테스트 해야 하는 시스템 부분을 매핑할 수 있습니다.

개발자와 보안 전문가 공격에 노출 될 응용 프로그램의 부분을 인식 하므로 응용 프로그램에서 위험 영역을 이해 하는 공격 노출 영역 분석의 목적은입니다. 그런 다음 시기와 방법을 공격 노출 영역이 변경 되 고 부서가 위험 관점에서 즉이 잠재적인, 트랙을 최소화 하는 방법을 찾을 수 있습니다.

공격 노출 영역 분석을 식별할 수 있습니다.

- 함수 및 시스템의 부분을 검토 하 고 보안 취약점에 대 한 테스트 해야 합니다.
- 심층 방어 protection (보호 해야 하는 시스템의 일부)를 필요로 하는 코드의 위험도 높은 영역입니다.
- Alter 공격 및 위협 평가 새로 고쳐야 합니다.

공격자가 잠재적 취약 한 부분 또는 취약성을 악용할 수 있는 기회를 줄이고 응용 프로그램의 전반적인 공격 노출 영역을 철저 하 게 분석 해야 합니다. 사용 가능한 방어를 계층화 하 고 최소 권한의 원칙을 적용 하는 시스템 서비스에 대 한 액세스를 제한 하거나 사용 하지 않도록 설정도 포함 합니다.

논의 [공격 노출 검토를 수행](secure-develop.md#conduct-attack-surface-review) SDL의 확인 단계 동안.

> [!NOTE]
> **위협 모델링 및 공격 노출 영역 분석 간의 차이 무엇입니까?**
위협 모델링은 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 하 고 위협에 대 한 적절 한 완화 방법을 적절 한지 확인 과정입니다. 공격 노출 영역 분석 공격에 개방 된 코드의 위험도 높은 영역을 식별 합니다. 여기에 응용 프로그램 검토 및 응용 프로그램을 배포 하기 전에 코드의 해당 영역을 테스트 위험도 높은 영역은 방어 하는 방법을 찾는 포함 됩니다.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>기본 보안 경계로서의 ID 정책 채택

클라우드 응용 프로그램을 디자인할 때 보안 경계 집중 네트워크 중심 접근 방식에서 id 중심 접근 방식을 확장 하는 것이 중요 합니다. 지금까지 기본 온-프레미스 보안 경계에 있는 조직의 네트워크 했습니다. 대부분의 온-프레미스 보안 설계를 기본 보안 피벗으로 네트워크를 사용합니다. 클라우드 응용 프로그램에 대 한 향상 된 기본 보안 경계로 서의 id를 고려 하 여 제공 됩니다.

작업 웹 응용 프로그램을 개발 하는 id 중심 접근 방식을 개발을 수행할 수 있습니다.

- 사용자에 대 한 multi-factor authentication을 적용 합니다.
- 강력한 인증 및 권한 부여 플랫폼을 사용합니다.
- 최소 권한의 원칙을 적용 합니다.
- Just in time 액세스를 구현 합니다.

#### <a name="enforce-multi-factor-authentication-for-users"></a>사용자에 대 한 multi-factor authentication 적용

따라서 2단계 인증을 사용해야 합니다. 다단계 인증은 인증 및 권한 부여에 대 한 현재 표준 인증의 사용자 이름 및 암호 형식에 고유한 보안 약점을 방지 하기 때문에입니다. 고객 관련 서비스 및 Azure 관리 인터페이스 (Azure 포털/원격 PowerShell)에 대 한 액세스를 설계 하 고 사용 하도록 구성 해야 [Azure Multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)합니다.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>강력한 인증 및 권한 부여 플랫폼을 사용 하 여

사용자 지정 코드 대신 플랫폼에서 제공하는 인증 및 권한 부여 메커니즘을 사용합니다. 즉, 사용자 지정 인증 코드 개발 오류가 발생할 수 있습니다. 상용 코드 (예: Microsoft)에서 종종 보안 검토 광범위 하 게 됩니다. [Azure AD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 ID 및 액세스 관리를 위한 Azure 솔루션입니다. 이러한 Azure AD 도구 및 서비스 보안 개발 있습니다.

- [Azure AD id 플랫폼과 (개발자를 위한 Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) 는 개발자가 사용자를 안전 하 게 로그인 하는 앱을 작성 하는 데 사용할 클라우드 id 서비스입니다. Azure AD 테 넌 트 단일 기간 업무 (LOB) 앱을 빌드하는 개발자와 다중 테 넌 트 앱을 개발 하려는 개발자를 지원 합니다. 기본 로그인 외에도 Azure AD를 사용 하 여 빌드된 앱 Microsoft Api 및 Azure AD 플랫폼에서 빌드된 사용자 지정 Api를 호출할 수 있습니다. Azure AD id 플랫폼과 OAuth 2.0 및 OpenID Connect와 같은 업계 표준 프로토콜을 지원합니다.

- [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) 는 사용자 지정 및 고객이 가입 하는 방법을 제어 하 고, 로그인 하 고 응용 프로그램을 사용할 경우 해당 프로필을 관리 하 여 id 관리 서비스입니다. 다른 iOS, Android 및.NET에 대 한 개발 된 응용 프로그램 포함 됩니다. Azure AD B2C는 고객 id를 보호 하는 동안 이러한 작업을 수 있습니다.

#### <a name="apply-the-principle-of-least-privilege"></a>최소 권한의 원칙 적용

개념이 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 정확 하 게 업무 등 아무 작업도 수행에 필요한 액세스 및 제어 수준을 사용자에 게 제공할 것을 의미 합니다.

소프트웨어 개발자는 도메인 관리자 권한이 필요? 관리 보조자 개인용 컴퓨터에 관리 컨트롤에 대 한 액세스 해야? 소프트웨어에 대 한 액세스를 평가 하는 것은 다르지 않습니다. 사용 하는 경우 [역할 기반 액세스 제어 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 사용자가 다양 한 기능 및 기관에서 응용 프로그램에 부여 하려면 모든 사용자 제공 하지 않습니다 하 모든 항목에 대 한 액세스. 각 역할에 필요한 것에 대 한 액세스를 제한 하 여 보안 문제 발생의 위험을 줄이고 있습니다.

응용 프로그램에 적용 되도록 [최소 권한](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) 전체 액세스 패턴에 해당 합니다.

> [!NOTE]
>  최소 권한의 규칙 및 소프트웨어를 만드는 사람에 게 소프트웨어에 적용 해야 합니다. 너무 많은 액세스 권한을 제공 되는 경우 소프트웨어 개발자가 IT 보안에 큰 위험을 수 있습니다. 결과 개발자 악의적인 의도가 없거나 너무 많은 액세스를 부여 하는 경우 심각 해질 수 있습니다. 개발 수명 주기 동안 개발자에 게 최소한의 권한 규칙이 적용 되어야 하는 것이 좋습니다.

#### <a name="implement-just-in-time-access"></a>Just in time 액세스를 구현 합니다.

구현 *-just-in-time* 추가로 권한의 노출 시간을 줄이고에 대 한 (JIT) 액세스 합니다. 사용 하 여 [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) 에:

- 사용자만 JIT 필요한 권한만 제공 합니다.
- 권한을 자동으로 해지하는 짧은 기간 동안 자신 있게 역할을 할당합니다.

### <a name="require-re-authentication-for-important-transactions"></a>중요 한 트랜잭션에 대 한 다시 인증을 요구 합니다.

[교차 사이트 요청 위조](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (라고도 *XSRF* 하거나 *CSRF*),이는 악의적인 웹 앱에 영향을 줍니다 클라이언트 브라우저와 웹 간의 상호 작용 하는 웹 호스팅 앱 공격 해당 브라우저에서 신뢰 하는 앱입니다. 웹 브라우저는 웹 사이트에 몇 가지 유형의 인증 토큰이 모든 요청에 자동으로 보내기 때문에 교차 사이트 요청 위조 공격 있을 수 있습니다.
악용이 폼은 라고도 *원클릭 공격* 또는 *세션 있는* 공격을 활용 하기 때문에 사용자의 이전 세션을 인증 합니다.

이러한 종류의 공격을 방어 하기 구매, 계정 비활성화 또는 암호 변경과 같은 중요 한 모든 트랜잭션을 하기 전에 사용자만 제공할 수 있는 항목에 대 한 사용자에 게 요청 하는 가장 좋은 방법은 합니다. 자신의 암호를 다시 입력 captcha를 완료 하려면 사용자에 게 요청 하거나 사용자만 포함 하는 보안 토큰을 제출 수 있습니다. 가장 일반적인 방법은 비밀 토큰입니다.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>키, 자격 증명 및 기타 비밀을 보호 하는 키 관리 솔루션을 사용 합니다.

키와 자격 증명을 잃어 버리는 것은 일반적인 문제입니다. 키 및 자격 증명을 분실하는 것보다 더 위험한 상황은 권한이 없는 사람이 해당 정보에 액세스하는 것뿐입니다. 공격자는 키 및 GitHub와 같은 코드 리포지토리에 저장 된 비밀을 검색할 자동 및 수동 기술 활용을 걸릴 수 있습니다. 이러한 공용 코드 리포지토리 또는 다른 서버 키와 비밀을 넣지 마세요.

키 관리 솔루션에서 키, 인증서, 암호 및 연결 문자열을 항상 입력 됩니다. 키 및 비밀을 Hsm (하드웨어 보안 모듈)에 저장 되는 중앙 집중식된 솔루션을 사용할 수 있습니다. Azure는 클라우드로 HSM을 사용 하 여 제공 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)합니다.

Key vault를 *비밀 저장소*: 중앙 집중화 된 클라우드 서비스 응용 프로그램 비밀을 저장 하기 위한 것입니다. Key Vault를 하나의 중앙 위치에서 응용 프로그램 비밀을 유지 하 고 안전 하 게 액세스, 사용 권한 제어 및 액세스 로깅을 제공 하 여 안전 하 게 기밀 데이터를 유지 합니다.

비밀은 개별에 저장 *자격 증명 모음*합니다. 각 자격 증명 모음에 자체 구성 및 액세스를 제어 하는 보안 정책을 있습니다. REST API를 통해 또는 클라이언트 대부분의 프로그래밍 언어에 사용할 수 있는 SDK 통해 데이터를 얻게 됩니다.

> [!IMPORTANT]
> Azure Key Vault는 서버 응용 프로그램에 대 한 구성 암호를 저장 하도록 설계 되었습니다. 앱 사용자에 게 속한 데이터를 저장 하는 데는 것이 아닙니다. 이 해당 성능 특성, API 및 비용 모델에 반영 됩니다.
>
> Transparent Data Encryption (TDE)에 Azure SQL Database 인스턴스 또는 Azure Storage 서비스 암호화를 사용 하는 저장소 계정에 같은 사용자 데이터를 다른 곳에 저장 되어야 합니다. Azure Key Vault에 응용 프로그램에서 이러한 데이터 저장소에 액세스 하는 데 사용 되는 암호를 유지할 수 있습니다.

### <a name="protect-sensitive-data"></a>중요 한 데이터 보호

데이터 보호는 보안 전략의 필수적인 부분입니다.
데이터를 분류 하 고 데이터 보호를 식별 사용 하면 데이터 보안을 염두에 앱을 디자인 해야 합니다. 구분 하 여 저장 된 데이터 분류 (분류) 및 비즈니스 영향 데이터와 연결 된 위험을 확인 하는 개발자는 데 도움이 됩니다.

데이터 형식을 디자인할 때 중요 한 정보로 모든 해당 데이터를 레이블입니다. 응용 프로그램이 중요 한 정보로 해당 데이터를 처리 한다는 것을 확인 합니다. 이러한 사례는 중요 한 데이터를 보호할 수 있습니다.

- 암호화를 사용 합니다.
- 키 및 암호와 같은 비밀을 하드 코딩 하지 마세요.
- 액세스 제어 및 감사 진행에서 되는지 확인 합니다.

#### <a name="use-encryption"></a>암호화 사용

데이터를 보호 해야 보안 전략의 필수적인 부분입니다.
암호화를 사용 하 여 데이터를 데이터베이스에 저장 된 경우 또는 위치 간에 앞뒤로 이동 하는 경우 [미사용 데이터](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (데이터베이스에 있는 동안) 및 암호화 [전송 중인 데이터](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (월호가 사용자의 데이터베이스, API 또는 서비스 끝점). 항상 SSL/TLS 프로토콜을 사용 하 여 데이터를 교환 하는 것이 좋습니다. 암호화에 대 한 최신 버전의 TLS 사용 해야 합니다 (현재, 이것은 버전 1.2).

#### <a name="avoid-hard-coding"></a>하드 코딩 방지

일부의 소프트웨어에서 하드 코드 된 되어서는 안 됩니다. 예로 호스트 이름 또는 IP 주소, Url, 전자 메일 주소, 사용자 이름, 암호, 저장소 계정 키 및 다른 암호화 키에는 있습니다. 수 또는 코드의 주석 섹션에 포함 하 여 코드에 하드 코딩 될 수 없습니다. 새로운 요구 사항을 구현 하는 것이 좋습니다.

코드에 메모를 배치 하면 중요 한 정보를 저장 하지 않는 확인 합니다. 여기에 전자 메일 주소, 암호, 연결 문자열, 응용 프로그램 정보는만 조직 및 다른 사용자가 알고 있어야 하는 장점은 응용 프로그램 또는 조직 공격에서 공격자를 지정할 수 있습니다. .

기본적으로, 된다고 가정 합니다.는 개발 프로젝트의 모든 공용 기술 배포 될 때입니다. 프로젝트에 어떤 종류의 중요 한 데이터를 포함 하지 마세요.

앞에서 설명한 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)합니다. 키 및 하드 코딩 하는 대신 암호와 같은 비밀 정보를 저장할 Key Vault를 사용할 수 있습니다. 관리 되는 id와 함께에서 Key Vault를 사용 하 여 Azure 리소스에 대 한 Azure 웹 앱에 소스 제어에 구성 된 비밀을 저장 하지 않고 비밀 구성 값 쉽고 안전 하 게 액세스할 수 있습니다. 자세한 내용은 참조 하세요 [Azure Key Vault를 사용 하 여 서버 앱의 비밀 관리](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)합니다.

### <a name="implement-fail-safe-measures"></a>유사 시 대기 조치 구현

응용 프로그램이 처리할 수 있어야 [오류](https://docs.microsoft.com/dotnet/standard/exceptions/) 일관 된 방식에서으로 실행 중에 발생 하는 합니다. 응용 프로그램 모두를 catch 해야 합니다 또는 닫힌 오류 및 하거나 실패 합니다.

또한 악성 또는 의심 스러운 활동을 식별 하도록 충분 한 사용자 컨텍스트를 사용 하 여 오류가 기록 않았는지 확인 해야 합니다. 지연 된 법정 분석을 허용 하도록 충분 한 시간에 대 한 로그를 보관 해야 합니다. 로그 로그 관리 솔루션으로 쉽게 사용할 수 있는 형식 이어야 합니다. 보안과 관련 된 오류에 대 한 경고는 트리거한 확인 합니다. 부족 하 여 로깅 및 모니터링에 공격자가 추가로 시스템을 공격 하 고 지 속성을 유지 하도록 허용 합니다.

### <a name="take-advantage-of-error-and-exception-handling"></a>오류 및 예외 처리를 활용

올바른 오류를 구현 하 고 [예외 처리](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) 방어 코딩의 중요 한 부분입니다. 오류 및 예외 처리는 안정적이 고 안전한 시스템에 중요 한입니다. 오류 처리에 실수는 다양 한 유형의 공격자에 게 정보 누수가 발생 하 고 플랫폼 및 디자인에 대 한 자세한 정보를 이해 하는 공격자가 같은 보안 취약점을 발생할 수 있습니다.

다음 사항을 확인합니다.

- 중앙 집중식으로 방지 하려면 예외를 처리 중복 [try/catch 블록](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) 코드에서입니다.

- 모든 예기치 않은 동작이 응용 프로그램 내에서 처리 됩니다.

- 사용자에 게 표시 되는 메시지는 중요 한 데이터 누출 하지 않습니다 하지만 문제를 설명 하는 데 충분 한 정보 제공 합니다.

- 예외가 기록 및 조사 포렌식 스 또는 인시던트 응답 팀을 위한 충분 한 정보를 제공 합니다.

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) 에 대 한 최고 수준의 환경을 제공 [오류 및 예외 처리](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) 종속 시스템으로 인해 발생 하는 합니다. Logic Apps 작업 및 기업 및 조직에서 앱, 데이터, 시스템 및 서비스를 통합 하는 프로세스를 자동화 하는 워크플로 만드는 데 사용할 수 있습니다.

### <a name="use-logging-and-alerting"></a>사용 하 여 로깅 및 경고

[로그](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) 보안 조사에 대 한 보안 문제 및 문제에 대 한 적절 한 시기에 알고 있어야 하는 사람들을 확인 하는 문제에 대 한 경고를 트리거합니다. 모든 구성 요소에 감사 및 로깅을 사용하도록 설정합니다. 감사 로그는 사용자 컨텍스트를 캡처 하 고 모든 중요 한 이벤트를 식별 해야 합니다.

사이트에 사용자가 제출 하는 중요 한 데이터를 기록 하지 확인 합니다. 중요한 데이터의 예는 다음과 같습니다.

- 사용자 자격 증명
- 사회 보장 번호 또는 기타 식별 정보
- 신용 카드 번호 또는 기타 금융 정보
- 의료 정보
- 개인 키 또는 암호화 된 정보를 해독 하는 다른 데이터
- 애플리케이션을 보다 효과적으로 공격하는 데 용할 수 있는 시스템 또는 애플리케이션 정보

응용 프로그램 성공 및 실패 한 사용자 로그인, 암호 재설정, 암호 변경, 계정 잠금 및 사용자 등록 등 사용자 관리 이벤트를 모니터링 하 고 있는지 확인 합니다. 이러한 이벤트에 대 한 로깅을 사용 하 여 검색 하 고 잠재적으로 의심 스러운 동작에 대응할 수 있습니다. 또한 응용 프로그램에 액세스 하는 등의 작업 데이터를 수집할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 권장 보안 제어 및 도움이 될 수 있는 활동을 개발 및 보안 응용 프로그램을 배포 합니다.

- [안전한 응용 프로그램 개발](secure-develop.md)
- [안전한 응용 프로그램 배포](secure-deploy.md)
