---
title: 응용 프로그램을 Azure 및 Azure 배포 스택 | Microsoft Docs
description: 하이브리드 CI/CD 파이프라인을 Azure 및 Azure 스택 앱을 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 49a80805c976e5584bb158965583a03eda68cc46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>자습서: Azure 및 Azure에 앱 배포 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

연속 통합/연속 하이브리드 배달 (CI/CD) 파이프라인 빌드, 테스트 및 여러 클라우드에 앱을 배포할 수 있습니다.  이 자습서에서는 샘플 환경을 빌드합니다.
 
> [!div class="checklist"]
> * Visual Studio Team Services (VSTS) 리포지토리에 코드 커밋에 따라 새 빌드를 시작 합니다.
> * 사용자 수용 테스트에 대 한 글로벌 Azure를 새로 작성된 된 코드를 자동으로 배포 합니다.
> * 코드, 테스트를 통과 한 Azure 스택을 자동으로 배포 합니다.

### <a name="about-the-hybrid-delivery-build-pipe"></a>하이브리드 배달에 대 한 빌드 파이프

응용 프로그램 배포 연속성, 보안 및 안정성은 귀하의 조직에 중요 하 고 개발 팀에 중요 합니다. 하이브리드 CI/CD 파이프라인이 포함 된 온-프레미스 환경 및 공용 클라우드 전반 파이프라인을 통합할 수 있습니다. 응용 프로그램 전환 하지 않고 위치를 변경할 수 있습니다.

또한이 방법을 사용을 사용 하면 개발 도구의 일관 된 집합을 유지 관리할 수 있습니다. Azure 공용 클라우드 및 온-프레미스 Azure 스택 환경에서 일관 된 도구 것을 쉽게 개발 연습 CI/CD를 구현할 수입니다. Azure 스택 또는 Azure에 배포 된 서비스 및 응용 프로그램은 서로 전환이 가능 하 고 온-프레미스 및 공용 클라우드 특성 / 기능을 활용 하기 위해 동일한 코드를 한 위치에서 실행할 수 있습니다.

