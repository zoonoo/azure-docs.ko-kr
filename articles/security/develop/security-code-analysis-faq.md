---
title: 마이크로소프트 보안 코드 분석 문서 FAQ
description: 이 문서에는 Microsoft 보안 코드 분석 확장에 대한 FAQ가 포함되어 있습니다.
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
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460225"
---
# <a name="frequently-asked-questions"></a>질문과 대답
질문이 있으신가요? 자세한 내용은 다음 FAQ를 참조하십시오.

## <a name="general-faq"></a>일반 자주 묻는 질문

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Azure DevOps 인스턴스 대신 Visual Studio Team Foundation Server 인스턴스에 확장을 설치할 수 있습니까?

아니요. 이 확장은 Visual Studio Team 파운데이션 서버에 다운로드하여 설치할 수 없습니다.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>빌드를 사용하여 Microsoft 보안 코드 분석을 실행해야 합니까? 

아마도 그렇습니다. 분석 도구의 유형에 따라 다릅니다. 소스 코드가 필요한 유일한 것일 수도, 아니면 빌드 출력이 필요할 수도 있습니다.

예를 들어 CredScan(자격 증명 스캐너)은 코드 리포지토리의 폴더 구조 내에서 파일을 분석합니다. 이 분석으로 인해 CredScan을 실행하고 보안 분석 로그를 게시하여 독립 실행형 빌드에서 작업을 빌드하여 결과를 얻을 수 있습니다.

빌드 후 아티팩트를 분석하는 BinSkim과 같은 다른 도구의 경우 먼저 빌드가 필요합니다.

### <a name="can-i-break-my-build-when-results-are-found"></a>결과가 발견되면 빌드를 중단할 수 있습니까?

예. 모든 도구가 로그 파일에서 문제 나 문제를 보고할 때 빌드 중단을 도입할 수 있습니다. 분석 후 빌드 작업을 추가하고 빌드를 중단할 도구에 대한 확인란을 선택하기만 하면 됩니다.

사후 분석 작업의 UI에서 도구에서 오류만 보고하거나 오류와 경고를 모두 보고할 때 빌드를 중단하도록 선택할 수 있습니다.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps의 명령줄 인수는 독립 실행형 데스크톱 도구의 인수와 어떻게 다른가요? 

대부분의 경우 Azure DevOps 빌드 작업은 보안 도구의 명령줄 인수를 둘러싼 직접 래퍼입니다. 일반적으로 명령줄 도구에 전달하는 모든 것을 빌드 작업에 인수로 전달할 수 있습니다.

눈에 띄는 차이점:

- 도구는 에이전트 $(Build.SourceDirectory)의 소스 폴더 또는 %BUILD_SOURCESDIRECTORY%에서 실행됩니다. 예를 들어 C:\에이전트\_작업\1\s입니다.
- 인수의 경로는 이전에 나열된 원본 디렉터리 의 루트를 상대할 수 있습니다. 경로도 절대적일 수 있습니다. Azure DevOps 빌드 변수를 사용하거나 로컬 리소스의 알려진 배포 위치가 있는 온-프레미스 에이전트를 실행하여 절대 경로를 얻을 수 있습니다.
- 도구는 자동으로 출력 파일 경로 또는 폴더를 제공합니다. 빌드 작업에 대한 출력 위치를 제공하는 경우 해당 위치가 빌드 에이전트의 잘 알려진 로그 위치에 대한 경로로 대체됩니다.
- 일부 도구에 대해 일부 추가 명령줄 인수가 변경됩니다. 한 가지 예는 GUI가 시작되지 않도록 하는 옵션을 추가하거나 제거하는 것입니다.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps 빌드의 여러 리포지토리에서 자격 증명 스캐너와 같은 빌드 작업을 실행할 수 있습니까?

아니요. 단일 파이프라인에서 여러 리포지토리에서 보안 개발 도구를 실행하는 것은 지원되지 않습니다.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>지정한 출력 파일이 만들어지지 않거나 지정한 출력 파일을 찾을 수 없습니다.

