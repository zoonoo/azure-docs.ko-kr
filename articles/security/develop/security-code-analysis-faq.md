---
title: Microsoft Azure 보안 코드 분석 설명서 Faq
description: 이 문서에는 보안 코드 분석 확장 프로그램에 대 한 Faq가 포함 되어 있습니다.
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
ms.openlocfilehash: 8038b7bd60ac771c798a1a8645022b0bf9e142a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934840"
---
# <a name="frequently-asked-questions"></a>질문과 대답
질문이 있나요? 자세한 내용은 아래의 Faq를 확인 하세요.

## <a name="general-faqs"></a>일반 Faq

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>TFS (Azure DevOps 아님) 서버에 확장을 설치할 수 있나요? 

아니요. 확장은 TFS 용 다운로드 및 설치에 사용할 수 없습니다.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>빌드를 사용 하 여 Microsoft 보안 코드 분석을 실행 해야 하나요? 

그렇기도 하고 그렇지 않기도 하고. 분석 도구 유형에 따라 소스 코드 자체는 필요한 유일한 것일 수도 있고 빌드의 출력이 필요할 수도 있습니다. 예를 들어 자격 증명 스캐너가 코드 리포지토리의 폴더 구조 내에서 파일을 분석 하므로 자격 증명 스캐너를 실행 하 고 독립 실행형 빌드에서 보안 분석 로그 빌드 작업을 게시 하 여 결과를 검색할 수 있습니다.
BinSkim와 같은 게시 빌드 아티팩트를 분석 하는 다른 도구에 대해서는 먼저 빌드가 필요 합니다.

### <a name="can-i-break-my-build-when-results-are-found"></a>결과가 발견 될 때 빌드를 나눌 수 있나요? 
예, 모든 도구가 로그 파일에서 발견 한 문제를 보고 하면 빌드 중단을 도입할 수 있습니다. 사후 분석 빌드 작업을 추가 하 고 빌드를 중단할 도구의 확인란을 선택 하기만 하면 됩니다. 사후 분석 작업의 UI에서 오류 또는 경고 및 오류를 보고 하는 경우 빌드를 중단 하도록 선택할 수 있습니다.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Azure DevOps의 명령줄 인수는 독립 실행형 데스크톱 도구에 있는 것과 다릅니다. 

대부분의 경우 Azure DevOps 빌드 작업은 보안 도구의 명령줄 인수를 중심으로 하는 직접 래퍼입니다. 일반적으로 데스크톱에서 명령줄의 도구에 전달 하는 모든 작업은 빌드 작업의 인수 입력에 전달할 수 있습니다.
다음은 눈에 띄는 차이점 목록입니다.
 - 이 도구는 에이전트 $ (빌드용 디렉터리) 또는% BUILD_SOURCESDIRECTORY%의 원본 폴더에서 실행 됩니다. 예제: C:\agent\_근무 \1\s 
 - 인수의 경로는 로컬 리소스의 알려진 배포 위치를 사용 하 여 온-프레미스 에이전트를 실행 하거나 Azure DevOps 빌드 변수를 사용 하 여 위에 나열 된 원본 디렉터리의 루트에 대 한 상대 경로일 수 있습니다.
 - 출력 경로를 제공 하면 도구에서 출력 파일 경로 또는 폴더를 자동으로 제공 합니다 .이 경로는 제거 되 고 빌드 에이전트의 잘 알려진 로그 위치에 대 한 경로로 대체 됩니다.
 - GUI를 시작 하지 않도록 하는 옵션 추가 또는 제거와 같은 일부 도구에서 일부 추가 명령줄 매개 변수를 삭제 하 고 제거 합니다.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps 빌드에서 여러 리포지토리에 대해 빌드 작업 (예: 자격 증명 스캐너)을 실행할 수 있나요? 

아니요. 단일 파이프라인의 여러 리포지토리에 대해 보안 개발 도구를 실행 하는 것은 현재 지원 되지 않습니다.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>지정한 출력 파일을 만들지 않았습니다. 지정 된 출력 파일을 찾을 수 없습니다. 

빌드 작업은 현재 사용자 입력을 삭제 하 고 생성 되는 출력 파일의 위치를 빌드 에이전트의 공통 위치로 업데이트 합니다. 이 위치에 대 한 자세한 내용은 다음 질문을 참조 하세요.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>도구에서 생성 된 출력 파일은 어디에 저장 되나요? 

