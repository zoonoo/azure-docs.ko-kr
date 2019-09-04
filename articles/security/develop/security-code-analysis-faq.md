---
title: Microsoft 보안 코드 분석 설명서 FAQ
description: 이 문서에는 Microsoft 보안 코드 분석 확장 프로그램에 대 한 FAQ가 포함 되어 있습니다.
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 846f0ecdd49fc1c501893209b60fa9acc8a32ed2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242326"
---
# <a name="frequently-asked-questions"></a>질문과 대답
질문이 있나요? 자세한 내용은 다음 FAQ를 확인 하세요.

## <a name="general-faq"></a>일반 FAQ

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Azure DevOps 인스턴스 대신 Visual Studio Team Foundation Server 인스턴스에 확장을 설치할 수 있나요?

아니요. Visual Studio Team Foundation Server의 다운로드 및 설치에는 확장을 사용할 수 없습니다.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>빌드를 사용 하 여 Microsoft 보안 코드 분석을 실행 해야 하나요? 

아마도 그렇습니다. 분석 도구 유형에 따라 달라 집니다. 소스 코드는 필요한 유일한 것일 수도 있고, 빌드 출력이 필요할 수도 있습니다.

예를 들어 자격 증명 스캐너 (CredScan)는 코드 리포지토리의 폴더 구조 내에서 파일을 분석 합니다. 이 분석으로 인해 자격 검색 및 보안 분석 로그 빌드 작업을 실행 하 여 결과를 가져올 수 있습니다.

빌드 후 아티팩트를 분석 하는 BinSkim와 같은 다른 도구에는 먼저 빌드가 필요 합니다.

### <a name="can-i-break-my-build-when-results-are-found"></a>결과가 발견 될 때 빌드를 나눌 수 있나요?

예. 모든 도구가 로그 파일의 문제 또는 문제를 보고할 때 빌드 중단을 도입할 수 있습니다. 사후 분석 빌드 작업을 추가 하 고 빌드를 중단할 도구의 확인란을 선택 하기만 하면 됩니다.

사후 분석 태스크의 UI에서 도구에서 오류만 보고 하거나 오류와 경고를 모두 보고 하는 경우 빌드를 중단 하도록 선택할 수 있습니다.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps의 명령줄 인수는 독립 실행형 데스크톱 도구의 인수와 어떻게 다릅니까? 

대부분의 경우 Azure DevOps 빌드 작업은 보안 도구의 명령줄 인수를 중심으로 하는 직접 래퍼입니다. 일반적으로 명령줄 도구에 전달 하는 모든 작업을 빌드 작업에 인수로 전달할 수 있습니다.

눈에 띄는 차이점:

- 도구는 에이전트 $ (빌드용 디렉터리)의 원본 폴더 또는% BUILD_SOURCESDIRECTORY%에서 실행 됩니다. 예를 들면\_,
- 인수의 경로는 이전에 나열 된 원본 디렉터리의 루트를 기준으로 할 수 있습니다. 경로는 절대 경로일 수 있습니다. Azure DevOps 빌드 변수를 사용 하거나 로컬 리소스의 알려진 배포 위치를 사용 하 여 온-프레미스 에이전트를 실행 하 여 절대 경로를 가져옵니다.
- 도구는 출력 파일 경로 또는 폴더를 자동으로 제공 합니다. 빌드 작업에 대 한 출력 위치를 제공 하는 경우 해당 위치는 빌드 에이전트의 잘 알려진 로그 위치에 대 한 경로로 바뀝니다.
- 일부 도구에 대해 몇 가지 추가 명령줄 인수를 변경 했습니다. 한 가지 예는 GUI를 시작 하지 않도록 하는 옵션을 추가 하거나 제거 하는 것입니다.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps 빌드에서 여러 리포지토리에서 자격 증명 스캐너와 같은 빌드 작업을 실행할 수 있나요?

아니요. 단일 파이프라인의 여러 리포지토리에 대해 보안 개발 도구를 실행 하는 것은 지원 되지 않습니다.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>지정한 출력 파일을 만들지 않았거나 지정한 출력 파일을 찾을 수 없습니다.

빌드 작업은 일부 사용자 입력을 필터링 합니다. 특히이 질문에서는 생성 된 출력 파일의 위치를 빌드 에이전트의 공통 위치로 업데이트 합니다. 이 위치에 대 한 자세한 내용은 다음 질문을 참조 하세요.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>도구에서 생성 된 출력 파일은 어디에 저장 되나요? 