다음에 대해 자세히 알아봅니다.
 - [연속 통합 이란?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [지속적인 업데이트 무엇입니까?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>필수 조건

몇 가지 구성 요소가 하이브리드 CI/CD 파이프라인을 구축 하기 위해 준비에서 해야 합니다. 준비 하려면 다소 시간이 걸릴 수 있습니다.
 
 - Azure OEM/하드웨어 파트너 Azure 스택 프로덕션에 배포할 수 있습니다 및 모든 사용자가 Azure 스택 개발 키트 (ASDK)를 배포할 수 있습니다. 
 - Azure 스택 연산자 해야도 응용 프로그램 서비스를 배포, 계획 및 제안 테 넌 트 구독 및 만든 Windows Server 2016 이미지를 추가 합니다.

이미 있는 경우 이러한 구성 요소 중 일부를 시작 하기 전에 요구 사항을 충족 해야 합니다.

이 항목에는 Azure 및 Azure 스택에 대 한 지식이 있다고 가정 합니다. 계속 하기 전에 자세한 정보를 원하는 경우 다음이 항목으로 시작 해야 합니다.


이 자습서에는 Azure 및 Azure 스택에 대 한 지식이 있다고 가정 합니다. 

계속 하기 전에 자세한 정보를 원하는 경우 이러한 항목을 시작할 수 있습니다.
 - [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure 스택 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

 - 만들기는 [웹 앱](https://docs.microsoft.com/azure/app-service/app-service-web-overview) Azure에서. 나중에 사용 중 이므로 새 웹 앱 URL을 기록해 두십시오.

Azure Stack
 - Azure 스택 통합 시스템을 사용 하거나 Azure 스택 개발 키트 (ASDK)이 아래에 링크 된 배포:
    - ASDK 배포에 대 한 자세한 지침을 찾을 수 있습니다 "[자습서: 설치 관리자를 사용 하 여 ASDK 배포](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - 다양 한 다음 PowerShell 스크립트를 사용 하면 ASDK 배포 후 단계를 자동화할 수 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 )합니다.

    > [!note]  
    > ASDK 설치 과정이 완료 되기를 수 있으므로 적절 하 게 계획 7 시간.

 - 배포 [앱 서비스](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure 스택에 PaaS 서비스입니다. 
 - 만들 [계획/제안](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure 스택 환경 내에서. 
 - 만들 [구독 테 넌 트](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure 스택 환경 내에서. 
 - 테 넌 트 구독 내에서 웹 앱을 만듭니다. 새 웹 앱 URL을 사용 하 여 나중에 메모를 확인 합니다.
 - 테 넌 트 구독 내에서 여전히 VSTS 가상 컴퓨터를 배포 합니다.
 - 필요한.NET 3.5와 함께 Windows Server 2016 VM입니다. 이 VM은 개인 빌드 에이전트와 Azure 스택에 생성 됩니다. 

### <a name="developer-tools"></a>개발자 도구

 - 만들기는 [VSTS 작업 영역](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)합니다. 등록 하면 프로젝트가 **MyFirstProject**합니다.
 - [Visual Studio 2017 설치](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 및 [VSTS 로그인](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)합니다.
 - 프로젝트에 연결 하 고 [로컬로 복제](https://www.visualstudio.com/docs/git/gitquickstart)합니다.
 
 > [!note]  
 > Azure 스택 (Windows Server 및 SQL)를 실행 하 고 배포 하는 응용 프로그램 서비스를 게시 하는 적절 한 이미지와 함께 필요 합니다.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>개인 빌드 및 릴리스 에이전트 Visual Studio Team Services 통합을 준비

### <a name="prerequisites"></a>필수 조건

Visual Studio Team Services VSTS ()에 대 한 Azure Resource Manager 서비스 사용자를 사용 하 여 인증 합니다. Azure 스택 구독에 리소스를 제공 하려면 VSTS에 대 한 참가자 상태가 필요 합니다.

이러한 인증을 사용 하도록 구성 해야 하는 상위 수준 단계는 다음과 같습니다.

1. 서비스 사용자를 만들지 또는 기존을 사용할 수 있습니다.
2. 인증 키 서비스 사용자를 만들어야 합니다.
3. Azure 스택 구독 참가자의 역할의 일부가 될 SPN을 허용 하도록 역할 기반 액세스 제어를 통해 유효성을 검사 해야 합니다.
4. SPN 정보 뿐만 아니라 Azure 스택 끝점을 사용 하 여 VSTS에서 새 서비스 정의 만들어야 합니다.

### <a name="service-principal-creation"></a>서비스 사용자 만들기

참조는 [서비스 사용자 만들기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 서비스 사용자를 만들고 응용 프로그램 종류에 대 한 웹 응용 프로그램/API를 선택 하기 위한 지침입니다.

### <a name="access-key-creation"></a>액세스 키 만들기

서비스 사용자 인증에 대 한 키가 필요한 키를 생성 하려면이 섹션의 단계를 수행 하십시오.


1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  값을 기록해 **응용 프로그램 ID**합니다. VSTS에서 서비스 끝점을 구성 하는 경우 해당 값을 사용 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 인증 키를 생성하려면 **설정**을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 인증 키를 생성하려면 **키**를 선택합니다.
 
    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.
 
    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다. 제공 하는 **키 값** 응용 프로그램으로 로그인 하도록 응용 프로그램 id가 있습니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>테넌트 ID 가져오기

서비스 끝점 구성의 일환으로, VSTS 필요는 **테 넌 트 ID** Azure 스택 스탬프에 배포 된 AAD 디렉터리에 해당 하는 합니다. 테 넌 트 id를 수집 하기 위해이 섹션의 단계를 수행 합니다.

1. **Azure Active Directory**를 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Azure 스택 구독에 리소스를 배포를 서비스 보안 주체 권한을 부여합니다 

구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다. 대신 리소스 그룹 또는 리소스를 선택할 수 있습니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. 선택 된 **구독** (리소스 그룹 또는 리소스) 응용 프로그램을 할당할 수 있습니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. **Access Control(IAM)** 을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. **추가**를 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. 응용 프로그램에 할당할 역할을 선택합니다. 다음 그림에서는 **소유자** 역할입니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 기본적으로 Azure Active Directory 응용 프로그램이 사용 가능한 옵션에 표시되지 않습니다. 해야 응용 프로그램을 찾으려면 **이름을 제공** 검색 필드에 있습니다. 이를 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. **저장**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

### <a name="role-based-access-control"></a>역할 기반 Access Control

Azure 역할 기반 액세스 제어 (RBAC) Azure에 대 한 세분화 된 액세스 관리를 사용할 수 있습니다. RBAC를 사용하여 사용자가 해당 작업을 수행하는 데 필요한 액세스 만큼 권한을 부여할 수 있습니다. 역할 기반 액세스 제어에 대 한 자세한 내용은 참조 [Azure 구독의 리소스에 대 한 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)합니다.

### <a name="vsts-agent-pools"></a>VSTS 에이전트 풀

각 에이전트를 개별적으로 관리 하는 대신 에이전트 풀에 에이전트를 구성 합니다. 에이전트 풀 해당 풀에 대 한 모든 에이전트에 대 한 공유 경계를 정의합니다. VSTS 계정을; 에이전트 풀 범위는 VSTS에서 따라서 팀 프로젝트에 걸쳐 에이전트 풀을 공유할 수 있습니다. 자세한 내용 및 VSTS 에이전트 풀을 만드는 방법에 대 한 자습서에 대 한 참조 [에이전트 풀 만들기 및 큐](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)합니다.

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>개인용 액세스 토큰 (P a t) Azure 스택에 대 한 추가 합니다.

1. VSTS 계정에 로그인 한 계정 프로필 이름을 선택 합니다.
2. 선택 **보안 관리** 액세스 토큰 만들기 페이지에 있습니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 토큰을 복사 합니다.
    
    > [!note]  
    > 토큰 정보를 가져옵니다. 이 화면을 종료 한 후에 다시 표시 되지 않습니다. 
    
    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure 스택에 VSTS 빌드 에이전트 설치 호스팅된 빌드 서버

1.  Azure 스택 호스트에 배포 하는 빌드 서버에 연결 합니다.

2.  다운로드 및 배포는 빌드 에이전트는 개인을 사용 하 여 서비스 액세스 토큰 (PAT) 및 VM 관리자 계정으로 실행 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 추출 된 빌드 에이전트 폴더로 이동 합니다. 실행 된 **run.cmd** 상승된 된 명령 프롬프트에서 파일입니다. 

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  run.cmd 완료 후 압축 푼된 내용 사용 하 여 폴더는 다음과 같이 표시 됩니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    이제 VSTS 폴더에 에이전트를 볼 수 있습니다.

## <a name="endpoint-creation-permissions"></a>끝점 만들기 권한

사용자가 VSTO 빌드 스택의에 Azure 서비스 앱을 배포할 수 있도록 끝점을 만들 수 있습니다. VSTS는 Azure 스택과 함께 연결 하는 빌드 에이전트에 연결 합니다. 

![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. 에 **설정** 메뉴 선택 **보안**합니다.
2. 에 **VSTS 그룹** 선택 왼쪽 목록 **의해 끝점 작성자**합니다. 

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. 에 **멤버** 탭에서 **+ 추가**합니다. 

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. 사용자 이름을 입력 하 고 목록에서 해당 사용자를 선택 합니다.
5. **변경 내용 저장**을 클릭합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. 에 **VSTS 그룹** 선택 왼쪽 목록 **끝점 관리자**합니다.
7. 에 **멤버** 탭에서 **+ 추가**합니다.
8. 사용자 이름을 입력 하 고 목록에서 해당 사용자를 선택 합니다.
9. **변경 내용 저장**을 클릭합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Azure 스택에서 빌드 에이전트는 다음 Azure 스택와의 통신에 대 한 끝점 정보를 전달 하는 VSTS에서 지침을 얻게 됩니다. 
    
    Azure 스택 연결으로 VSTS 모드로 전환 되었습니다.

## <a name="develop-your-application"></a>응용 프로그램 개발

Azure 스택와 Azure에 웹 앱을 배포 하려면 하이브리드 CI/CD를 설정 하 고 자동 푸시 두 클라우드 모두로 변경 됩니다.

> [!note]  
> Azure 스택 (Windows Server 및 SQL)를 실행 하 고 배포 하는 응용 프로그램 서비스를 게시 하는 적절 한 이미지와 함께 필요 합니다. Azure 스택 연산자 요구 사항에 대 한 앱 서비스 설명서 "전제 조건" 섹션을 검토 합니다.

### <a name="add-code-to-vsts-project"></a>VSTS 프로젝트에 코드 추가

1. Azure 스택에 대 한 프로젝트 만들기 권한이 있는 계정으로 Visual Studio에 로그인 합니다.

    하이브리드 CI/CD 응용 프로그램 코드와 인프라 코드를 적용할 수 있습니다. 사용 하 여 [웹 같은 Azure 리소스 관리자 템플릿을 ](https://azure.microsoft.com/resources/templates/) 두 클라우드와 VSTS에서 응용 프로그램 코드입니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **리포지토리 복제** 만들고 열어서 기본 웹 응용 프로그램입니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>두 클라우드 모두에서 응용 프로그램 서비스에 대 한 자체 포함된 웹 앱 배포 만들기

1. 편집 된 **WebApplication.csproj** 파일: 선택 **Runtimeidentifier** 추가 `win10-x64.` 자세한 내용은 참조 [자체 포함된 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서 .

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. VSTS 팀 탐색기를 사용 하 여 코드를 확인 하십시오.

3. Visual Studio Team Services에는 응용 프로그램 코드에 체크 인 된 있는지 확인 합니다. 

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1. 빌드 정의 만들 수 있는 기능을 확인 하려면 VSTS에 로그인 합니다.

2. 추가 **-r win10 x64** 코드입니다. 이.net 자체 포함된 배포를 트리거하는 데 필요한 핵심입니다. 

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. 빌드를 실행 합니다. [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure 스택에서 실행할 수 있는 아티팩트를 게시 합니다.

### <a name="using-an-azure-hosted-agent"></a>Azure 호스트 된 에이전트를 사용 하 여

VSTS에서 호스트 된 에이전트를 사용 하 여 빌드하고 웹 앱을 배포 하는 편리한 옵션입니다. 유지 관리 및 업그레이드는 지속적으로 중단 없이 개발, 테스트 및 배포를 사용 하도록 설정 하는 Microsoft Azure에서 자동으로 수행 됩니다.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>관리 하 고 연속 배포 (CD) 프로세스를 구성 합니다.

Visual Studio Team Services VSTS () 및 Team Foundation Server (TFS) 제공 고도로 구성 가능 하며 관리 하기 쉬운 파이프라인 릴리스를 개발와 같은 여러 환경 준비, QA, 및 프로덕션 환경입니다. 등의 특정 단계에서 승인이 필요한입니다.

### <a name="create-release-definition"></a>릴리스 정의 만들기

![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. 선택은  **\[ +]** 아래 새 릴리스를 추가 하는 **r 탭** VSO의 빌드 및 릴리스 페이지에서.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\102.png)

2. 적용 된 **Azure 앱 서비스 배포** 템플릿.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\103.png)

3. 추가 아티팩트 풀 다운 메뉴에서 **아티팩트 추가** Azure 클라우드 빌드 응용 프로그램에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\104.png)

4. 파이프라인 탭에서 선택 된 **단계**, **작업** 환경에 연결 하 고 Azure 클라우드 환경 값을 설정 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\105.png)

5. 설정의 **환경 이름** Azure를 선택 하 고 **구독** Azure 클라우드 끝점에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\106.png)

6. 환경 이름 아래에서 필요한 설정 **Azure 앱 서비스 이름은**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\107.png)

7. 입력 **호스팅된 VS2017** 에서 호스팅되는 Azure 클라우드 환경에 대 한 에이전트 큐입니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Azure 앱 서비스 배포 메뉴에서 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 선택 **확인** 를 **폴더 위치**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\110.png)

9. 모든 변경 내용을 저장 하 고 돌아가서 **릴리스 파이프라인**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\111.png)

10. 추가 **새 아티팩트가** Azure 스택 앱에 대 한 빌드를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\112.png)

11. 더 많은 환경 적용 한 추가 **Azure 앱 서비스 배포**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\113.png)

12. 새 환경의 이름을 **Azure 스택**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Azure 스택 환경에서 찾을 **작업** 탭 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\115.png)

14. 선택 된 **구독** Azure 스택 끝점에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Azure 스택 웹 응용 프로그램 이름으로 설정 된 **응용 프로그램 서비스 이름**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\117.png)

16. 선택 된 **Azure 스택 에이전트**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\118.png)

17. Azure 앱 서비스 배포에서 섹션 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 확인을 선택 **폴더 위치**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\120.png)

18. 변수 탭에서 명명 된 변수를 추가 **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, 해당 값으로 설정 **true**, 범위 및 **Azure 스택**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\121.png)