빌드 작업은 일부 사용자 입력을 필터링합니다. 이 질문의 경우 특히 생성된 출력 파일의 위치를 빌드 에이전트의 공통 위치로 업데이트합니다. 이 위치에 대한 자세한 내용은 다음 질문을 참조하십시오.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>저장된 도구에 의해 생성된 출력 파일은 어디에 있습니까? 

빌드 작업은 빌드 에이전트에서 잘 알려진 이 위치에 출력 경로를 자동으로 추가합니다: $(Agent.BuildDirectory)\_sdt\logs. 이 위치에서 표준화하기 때문에 코드 분석 로그를 생성하거나 사용하는 모든 팀은 출력에 액세스할 수 있습니다.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>호스팅된 빌드 에이전트에서 이러한 작업을 실행하도록 빌드를 큐에 대기할 수 있습니까? 

예. 확장의 모든 작업 및 도구는 호스팅된 빌드 에이전트에서 실행할 수 있습니다.

>[!NOTE]
> 맬웨어 방지 스캐너 빌드 작업에는 Windows Defender가 활성화된 빌드 에이전트가 필요합니다. 호스팅 Visual Studio 2017 및 나중에 이러한 에이전트를 제공합니다. 빌드 작업은 Visual Studio 2015 호스팅 에이전트에서 실행되지 않습니다.
>
> 이러한 에이전트에서 서명을 업데이트할 수는 없지만 서명은 항상 3시간 미만이어야 합니다.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>빌드 파이프라인이 아닌 릴리스 파이프라인의 일부로 이러한 빌드 작업을 실행할 수 있습니까?

대부분의 경우 그렇습니다.

그러나 Azure DevOps는 해당 작업이 아티팩트를 게시할 때 릴리스 파이프라인 내에서 실행 중인 작업을 지원하지 않습니다. 이러한 지원이 부족하면 릴리스 파이프라인에서 보안 분석 로그 게시 작업이 성공적으로 실행되지 않습니다. 대신 설명 오류 메시지와 함께 작업이 실패합니다.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>빌드 작업은 어디에서 도구를 다운로드합니까?

빌드 작업은 [Azure DevOps 패키지 관리 피드에서](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)도구의 NuGet 패키지를 다운로드할 수 있습니다. 빌드 작업은 빌드 에이전트에 미리 설치해야 하는 노드 패키지 관리자를 사용할 수도 있습니다. 이러한 설치의 예는 명령 **npm tslint를 설치합니다.**

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>확장 설치는 Azure DevOps 조직에 어떤 영향을 미합니까? 

설치 시 확장에서 제공하는 보안 빌드 작업을 조직의 모든 사용자가 사용할 수 있게 됩니다. Azure 파이프라인을 만들거나 편집할 때 이러한 작업은 빌드 작업 컬렉션 목록에서 사용할 수 있습니다. 그렇지 않으면 Azure DevOps 조직에 확장을 설치하면 효과가 없습니다. 설치는 계정 설정, 프로젝트 설정 또는 파이프라인을 수정하지 않습니다.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>확장을 설치하면 기존 Azure 파이프라인이 수정됩니까? 

아니요. 확장을 설치하면 파이프라인에 추가하여 보안 빌드 작업을 사용할 수 있습니다. 도구가 빌드 프로세스에서 작업할 수 있도록 빌드 정의를 추가하거나 업데이트해야 합니다.

## <a name="task-specific-faq"></a>작업별 FAQ

빌드 작업과 관련된 질문은 이 섹션에 나와 있습니다.

### <a name="credential-scanner"></a>자격 증명 스캐너

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>일반적인 억제 시나리오와 예제는 무엇입니까?

다음은 가장 일반적인 억제 시나리오 두 가지에 대한 세부 정보입니다.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>지정된 경로 내에서 지정된 비밀의 모든 발생을 억제하려면

다음 샘플과 같이 CredScan 출력 파일의 보안 검색키의 해시 키가 필요합니다.

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
> 해시 키는 일치하는 값 또는 파일 콘텐츠의 일부에 의해 생성됩니다. 모든 소스 코드 개정은 해시 키를 변경하고 억제 규칙을 비활성화할 수 있습니다.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>지정된 파일의 모든 기밀을 표시하거나 비밀 파일 자체를 억제하려면