빌드 작업은 빌드 에이전트의 잘 알려진이 위치에 대 한 출력 경로를 자동으로 추가 합니다. $ (에이전트로 디렉터리\_) sdt\logss 이 위치를 표준화 하기 때문에 코드 분석 로그를 생성 하거나 소비 하는 모든 팀이 출력에 액세스할 수 있습니다.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>호스트 된 빌드 에이전트에서 이러한 작업을 실행 하기 위해 빌드를 큐에 대기 시킬 수 있나요? 

예. 확장의 모든 작업 및 도구는 호스팅된 빌드 에이전트에서 실행할 수 있습니다.

>[!NOTE]
> 맬웨어 방지 스캐너 빌드 작업에는 Windows Defender를 사용 하는 빌드 에이전트가 필요 합니다. 호스팅된 Visual Studio 2017 이상에서는 이러한 에이전트를 제공 합니다. 빌드 작업은 Visual Studio 2015 호스팅된 에이전트에서 실행 되지 않습니다.
>
> 이러한 에이전트에서는 시그니처를 업데이트할 수 없지만 서명이 항상 3 시간 미만 이어야 합니다.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>빌드 파이프라인이 아니라 릴리스 파이프라인의 일부로 이러한 빌드 작업을 실행할 수 있나요?

대부분의 경우에는 예입니다.

그러나 Azure DevOps는 이러한 태스크가 아티팩트를 게시할 때 릴리스 파이프라인 내에서 작업을 실행 하는 것을 지원 하지 않습니다. 지원 부족으로 인해 릴리스 파이프라인에서 보안 분석 로그 게시 태스크가 성공적으로 실행 되지 않습니다. 대신 작업이 오류 메시지를 설명 하는 오류 메시지와 함께 실패 합니다.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>빌드 작업에서 도구를 다운로드 하는 위치

빌드 작업은 [Azure DevOps 패키지 관리 피드에서](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)도구의 NuGet 패키지를 다운로드할 수 있습니다. 빌드 작업은 빌드 에이전트에 미리 설치 해야 하는 노드 패키지 관리자를 사용할 수도 있습니다. 이러한 설치의 예로는 **npm install tslint**명령이 있습니다.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>확장을 설치 하면 Azure DevOps 조직에 어떤 영향이 있나요? 

설치 시 확장에서 제공 하는 보안 빌드 작업은 조직의 모든 사용자가 사용할 수 있게 됩니다. Azure 파이프라인을 만들거나 편집할 때 이러한 작업은 빌드-작업 컬렉션 목록에서 사용할 수 있습니다. 그렇지 않으면 Azure DevOps 조직에 확장을 설치 해도 아무런 영향을 주지 않습니다. 설치 시 계정 설정, 프로젝트 설정 또는 파이프라인이 수정 되지 않습니다.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>확장을 설치 하면 기존 Azure 파이프라인이 수정 되나요? 

아니요. 확장을 설치 하면 보안 빌드 작업을 파이프라인에 추가 하는 데 사용할 수 있습니다. 도구가 빌드 프로세스를 사용할 수 있도록 빌드 정의를 추가 하거나 업데이트 해야 합니다.

## <a name="task-specific-faq"></a>작업 관련 FAQ

빌드 작업과 관련 된 질문은이 섹션에 나와 있습니다.

### <a name="credential-scanner"></a>자격 증명 스캐너

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>일반적인 비 표시 시나리오 및 예제는 무엇 인가요?

가장 일반적인 두 가지 비 표시 시나리오에 대 한 자세한 내용은 다음과 같습니다.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>지정 된 경로 내에서 지정 된 암호의 모든 항목을 표시 하지 않으려면

다음 샘플에 표시 된 것 처럼 CredScan 출력 파일의 암호 해시 키가 필요 합니다.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> 해시 키는 일치 하는 값 또는 파일 콘텐츠의 일부에 의해 생성 됩니다. 모든 소스 코드 수정 버전은 해시 키를 변경 하 고 비 표시 규칙을 사용 하지 않도록 설정할 수 있습니다.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>지정 된 파일의 모든 암호를 표시 하지 않거나 암호 파일 자체를 표시 하지 않으려면

파일 식은 파일 이름일 수 있습니다. 전체 파일 경로 또는 파일 이름의 basename 될 수도 있습니다. 와일드 카드 지원 되지 않습니다.

다음 예제에서는 \src\JS\lib\angular.js 파일 > 파일 \<을 표시 하지 않는 방법을 보여 줍니다.

유효한 비 표시 규칙의 예:

- \<InputPath > \src\JS\lib\angular.js-지정 된 경로에 있는 파일을 표시 하지 않습니다.
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- node.js-이름이 같은 파일을 표시 하지 않습니다.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> 또한 파일에 추가 된 모든 향후 암호는 자동으로 표시 되지 않습니다.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>비밀을 관리 하는 데 권장 되는 지침은 무엇 인가요?

하드 코드 된 비밀을 신속 하 게 검색 하 고 위험을 완화 하는 것이 유용 합니다. 그러나 비밀이 체크 인 되지 않도록 하는 것이 훨씬 더 낫습니다.

이러한 측면에서 도움이 되도록 Microsoft는 Visual Studio 용 [Microsoft DevLabs 확장](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) 의 일부로 자격 증명 스캐너 코드 분석기의 초기 미리 보기를 릴리스 했습니다. 분석기는 초기 미리 보기 릴리스입니다. 개발자는 코드에서 잠재적 비밀을 검색 하기 위한 인라인 환경을 개발자에 게 제공 합니다. 이를 통해 분석기는 이러한 문제를 실시간으로 해결할 수 있는 기회를 개발자에 게 제공 합니다.

자세한 내용은 [클라우드에서 안전 하 게 암호 관리](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)블로그 게시물을 참조 하세요.