19. 선택 된 **Continuous** 배포 두 아티팩트의 아이콘 트리거되며이 시작을 계속 배포 트리거를 사용 하도록 설정 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\122.png)

20. 선택 된 **배포 전** 조건 아이콘의 azure에서 스택 환경 및 트리거 설정 **릴리스 후**합니다.

21. 모든 변경 내용을 저장 합니다.

> [!note]  
> 작업에 대 한 일부 설정을 수 자동으로 정의 된 [환경 변수](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 정의 만들 때. 작업 설정;에서 이러한 설정은 수정할 수 없습니다. 대신 이러한 설정을 편집 하려면 부모 환경 항목을 선택 해야 합니다.

## <a name="create-a-release"></a>릴리스 만들기

이제 릴리스 정의 수정 내용을 완료 하면 배포를 시작 하는 시간입니다. 이 작업을 수행 하려면 릴리스 정의에서 릴리스를 만듭니다. 릴리스를 자동으로 만들 수 있습니다. 예를 들어 연속 배포 트리거 릴리스 정의에서 설정 됩니다. 즉, 소스 코드를 수정 새 빌드를 시작 하 고이 통해 새 릴리스 합니다. 그러나이 섹션에서는 수동으로 새 릴리스를 만들는 있습니다.

1. 열기는 **릴리스** 드롭 다운 목록을 열고 **릴리스 만들기**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. 릴리스에 대 한 설명을 입력, 올바른 아티팩트 선택 되어 있는지 확인 한 다음 선택 **만들기**합니다. 몇 분 후 배너 만들어진 새로운 버전을 나타내는 표시 됩니다. 링크 (릴리스의 이름)을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. 릴리스 요약 페이지가 열립니다 릴리스의 세부 정보를 표시 합니다. 에 **환경** 섹션 "SUCCEEDED"-"진행 중"에서 변경 "QA" 환경에 대 한 배포 상태를 볼 수 있습니다 및, 릴리스 승인을 기다리고 이제 나타내는 배너를 표시 하는 해당 시점에 있습니다. 환경에 배포 보류 중이거나 실패 했습니다 (i) 옆에 파란색 정보 아이콘 표시 됩니다. 원인이 포함 된 팝업이 표시 하려면이 옵션을 가리킵니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\202.png)