파일 표현식은 파일 이름일 수 있습니다. 또한 전체 파일 경로또는 파일 이름의 기본 이름 부분일 수도 있습니다. 와일드카드는 지원되지 않습니다.

다음 예제는 입력 \<패스>\src\JS\lib\angular.js를 억제 하는 방법을 보여 준다

유효한 억제 규칙의 예:

- \<InputPath>\src\JS\lib\angular.js - 지정된 경로에서 파일을 표시하지 않습니다.
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- [각도]js - 이름이 같은 파일을 표시하지 않습니다.

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
> 파일에 추가된 모든 이후의 암호도 자동으로 표시되지 않습니다.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>비밀 관리를 위한 권장 지침은 무엇입니까?

다음 리소스는 응용 프로그램 내에서 기밀 정보를 안전하게 관리하고 액세스하는 데 도움이 됩니다.

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active 디렉터리(Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [AZURE AD 관리 서비스 ID(MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 앱 서비스 및 Azure 함수에서 관리되는 ID](../../app-service/overview-managed-identity.md)
 - [앱 인증 라이브러리](../../key-vault/general/service-to-service-authentication.md)


자세한 내용은 [클라우드에서 비밀 관리](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)블로그 게시물을 참조하십시오.

#### <a name="can-i-write-my-own-custom-searchers"></a>사용자 지정 검색어를 직접 작성할 수 있나요?

자격 증명 스캐너는 buildsearchers.xml 파일에 일반적으로 정의되는 콘텐츠 검색기 집합을 사용합니다. 파일에는 **ContentSearcher** 개체를 나타내는 XML 직렬화된 개체의 배열이 포함되어 있습니다. 이 프로그램은 잘 테스트 된 검색자의 집합으로 배포됩니다. 그러나 사용자 지정 검색어도 구현할 수 있습니다.

콘텐츠 검색자는 다음과 같이 정의됩니다.

- **이름**: 자격 증명 스캐너 출력 파일에 사용할 설명 검색기 이름입니다. 검색어 이름에 는 낙타 대/소문자 명명 규칙을 사용하는 것이 좋습니다.
- **RuleId**: 검색자의 안정적인 불투명 ID:
    - 자격 증명 스캐너 기본 검색기에는 CSCAN0010, CSCAN0020 또는 CSCAN0030과 같은 **RuleId** 값이 할당됩니다. 마지막 숫자는 정규식(정규식)을 통해 검색기 그룹을 병합하거나 나눌 수 있는 예약되어 있습니다.
    - 사용자 지정된 검색에 대한 **RuleId** 값에는 고유한 네임스페이스가 있어야 합니다. 예를 들어 CSCAN-네임스페이스\<\>0010,\<CSCAN-네임스페이스\>0020\>및 CSCAN-네임스페이스\<0030을 예로 들 수 있습니다.
    - 정규화된 검색기 이름은 **RuleId** 값과 검색자 이름의 조합입니다. CSCAN0010을 예로 들 수 있습니다. 키 스토어파일 및 CSCAN0020. Base64암호화된 인증서.
- **ResourceMatchPattern**: 검색에 대해 확인하기 위해 파일 확장자의 정규식.
- **ContentSearch패턴**: 일치하는 정규식 문을 포함하는 문자열의 배열입니다. 검색 패턴이 정의되지 않으면 **ResourceMatchPattern** 값과 일치하는 모든 파일이 반환됩니다.
- **ContentSearchFilters**: 검색기 별 거짓 긍정을 필터링하기 위해 정규법 문을 포함하는 문자열의 배열입니다.
- **일치 세부 정보**: 설명 메시지, 완화 지침 또는 둘 다 검색자의 각 일치 에 대해 추가됩니다.
- **권장 사항**: PREfast 보고서 형식을 사용하여 일치하는 것에 대한 제안 필드 콘텐츠입니다.
- **심각도**: 문제의 심각도 수준을 반영하는 정수입니다. 가장 높은 심각도 수준에는 값 1이 있습니다.

  ![자격 증명 스캐너 설정을 보여주는 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 분석기

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn 분석기 작업을 사용할 때 일반적인 오류는 무엇입니까?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>프로젝트가 잘못된 Microsoft.NETCore.App 버전을 사용하여 복원되었습니다.

전체 오류 메시지:

"오류: 이 프로젝트는 Microsoft.NETCore.App 버전 *x.x.x를*사용하여 복원되었지만 현재 설정에서는 버전 *y.y.y가* 대신 사용됩니다. 이 문제를 해결하려면 복원 및 빌드 또는 게시와 같은 후속 작업에 대해 동일한 설정을 사용해야 합니다. 일반적으로 복원 중이 아닌, 빌드 또는 게시 중에 RuntimeIdentifier 속성이 설정된 경우 이 문제가 발생할 수 있습니다."

Roslyn 분석기 작업은 컴파일의 일부로 실행되므로 빌드 컴퓨터의 소스 트리는 빌드 가능한 상태여야 합니다.

기본 빌드와 Roslyn 분석기 단계 사이의 단계는 소스 트리를 빌드를 방지하는 상태로 전환했을 수 있습니다. 이 추가 단계는 아마도 **dotnet.exe 게시입니다.** Roslyn 분석기 단계 바로 전에 NuGet 복원을 수행하는 단계를 복제해 보십시오. 이 중복된 단계는 원본 트리를 빌드 가능한 상태로 되돌릴 수 있습니다.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe 분석기 인스턴스를 만들 수 없습니다.

전체 오류 메시지:

"'csc.exe' 오류 코드 1로 종료 - 분석기 *AAAA의* 인스턴스는 C에서 만들 수 없습니다 :\\*BBBB*.dll : 파일 또는 어셈블리를로드 할 수 없습니다 'Microsoft.CodeAnalysis, 버전 =*X.X.X. X.X.* 시스템은 지정된 파일을 찾을 수 없습니다."

컴파일러가 Roslyn 분석기를 지원하는지 확인합니다. **csc.exe /version** 명령을 실행하면 버전 값이 2.6 이상이라고 보고해야 합니다.

경우에 따라 .csproj 파일은 Microsoft.Net.Compilers의 패키지를 참조하여 빌드 컴퓨터의 Visual Studio 설치를 재정의할 수 있습니다. 컴파일러의 특정 버전을 사용하지 않으려면 Microsoft.Net.Compilers에 대한 참조를 제거합니다. 그렇지 않으면 참조된 패키지의 버전도 2.6 이상인지 확인합니다.

**csc.exe /errorlog** 옵션에 지정된 오류 로그 경로를 가져옵니다. 옵션 및 경로는 Roslyn 분석기 빌드 작업에 대한 로그에 나타납니다. **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# 컴파일러 버전이 최신 버전이 충분하지 않습니다.

C# 컴파일러의 최신 버전을 얻으려면 [Microsoft.Net.Compiler](https://www.nuget.org/packages/Microsoft.Net.Compilers)로 이동하십시오. 설치된 버전을 얻으려면 명령 프롬프트에서 **csc.exe /version을** 실행합니다. 버전 2.6 이상인 Microsoft.Net.Compilers NuGet 패키지를 참조해야 합니다.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild 및 VSBuild 로그를 찾을 수 없습니다.

Roslyn 분석기 빌드 작업은 MSBuild 빌드 작업에서 MSBuild 로그에 대한 Azure DevOps를 쿼리해야 합니다. 분석기 작업이 MSBuild 작업 직후에 실행되는 경우 로그를 아직 사용할 수 없습니다. MSBuild 작업과 Roslyn 분석기 작업 사이에 다른 작업을 배치합니다. 다른 작업의 예로는 BinSkim 및 맬웨어 방지 스캐너가 있습니다.

## <a name="next-steps"></a>다음 단계

추가 지원이 필요한 경우 월요일부터 금요일까지 오전 9:00부터 오후 5:00까지 태평양 표준시로 Microsoft 보안 코드 분석 지원을 사용할 수 있습니다.

- 온보딩: [온보딩 설명서](security-code-analysis-onboard.md) 참조
  
- 지원: [Microsoft 보안 코드 분석 지원](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) 팀에 이메일을 보내주십시오.