빌드 작업은 빌드 에이전트 $ (에이전트로 디렉터리)\_sdt\logs.의 잘 알려진 다음 위치에 출력 경로를 자동으로 추가 합니다. 이 위치에서 표준화 하면 코드 분석 로그를 생성 하거나 사용 하는 다른 팀이 액세스할 수 있도록 보장할 수 있습니다.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>호스트 된 빌드 에이전트에서 이러한 작업을 실행 하기 위해 빌드를 큐에 대기 시킬 수 있나요? 

예, 호스팅된 빌드 에이전트에서 확장의 모든 작업 및 도구를 실행할 수 있습니다.

>[!NOTE]
> 맬웨어 방지 빌드 작업을 수행 하려면 Windows Defender를 사용 하는 빌드 에이전트가 필요 합니다 .이는 "Hosted VS2017" 이상 빌드 에이전트에 해당 합니다. (레거시/VS2015 "호스팅된" 에이전트에서 실행 되지 않습니다.) 이러한 에이전트에서는 시그니처를 업데이트할 수 없지만 서명이 3 시간 미만인 경우 항상 상대적으로 최신 이어야 합니다.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>빌드 파이프라인이 아니라 릴리스 파이프라인의 일부로 이러한 빌드 작업을 실행할 수 있나요? 
대부분의 경우에는 예입니다. 그러나 아티팩트를 게시 하는 작업은 릴리스 파이프라인 내에서 실행 되도록 Azure DevOps에서 지원 되지 않습니다. "릴리스와 함께 사용할 수 없는 유일한 작업 범주는 아티팩트를 게시 하는 작업입니다. 지금은 릴리스 내에서 아티팩트 게시를 지원 하지 않기 때문입니다.
이렇게 하면 "보안 분석 로그 게시" 태스크가 릴리스 파이프라인에서 성공적으로 실행 되지 않습니다. 설명이 포함 된 오류 메시지와 함께 실패 합니다.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>빌드 작업에서 도구를 다운로드 하는 위치 
빌드 작업 a) 다음 [Azure DevOps 패키지 관리 피드의](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) 도구에 대 한 NuGet 패키지를 다운로드 하거나 빌드 에이전트에 미리 설치 해야 하는 노드 패키지 관리자를 사용 합니다 (예: "npm install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>확장을 설치 하면 Azure DevOps 조직에 어떤 영향을 미칠 수 있나요? 

를 설치할 때 확장에서 제공 하는 보안 빌드 작업은 조직의 모든 사용자가 사용할 수 있게 됩니다. Azure 파이프라인을 만들거나 편집할 때 이러한 작업은 빌드 작업 컬렉션 목록에서 추가할 수 있습니다. 그렇지 않으면 Azure DevOps 조직에 확장을 설치 해도 아무런 영향을 주지 않습니다. 계정 또는 프로젝트 설정이 나 파이프라인은 수정 하지 않습니다.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>확장을 설치 하면 기존 Azure Pipelines 수정 되나요? 

아니요. 확장을 설치 하면 보안 빌드 작업을 Azure Pipelines에 추가할 수 있습니다. 사용자는 빌드 프로세스에 도구를 통합 하기 위해 빌드 정의를 추가 하거나 업데이트 해야 합니다.

## <a name="task-specific-faqs"></a>작업 관련 Faq

빌드 작업 관련 Faq는이 섹션에 나와 있습니다.

### <a name="credential-scanner-faqs"></a>자격 증명 스캐너 Faq

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>일반적인 비 표시 시나리오 및 예는 무엇입니까? 
가장 일반적인 비 표시 시나리오 중 두 가지는 아래에 자세히 설명 되어 있습니다.
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>지정 된 경로 내에서 지정 된 암호의 모든 항목을 표시 하지 않습니다. 
아래 샘플에 표시 된 것 처럼 자격 증명 스캐너 출력 파일에서 암호의 해시 키가 필요 합니다.
   
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

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>지정 된 파일의 모든 암호를 표시 하지 않거나 비밀 파일 자체를 표시 하지 않으려면 
파일 식은 파일 이름 이거나 전체 파일 경로/이름의 후 위 일부일 수 있습니다. 와일드 카드 지원 되지 않습니다. 

**예제** 

억제할 파일: [InputPath] \src\JS\lib\angular.js 

유효한 비 표시 규칙: 
- [InputPath] \src\JS\lib\angular.js--지정 된 경로에 있는 파일을 표시 하지 않습니다.
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- node.js--이름이 같은 파일을 표시 하지 않습니다.

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

#### <a name="what-are-recommended-secrets-management-guidelines"></a>권장 되는 비밀 관리 지침은 무엇 인가요? 
하드 코딩 된 비밀을 적시에 검색 하 고 위험을 완화 하는 것이 도움이 되는 반면,이로 인해 비밀이 완전히 체크 인 되지 않는 경우 훨씬 더 낫습니다. 이와 관련 하 여 Microsoft는 Visual Studio 용 [Microsoft DevLabs 확장](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) 의 일부로 Credscan Code Analyzer를 출시 했습니다. 초기 미리 보기 중에는 개발자에 게 코드에서 잠재적인 비밀을 검색 하는 인라인 환경을 제공 하 여 이러한 문제를 실시간으로 해결할 수 있는 기회를 제공 합니다. 자세한 내용은 클라우드에서 안전 하 게 암호 관리에 대 한 [이](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) 블로그를 참조 하세요. 다음은 암호를 관리 하 고 응용 프로그램 내에서 중요 한 정보를 안전한 방식으로 액세스 하는 데 도움이 되는 몇 가지 추가 리소스입니다. 
 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 관리 서비스 ID](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 리소스용 MSI(관리 서비스 ID)](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 관리 서비스 ID](../../app-service/overview-managed-identity.md)
 - [AppAuthentication 라이브러리](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>사용자 지정 searchers를 직접 작성할 수 있나요?

자격 증명 스캐너는 일반적으로 **buildsearchers** 파일에 정의 된 콘텐츠 searchers 집합을 사용 합니다. 이 파일은 ContentSearcher 개체를 나타내는 XML 직렬화 된 개체의 배열을 포함 합니다. 이 프로그램은 잘 테스트 된 searchers 집합을 사용 하 여 배포 되지만 사용자 지정 searchers도 구현할 수 있습니다. 

콘텐츠 검색자는 다음과 같이 정의 됩니다. 

- **이름** – 자격 증명 스캐너 출력 파일에서 사용 되는 설명이 포함 된 검색자 이름입니다. 검색자 이름에 카멜식 대/소문자 명명 규칙을 사용 하는 것이 좋습니다. 
- **RuleId** – 검색자의 안정적인 불투명 ID입니다. 
    - 자격 증명 스캐너 기본 searchers는 CSCAN0010, CSCAN0020, CSCAN0030 등과 같은 RuleIds와 함께 할당 됩니다. 마지막 숫자는 잠재적인 검색자 regex 그룹 병합 또는 나누기를 위해 예약 됩니다.
    - 사용자 지정 된 searchers에 대 한 RuleId의 형식에는 고유한 네임 스페이스가 있어야 합니다. CSCAN-{Namespace} mylnxcn-0006 mylnxcn-0010, CSCAN-{namespace} 0020, CSCAN-{Namespace} 0030 등
    - 정규화 된 검색자 이름은 RuleId 및 검색자 이름 조합입니다. 예 CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate 등
- **Resourcematchpattern** – 검색할 파일 확장명의 Regex
- **Contentsearchpatterns** -일치 하는 Regex 문을 포함 하는 문자열의 배열입니다. 검색 패턴이 정의 되지 않은 경우 리소스 일치 패턴과 일치 하는 모든 파일이 반환 됩니다.
- **Contentsearchfilters** – Regex 특정 가양성을 필터링 하기 위한 Regex 문을 포함 하는 문자열의 배열입니다.
- **Matchdetails** – 검색 각 일치 항목에 대해 설명 메시지 및/또는 완화 지침을 추가 합니다.
- **권장 사항** – PREfast 보고서 형식을 사용 하 여 일치 항목에 대 한 제안 필드 내용을 제공 합니다.
- **심각도** – 문제의 심각도를 반영 하는 정수입니다 (가장 높음 = 1).
![자격 증명 스캐너 설정](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Roslyn 분석기 Faq

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn 분석기 작업을 사용 하는 경우 가장 일반적인 오류는 무엇 인가요?

**오류: 프로젝트가 Microsoft. x. x. x. x. x. x. x. x. x. x. x. x. x. x를 사용 하 여 복원 되었지만 현재 설정이 사용 됩니다. 이 문제를 해결 하려면 동일한 설정이 복원에 사용 되 고 빌드 또는 게시와 같은 후속 작업에 사용 되는지 확인 합니다. 일반적으로이 문제는 RuntimeIdentifier 속성이 빌드 또는 게시 중에 설정 되지만 복원 중에는 설정 되지 않은 경우 발생할 수 있습니다.**

Roslyn 분석기는 컴파일의 일부로 실행 되므로 빌드 컴퓨터의 소스 트리가 빌드 가능한 상태 여야 합니다. 주 빌드와 Roslyn 분석기 간의 단계 ("dotnet .exe 게시")는 원본 트리를 unbuildable 상태로 전환 했을 수 있습니다. Roslyn 분석기 단계 직전에 Nuget 복원을 수행 하는 단계를 복제 하는 것은 원본 트리를 다시 빌드 가능한 상태로 전환 합니다.

**"csc.exe"가 종료 되었습니다 (오류 코드 1). C:\BBBB.dll에서 다음 AAAA 인스턴스를 만들 수 없습니다. 파일 또는 어셈블리 ' 31bf3856ad364e35, Version = X. x. x. x. x X X. x. x X X. x. x X X. x. x X X. 시스템에서 지정 된 파일을 찾을 수 없습니다.**

컴파일러가 Roslyn 분석기를 지원 하는지 확인 합니다. "csc.exe/version"는 적어도 v 2.6. x를 보고 해야 합니다. 경우에 따라 개별 .csproj 파일은 Microsoft.Net에서 패키지를 참조 하 여 빌드 컴퓨터의 Visual Studio 설치를 재정의할 수 있습니다. 특정 버전의 컴파일러를 사용할 수 없는 경우 Microsoft.Net에 대 한 참조를 제거 합니다. 그렇지 않으면 참조 된 패키지도 v 2.6. x 이상 인지 확인 합니다. Csc.exe 명령줄 (Roslyn build 작업의 로그에 있음)의/cmss: 매개 변수에서 찾을 수 있는 오류 로그를 가져오려고 시도 합니다. 다음과 유사 하 게 표시 될 수 있습니다./namef:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif

**C# 컴파일러가 최신 버전이 아닙니다 (> = 2.6 이어야 함).**

최신 버전의 C# 컴파일러는에 릴리스 https://www.nuget.org/packages/Microsoft.Net.Compilers 되었습니다. 설치 된 버전을 가져오려면 명령 프롬프트에서를 `C:\>csc.exe /version` 실행 합니다. < V 2.6 인 Microsoft.Net NuGet 패키지에 대 한 참조가 없는지 확인 합니다.

**MSBuild/VSBuild 로그를 찾을 수 없습니다.**

작업의 작동 방식 때문에이 작업은 MSBuild 빌드 작업에서 MSBuild 로그에 대해 Azure DevOps를 쿼리해야 합니다. MSBuild 빌드 작업 직후에이 작업이 실행 되는 경우에는 로그를 사용할 수 없습니다. MSBuild 빌드 작업과 Roslyn 분석기 빌드 작업 간에 Binskim, 맬웨어 방지 검사 등의 기타 빌드 작업을 포함 하 여 다른 빌드 작업을 수행 합니다. 

## <a name="next-steps"></a>다음 단계

추가 지원이 필요한 경우 Microsoft 보안 코드 분석 지원은 월요일부터 금요일 오전 9:00 시-5:00 태평양 표준시 (태평양 표준시)로 제공 됩니다.

  - 온 보 딩-시작 하려면 기술 계정 관리자에 게 문의 하세요. 
  >[!NOTE] 
  >Microsoft와 유료 지원 관계가 아직 없거나 Phoenix 카탈로그에서 서비스를 구매할 수 없는 지원 서비스가 있는 경우 [지원 서비스 홈 페이지](https://www.microsoft.com/enterprise/services/support) 에서 자세한 내용을 참조 하세요.

  - 지원- [Microsoft 보안 코드 분석 지원](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) 팀에 전자 메일 보내기