다음 리소스를 통해 안전 하 게 암호를 관리 하 고 응용 프로그램 내에서 중요 한 정보에 액세스할 수 있습니다.

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure AD(Azure Active Directory)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 관리 서비스 ID (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure App Service 및 Azure Functions에서 관리 되는 id](../../app-service/overview-managed-identity.md)
 - [AppAuthentication 라이브러리](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>사용자 지정 searchers를 직접 작성할 수 있나요?

자격 증명 스캐너는 일반적으로 buildsearchers 파일에 정의 된 콘텐츠 searchers 집합을 사용 합니다. 이 파일은 **Contentsearcher** 개체를 나타내는 XML 직렬화 된 개체의 배열을 포함 합니다. 이 프로그램은 잘 테스트 된 searchers 집합을 사용 하 여 배포 됩니다. 그러나 사용자 고유의 사용자 지정 searchers 구현할 수 있습니다.

콘텐츠 검색자는 다음과 같이 정의 됩니다.

- **이름**: 자격 증명 스캐너 출력 파일에서 사용 되는 설명이 포함 된 검색자 이름입니다. 검색자 이름에 카멜식 대/소문자 명명 규칙을 사용 하는 것이 좋습니다.
- **RuleId**: 인 검색자의 안정 된 불투명 ID입니다.
    - 자격 증명 스캐너 기본 검색 프로그램에는 CSCAN0010, CSCAN0020 또는 CSCAN0030와 같은 **RuleId** 값이 할당 됩니다. 마지막 숫자는 정규식 (regex)을 통해 검색자 그룹을 병합 하거나 분리 하기 위해 예약 되어 있습니다.
    - 사용자 지정 된 사용자에 대 한 **RuleId** 값에는 고유한 네임 스페이스가 있어야 합니다. 이러한 예로는 cscan\<네임\>스페이스 mylnxcn-0006 mylnxcn-0010, cscan\<-\>namespace 0020 및 cscan-\<namespace\>0030가 있습니다.
    - 정규화 된 검색자 이름은 **RuleId** 값과 검색자 이름의 조합입니다. 예를 들면 CSCAN0010이 있습니다. KeyStoreFiles 및 CSCAN0020. Base64EncodedCertificate.
- **Resourcematchpattern**: 검색자에 대해 확인할 파일 확장명의 Regex입니다.
- **Contentsearchpatterns**: 일치 시킬 regex 문을 포함 하는 문자열의 배열입니다. 검색 패턴이 정의 되지 않은 경우에는 **Resourcematchpattern** 값과 일치 하는 모든 파일이 반환 됩니다.
- **Contentsearchfilters**: Regex 관련 가양성을 필터링 하기 위한 regex 문을 포함 하는 문자열의 배열입니다.
- **Matchdetails**: 각 검색 프로그램과 일치 하는 설명 메시지, 완화 지침 또는 둘 다를 추가 합니다.
- **권장 사항**: PREfast 보고서 형식을 사용 하 여 일치 항목에 대 한 제안 필드 내용입니다.
- **심각도**: 문제의 심각도 수준을 반영 하는 정수입니다. 가장 높은 심각도 수준의 값은 1입니다.

  ![자격 증명 스캐너 설정을 보여 주는 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 분석기

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn 분석기 작업을 사용할 때 발생 하는 일반적인 오류는 무엇 인가요?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>잘못 된 Microsoft .Net Core 앱 버전을 사용 하 여 프로젝트를 복원 했습니다.

전체 오류 메시지:

"오류: 프로젝트가 Microsoft. x. x. x. x. x *. x. x. x. x. x. x. x. x. x*를 사용 하 여 복원 되었지만 현재 설정이 사용 됩니다. 이 문제를 해결 하려면 동일한 설정이 복원에 사용 되 고 빌드 또는 게시와 같은 후속 작업에 사용 되는지 확인 합니다. 일반적으로이 문제는 RuntimeIdentifier 속성이 빌드 중에 설정 되었지만 복원 중에는 설정 되지 않은 경우 발생할 수 있습니다. "

Roslyn 분석기 태스크가 컴파일의 일부로 실행 되기 때문에 빌드 컴퓨터의 소스 트리가 빌드 가능한 상태 여야 합니다.

주 빌드 및 Roslyn 분석기 단계 사이의 단계에서 원본 트리를 빌드를 방해 하는 상태로 전환할 수 있습니다. 이 추가 단계는 **dotnet 게시**일 수 있습니다. Roslyn 분석기 단계 바로 전에 NuGet 복원을 수행 하는 단계를 복제 해 보세요. 이 중복 된 단계는 원본 트리를 다시 빌드 가능한 상태로 전환할 수 있습니다.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe에서 분석기 인스턴스를 만들 수 없습니다.

전체 오류 메시지:

오류 코드 1 (으)로 인해 "csc.exe"가 종료 되었습니다. C: \\*bbbb*.dll에서 다음 AAAA 인스턴스를 만들 수 없습니다. 파일 또는 어셈블리 ' 31bf3856ad364e35, Version = x. x. x. x. x X X. x. x X X. x. x X X. x. x*X x*. 시스템은 지정된 파일을 찾을 수 없습니다."

컴파일러가 Roslyn 분석기를 지원 하는지 확인 합니다. **Csc.exe/version** 명령을 실행 하면 2.6 이상 버전 값을 보고 해야 합니다.

경우에 따라 .csproj 파일은 Microsoft.Net에서 패키지를 참조 하 여 빌드 컴퓨터의 Visual Studio 설치를 재정의할 수 있습니다. 특정 버전의 컴파일러를 사용 하지 않으려는 경우 Microsoft.Net에 대 한 참조를 제거 합니다. 그렇지 않으면 참조 된 패키지의 버전도 2.6 이상 인지 확인 합니다.

**Csc.exe/** 오류 로그 옵션에 지정 된 오류 로그 경로를 가져오려고 시도 합니다. 옵션 및 경로는 Roslyn 분석기 빌드 작업에 대 한 로그에 표시 됩니다. 다음과 같이 표시 될 수 있습니다 **./cf:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# 컴파일러 버전이 최신 버전이 아닙니다.

최신 버전의 C# 컴파일러를 다운로드 하려면 [Microsoft.Net](https://www.nuget.org/packages/Microsoft.Net.Compilers)로 이동 합니다. 설치 된 버전을 가져오려면 명령 프롬프트에서 **csc.exe/version** 를 실행 합니다. 2\.6 이상 버전의 Microsoft.Net NuGet 패키지를 참조 해야 합니다.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild 및 VSBuild 로그를 찾을 수 없습니다.

Roslyn 분석기 빌드 작업은 MSBuild 빌드 작업에서 MSBuild 로그에 대해 Azure DevOps를 쿼리해야 합니다. MSBuild 작업 직후에 분석기 태스크가 실행 되는 경우에는 로그를 아직 사용할 수 없습니다. MSBuild 작업과 Roslyn 분석기 작업 사이에 다른 작업을 추가 합니다. 다른 작업의 예로는 BinSkim 및 맬웨어 방지 스캐너가 있습니다.

## <a name="next-steps"></a>다음 단계

추가 지원이 필요한 경우 Microsoft 보안 코드 분석 지원은 월요일부터 금요일 오전 9:00 시부터 오후 5:00 시 태평양 표준시로 제공 됩니다.

  - 온보딩: 시작 하려면 기술 계정 관리자에 게 문의 하세요.
  
  - 지원은 [Microsoft 보안 코드 분석 지원](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)팀에 전자 메일을 보내세요.

  >[!NOTE] 
  >Microsoft와 유료 지원 관계가 없을 수 있습니다. 또는 Phoenix 카탈로그에서 서비스를 구입 하지 못하도록 하는 지원 제공이 있을 수 있습니다. 이러한 조건 중 하나에 해당 하는 경우 자세한 내용은 [지원 서비스 홈 페이지](https://www.microsoft.com/enterprise/services/support) 를 참조 하세요.