릴리스 목록 등의 다른 보기도 승인을 나타내는 아이콘이 표시 보류 중입니다. 아이콘을 가리키면 환경 이름 및 자세한 정보를 포함 하는 팝업이 표시 됩니다. 이 릴리스가 기다리는으로 승인, 모든 버전의 전반적인 진행 상황을 확인 하려면 관리자가 쉽게 있습니다.

### <a name="monitor-and-track-deployments"></a>배포를 모니터링 및 추적

이 섹션에서는 나타납니다 모니터링 하는 방법 및 이전 섹션에서 만든 버전에서이 예제를 두 개의 Azure 앱 서비스 웹 사이트-배포-추적 합니다.

1. 릴리스 요약 페이지에서 선택 된 **로그** 링크 합니다. 배포 작업이 발생 하는 동안 에이전트에서 하 고, 각 환경에 대 한 배포 프로세스의 각 작업의 상태 표시의 왼쪽된 창에서이 페이지에 실시간 로그를 표시 됩니다.

    아이콘을 선택는 **작업** 누가 자세한 내용을 보려면 배포 전 또는 배포 후 승인에 대 한 열 승인 (또는 거부 됨) 메시지 및 배포, 해당 사용자가 제공 합니다.

2. 배포가 완료 되 면 전체 로그 파일은 오른쪽 창에 표시 됩니다. 중 하나를 선택는 **단계 처리** 로그 뿐 해당 단계에 대 한 파일 내용을 표시 하려면 왼쪽된 창에서. 이 추적 및 디버그 전반적인 배포의 각 부분에 쉽게 있습니다. 아이콘 및 페이지의 링크에서 개별 로그 파일 또는 로그 파일의 zip을 또는 다운로드 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. 열기는 **요약** 릴리스의 전체 세부 정보를 볼 탭 합니다. 빌드 및-배포 상태 및 릴리스에 대 한 다른 정보와 함께 배포 된 환경에 대 한 세부 정보를 표시 합니다.

4. 각 선택는 **환경 링크** 기존 하는 방법에 대 한 주소 및 해당 특정 환경에 대 한 배포가 보류 중인 자세한 정보를 확인할 수 있습니다. 이러한 페이지를 사용 하 여 동일한 빌드의 두 환경에 배포 되었는지 확인할 수 있습니다.

5. 열기는 **프로덕션 응용 프로그램 배포** 프로그램 찾아보기에 있습니다. URL에서는 Azure 앱 서비스 웹 사이트에 대 한 예를 들어 `http://[your-app-name].azurewebsites.net`합니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 참조 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
