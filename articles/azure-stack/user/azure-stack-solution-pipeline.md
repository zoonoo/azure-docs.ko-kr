---
title: Azure 및 Azure 앱 배포 스택 | Microsoft Docs
description: 하이브리드 CI/CD 파이프라인을 사용 하 여 Azure 및 Azure Stack에 앱을 배포 하는 방법에 알아봅니다.
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: febdb2e3ae4432c36ca839f81ba7a1d333df1a2f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952004"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>자습서: Azure 및 Azure에 앱을 배포 스택

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure 및 하이브리드 지속적인 통합/지속적인 업데이트 (CI/CD) 파이프라인을 사용 하 여 Azure Stack에 응용 프로그램을 배포 하는 방법에 알아봅니다.

이 자습서에서는 샘플 환경을 만듭니다.

> [!div class="checklist"]
> * Azure DevOps 서비스 리포지토리에 코드 커밋을 기반으로 새 빌드를 시작 합니다.
> * 자동으로 사용자 수용 테스트에 대 한 전역 Azure에 앱을 배포 합니다.
> * 코드 테스트에 통과 하면 자동으로 Azure Stack에 앱을 배포 합니다.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>하이브리드 배달 이점은 빌드 파이프

연속성, 보안 및 안정성은 응용 프로그램 배포의 주요 요소입니다. 이러한 요소는 조직에 중요 및 개발 팀에 중요 합니다. 하이브리드 CI/CD 파이프라인을 사용 하면 온-프레미스 환경 및 공용 클라우드 전반에 빌드 파이프를 통합할 수 있습니다. 또한 하이브리드 배달 모델을 응용 프로그램을 변경 하지 않고 배포 위치를 변경할 수 있습니다.

하이브리드 접근 방식을 사용 하 여 이점을 다음과 같습니다.

* 온-프레미스 Azure Stack 환경 및 Azure 공용 클라우드에서 일관 된 개발 도구 집합을 유지할 수 있습니다.  공통 도구 집합을 쉽게 CI/CD 패턴 및 사례를 구현 합니다.
* 앱 및 Azure 또는 Azure Stack에 배포 된 서비스는 서로 바꿔 사용할 수 및 위치 중 하나에 동일한 코드를 실행할 수 있습니다. 온-프레미스 및 공용 클라우드 기능 및 기능을 걸릴 수 있습니다.

CI 및 CD에 대 한 자세한 정보를 알아보려면:

* [지속적인 통합 이란?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [지속적인 업데이트란?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 하이브리드 운영 검토 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 조건

하이브리드 CI/CD 파이프라인 빌드를 구성 해야 합니다. 다음 구성 요소 준비 하는 데 시간이 됩니다.

* Azure OEM/하드웨어 파트너는 Azure Stack 프로덕션에 배포할 수 있습니다. 모든 사용자는 Azure Stack 개발 키트 ASDK ()를 배포할 수 있습니다.
* Azure Stack 운영자에도 있어야 합니다: App Service 배포를 계획 및 제품은 테 넌 트 구독을 만들고 Windows Server 2016 이미지를 추가 합니다.

>[!NOTE]
>이미 있는 경우 이러한 구성 요소 배포의 일부에 모든 요구 사항을 만족할이 자습서를 시작 하기 전에 있는지 확인 합니다.

이 자습서에서는 Azure 및 Azure Stack에 대 한 기본 지식이 있다고 가정 합니다. 이 자습서를 시작 하기 전에 자세한 내용은 다음 문서를 참조 합니다.

* [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure Stack의 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 만들기는 [웹 앱](https://docs.microsoft.com/azure/app-service/app-service-web-overview) Azure에서. 웹 앱 url을 확인, 자습서에서 사용 해야 합니다.

### <a name="azure-stack-requirements"></a>Azure Stack 요구 사항

* Azure Stack 통합 시스템을 사용 하거나 Azure Stack 개발 키트 (ASDK)를 배포 합니다. ASDK 배포:
    * 합니다 [자습서: 설치 관리자를 사용 하 여 ASDK 배포](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) 상세한 배포 지침을 제공 합니다.
    * 사용 된 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) ASDK 배포 후 단계를 자동화 하도록 PowerShell 스크립트입니다.

    > [!Note]
    > ASDK 설치는 적절 하 게 계획을 완료 하려면 약 7 시간입니다.

 * 배포할 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure Stack에 PaaS 서비스입니다.
 * 만들 [계획/제품](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure Stack의 합니다.
 * 만들기는 [테 넌 트 구독](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure Stack에서.
 * 테 넌 트 구독에 웹 앱을 만듭니다. 나중에 사용 하 여 새 웹 앱 url에 대 한 참고를 확인 합니다.
 * 테 넌 트 구독에는 Windows Server 2012 가상 머신을 배포 합니다. Azure DevOps 서비스를 실행 하 여 빌드 서버와이 서버를 사용 합니다.
* 가상 컴퓨터 (VM)를 위한.NET 3.5를 사용 하 여 Windows Server 2016 이미지를 제공 합니다. 개인 빌드 에이전트로이 VM에 Azure Stack에서 빌드됩니다.

### <a name="developer-tool-requirements"></a>개발자 도구 요구 사항

* 만들기는 [Azure DevOps 서비스 작업 영역](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces)합니다. 등록 프로세스 라는 프로젝트를 만듭니다 **MyFirstProject**합니다.
* [Visual Studio 2017 설치](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 하 고 [Azure DevOps 서비스에 로그인](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)합니다.
* 프로젝트에 연결 하 고 [로컬로 복제할](https://www.visualstudio.com/docs/git/gitquickstart)합니다.

 > [!Note]
 > Azure Stack 환경과 Windows Server 및 SQL Server를 실행 하려면 신디케이티드 올바른 이미지가 필요 합니다. App Service에서 배포도 있어야 합니다.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Azure DevOps 서비스 통합에 대 한 개인 Azure 파이프라인 에이전트 준비

### <a name="prerequisites"></a>필수 조건

Azure DevOps 서비스에 대 한 Azure Resource Manager는 서비스 주체를 사용 하 여 인증 합니다. Azure DevOps 서비스에 있어야 합니다 **참가자** Azure Stack 구독에 리소스를 프로 비전 하는 역할입니다.

다음 단계 인증을 구성 하는 데 필요한 항목을 설명 합니다.

1. 서비스 주체를 만들거나 기존 서비스 주체를 사용 합니다.
2. 서비스 주체에 대 한 인증 키를 만듭니다.
3. 주체 이름 SPN (서비스)는 참가자 역할의 일부가 되도록 수 있도록 역할 기반 Access Control 통해 Azure Stack 구독을 확인 합니다.
4. Azure Stack 끝점 및 SPN 정보를 사용 하 여 Azure DevOps 서비스에서 새 서비스 정의 만듭니다.

### <a name="create-a-service-principal"></a>서비스 주체 만들기

참조를 [서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 를 서비스 주체를 만드는 지침입니다. 선택할 **웹 앱/a p I** 응용 프로그램 형식 또는 [PowerShell 스크립트를 사용 하 여](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) 문서에 설명 된 대로 [기존 서비스를 사용 하 여 Azure Resource Manager 서비스 연결을 만들기 주 ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal)합니다.

 > [!Note]  
 > 전달 해야 하는 Azure Stack Azure Resource Manager 끝점을 만드는 스크립트를 사용 합니다 **-azureStackManagementURL** 매개 변수 및 **-environmentName** 매개 변수입니다. 예:   
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>액세스 키 만들기

서비스 주체 인증에 대 한 키가 필요 합니다. 다음 단계를 사용 하 여 키를 생성 합니다.

1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

    ![응용 프로그램 선택](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. 값을 기록해 **응용 프로그램 ID**합니다. Azure DevOps 서비스에서 서비스 끝점을 구성 하는 경우 해당 값을 사용 합니다.

    ![응용 프로그램 UI](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 인증 키를 생성하려면 **설정**을 선택합니다.

    ![앱 설정 편집](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 인증 키를 생성하려면 **키**를 선택합니다.

    ![키 설정 구성](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 키에 대 한 설명을 입력 하 고 키의 기간을 설정 합니다. 완료되면 **저장**을 선택합니다.

    ![키 설명 및 기간](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    키, 키를 저장 한 후 **값** 표시 됩니다. 이 값을 나중에 가져올 수 없습니다 때문에이 값을 복사 합니다. 제공 하는 **키 값** 응용 프로그램으로 로그인 하려면 응용 프로그램 id입니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

    ![키 값](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>테 넌 트 ID를 가져옵니다.

Azure DevOps 서비스를 실행 하려면 서비스 끝점 구성의 일부로 합니다 **테 넌 트 ID** 에 Azure Stack 스탬프에 배포 되는 AAD 디렉터리에 해당 하는 합니다. 다음 단계를 사용 하 여 id입니다. 테 넌 트 가져오기

1. **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 테 넌 트에 대 한](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다.

    ![테 넌 트 속성 보기](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

    ![디렉터리 ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Azure Stack 구독에 리소스를 배포 하려면 서비스 주체 권한 부여

구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 대 한 최상의 권한을 나타내는 역할을 결정 합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어, 리소스 그룹에 대 한 읽기 권한자 역할에 응용 프로그램을 추가할 리소스 그룹 및 해당 리소스를 읽을 수 의미 합니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

    ![구독 선택](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. **구독**, Visual Studio Enterprise를 선택 합니다.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Visual Studio Enterprise에서 선택 **액세스 제어 (IAM)** 합니다.

    ![액세스 제어 (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. **추가**를 선택합니다.

    ![추가](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. **권한 추가**, 역할을 선택 하는 응용 프로그램에 할당 하려면. 이 예제는 **소유자** 역할입니다.

    ![소유자 역할](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 기본적으로 Azure Active Directory 응용 프로그램이 사용 가능한 옵션에 표시되지 않습니다. 응용 프로그램을 찾으려면에서 해당 이름을 제공 해야 합니다는 **선택** 필드를 검색 합니다. 앱을 선택합니다.

    ![앱 검색 결과](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. **저장**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

### <a name="role-based-access-control"></a>역할 기반 Access Control

Azure 역할 기반 Access Control (RBAC)는 Azure에 대 한 세분화 된 액세스 관리를 제공합니다. RBAC를 사용 하 여 사용자가 작업을 수행 해야 하는 액세스 수준을 제어할 수 있습니다. 역할 기반 Access Control에 대 한 자세한 내용은 참조 하세요. [Azure 구독 리소스에 대 한 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)합니다.

### <a name="azure-devops-services-agent-pools"></a>Azure DevOps 서비스 에이전트 풀

각 에이전트를 별도로 관리 하는 대신 에이전트 풀에 에이전트를 구성할 수 있습니다. 해당 풀의 모든 에이전트에 대 한 공유 경계를 정의 하는 에이전트 풀입니다. Azure DevOps 서비스에서 에이전트 풀 에이전트 풀을 프로젝트 간에 공유할 수 있습니다 즉 Azure DevOps 서비스 조직에 범위가 지정 됩니다. 에이전트 풀에 대 한 자세한 내용은 참조 하세요 [에이전트 풀 만들기 및 큐](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts)합니다.

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Azure Stack에 대 한 개인용 액세스 토큰 (PAT)를 추가 합니다.

개인 액세스 토큰을 Azure DevOps 서비스에 액세스 하도록 만듭니다.

1. Azure DevOps 서비스 조직에 로그인 하 고 조직 프로필 이름을 선택 합니다.

2. 선택 **보안 관리** 액세스 토큰 생성 페이지에 있습니다.

    ![사용자 로그인](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![프로젝트 선택](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![개인용 액세스 토큰 추가](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![토큰 만들기](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 토큰을 복사 합니다.

    > [!Note]
    > 토큰 정보를 저장 합니다. 이 정보는 저장 되지 않습니다 하 고 다시 표시 되지 않습니다 웹 페이지에서 벗어날 때.

    ![개인용 액세스 토큰](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure Stack에서 Azure DevOps 서비스 빌드 에이전트 설치 호스팅된 빌드 서버

1. Azure Stack 호스트에 배포 된 빌드 서버에 연결 합니다.
2. 다운로드 및 배포 개인을 사용 하 여 서비스로 빌드 에이전트 액세스 토큰 (PAT) 및 VM 관리자 계정으로 실행 합니다.

    ![빌드 에이전트를 다운로드 합니다.](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 추출 된 빌드 에이전트에 대 한 폴더로 이동 합니다. 실행 합니다 **config.cmd** 관리자 권한 명령 프롬프트에서 파일입니다.

    ![추출 된 빌드 에이전트](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![빌드 에이전트를 등록 합니다.](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. config.cmd 완료 되 면 빌드 에이전트 폴더를 추가 파일을 사용 하 여 업데이트 됩니다. 압축 푼된 콘텐츠의 폴더는 다음과 같아야 합니다.

    ![빌드 에이전트 폴더 업데이트](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Azure DevOps 서비스 폴더에서 에이전트를 볼 수 있습니다.

## <a name="endpoint-creation-permissions"></a>끝점 만들기 권한

끝점을 만들면 Visual Studio Online (VSTO) 빌드는 Azure Stack에 Azure 서비스 앱을 배포할 수 있습니다. Azure DevOps 서비스는 Azure Stack에 연결 하는 빌드 에이전트에 연결 합니다.

![VSTO에서 NorthwindCloud 샘플 앱](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. VSTO에 로그인 하 고 앱 설정 페이지로 이동 합니다.
2. 온 **설정을**를 선택 **보안**합니다.
3. **Azure DevOps 서비스 그룹**를 선택 **의해 끝점 작성자**합니다.

    ![NorthwindCloud 끝점 작성자](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. 에 **멤버** 탭을 선택 **추가**합니다.

    ![멤버 추가](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.
6. **변경 내용 저장**을 선택합니다.
7. 에 **Azure DevOps 서비스 그룹** 목록에서 **끝점 관리자**합니다.

    ![NorthwindCloud 끝점 관리자](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. 에 **멤버** 탭을 선택 **추가**합니다.
9. **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.
10. **변경 내용 저장**을 선택합니다.

끝점 정보가 했으므로 Azure DevOps 서비스가 Azure Stack 연결에 사용할 준비가 되었습니다. Azure Stack에서 빌드 에이전트는 지침을 Azure DevOps 서비스에서 가져오고 에이전트에서 Azure Stack을 사용 하 여 통신에 대 한 끝점 정보를 전달 하는 다음 합니다.
## <a name="create-an-azure-stack-endpoint"></a>Azure Stack 끝점 만들기

지침에 따르면 [Azure Resource Manager 서비스 연결을 기존 서비스 주체 만들기 ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) 문서에서는 서비스 연결을 사용 하 여 기존 서비스 주체 만들고 다음 매핑을 사용 합니다.

- 환경: azurestack의 경우
- 환경 URL 같이 `https://management.local.azurestack.external`
- Azure Stack에서 구독 ID: 사용자 구독 ID
- 구독 이름: Azure Stack에서 사용자 구독 이름
- 서비스 주체 클라이언트 ID:에서 보안 주체 ID [이](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) 이 문서의 섹션입니다.
- 서비스 주체 키: 동일한 문서 (또는 스크립트를 사용 하는 경우 암호)의 키입니다.
- 테 넌 트 ID: 테 넌 트 ID를 검색할 있습니다 명령에 다음 [테 넌 트 ID 가져오기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id)합니다.

이제 끝점을 만들었으므로 VSTS Azure Stack 연결에 사용할 준비가 되었습니다. Azure Stack에서 빌드 에이전트는 VSTS에서 지침을 가져옵니다. 에이전트에서 Azure Stack을 사용 하 여 통신에 대 한 끝점 정보를 전달 하는 다음을

![빌드 에이전트](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>응용 프로그램 빌드를 개발 합니다.

이 자습서의이 부분에서는 다음을 수행 해야합니다.

* Azure DevOps 서비스 프로젝트에 코드를 추가 합니다.
* 자체 포함 된 웹 앱 배포를 만듭니다.
* 연속 배포 프로세스를 구성 합니다.

> [!Note]
 > Azure Stack 환경과 Windows Server 및 SQL Server를 실행 하려면 신디케이티드 올바른 이미지가 필요 합니다. App Service에서 배포도 있어야 합니다. Azure Stack 연산자 요구 사항에 대 한 App Service 설명서 "전제 조건" 섹션을 검토 합니다.

CI/CD 하이브리드 응용 프로그램 코드와 인프라 코드를 적용할 수 있습니다. 사용 하 여 [웹과 같은 Azure Resource Manager 템플릿 ](https://azure.microsoft.com/resources/templates/) 두 클라우드 모두에 배포 하려면 Azure DevOps 서비스에서 앱 코드입니다.

### <a name="add-code-to-an-azure-devops-services-project"></a>Azure DevOps 서비스 프로젝트에 코드 추가

1. Azure Stack에서 프로젝트 만들기 권한이 있는 조직을 사용 하 여 Azure DevOps 서비스에 로그인 합니다. 다음 화면 캡처 HybridCICD 프로젝트에 연결 하는 방법을 보여 줍니다.

    ![프로젝트에 연결](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **리포지토리를 복제 합니다** 만들어 기본 웹 앱을 열어서 합니다.

    ![리포지토리 복제](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>두 클라우드 모두에서 App Services에 대 한 자체 포함 된 웹 앱 배포 만들기

1. 편집 합니다 **WebApplication.csproj** 파일: 선택 **runtimeidentifier-** 추가한 `win10-x64.` 자세한 내용은 참조 하십시오 [자체 포함된 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서입니다.

    ![Runtimeidentifier-구성](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 팀 탐색기를 사용 하 여 Azure DevOps 서비스에 코드를 확인 합니다.

3. Azure DevOps 서비스에 응용 프로그램 코드를 체크 인 있는지 확인 합니다.

### <a name="create-the-build-pipeline"></a>빌드 파이프라인 만들기

1. 빌드 파이프라인을 만들 수 있는 조직을 사용 하 여 Azure DevOps 서비스에 로그인 합니다.

2. 로 이동 합니다 **웹 응용 프로그램 빌드** 프로젝트에 대 한 페이지입니다.

3. **인수**에 추가 **-r win10-x64** 코드입니다. .NET Core를 사용 하 여 자체 포함된 배포를 트리거하려면 반드시 확인 해야 합니다.

    ![인수 빌드 파이프라인 추가](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. 빌드를 실행 합니다. 합니다 [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure Stack에서 실행 될 수 있는 아티팩트를 게시 합니다.

### <a name="use-an-azure-hosted-build-agent"></a>Azure를 사용 하 여 호스트 된 빌드 에이전트

Azure DevOps 서비스에서 호스트 된 빌드 에이전트를 사용 하는 것은 웹 앱 빌드 및 배포에 대 한 편리 합니다. 에이전트 유지 관리 및 업그레이드를 중단 없이 연속적으로 연속 개발 주기를 사용 하도록 설정 하는 Microsoft Azure에서 자동으로 수행 됩니다.

### <a name="configure-the-continuous-deployment-cd-process"></a>연속 배포 (CD) 프로세스를 구성 합니다.

Azure DevOps 서비스 및 Team Foundation Server (TFS)는 항상 구성 및 관리 가능한 파이프라인 개발, 스테이징, QA (품질 보증), 및 프로덕션과 같은 여러 환경에 릴리스를 제공합니다. 이 프로세스는 응용 프로그램 수명 주기의 특정 단계에서 승인 필요를 포함할 수 있습니다.

### <a name="create-release-pipeline"></a>릴리스 파이프라인 만들기

릴리스 파이프라인을 만드는 경우 응용 프로그램의 마지막 단계는 빌드 프로세스 이 릴리스 파이프라인은 릴리스를 만들고 빌드 배포에 사용 됩니다.

1. Azure DevOps 서비스에 로그인 하 고 이동할 **Azure 파이프라인** 프로젝트에 대 한 합니다.
2. 에 **릴리스에서** 탭을 선택  **\[ +]** 선택 하 고 **릴리스 정의 만들기**합니다.

   ![릴리스 파이프라인 만들기](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. **템플릿을 선택**, 선택 **Azure App Service 배포**를 선택한 후 **적용**합니다.

    ![템플릿 적용](media\azure-stack-solution-hybrid-pipeline\102.png)

4. **아티팩트 추가**에서 **원본 (빌드 정의)** 풀 다운 메뉴에서 Azure 클라우드 빌드 앱을 선택 합니다.

    ![아티팩트 추가](media\azure-stack-solution-hybrid-pipeline\103.png)

5. 에 **파이프라인** 탭을 선택 합니다 **1 단계**, **작업이 1 개** 연결할 **환경 작업 보기**.

    ![파이프라인 보기 작업](media\azure-stack-solution-hybrid-pipeline\104.png)

6. 에 **작업** 탭을 입력으로 Azure는 **환경 이름** AzureCloud Traders 웹 EP에서 선택 하 고는 **Azure 구독** 드롭 다운 목록.

    ![환경 변수 설정](media\azure-stack-solution-hybrid-pipeline\105.png)

7. 입력 합니다 **Azure app service 이름**, 다음 화면 캡처에 표시 된 "northwindtraders"입니다.

    ![App service 이름](media\azure-stack-solution-hybrid-pipeline\106.png)

8. 에이전트 단계에 대 한 선택 **호스팅된 VS2017** 에서 합니다 **에이전트 큐** 드롭 다운 목록.

    ![호스트 된 에이전트](media\azure-stack-solution-hybrid-pipeline\107.png)

9. **Azure App Service 배포**, 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다.

    ![패키지 또는 폴더 선택](media\azure-stack-solution-hybrid-pipeline\108.png)

10. **선택 파일 또는 폴더**를 선택 **확인** 하 **위치**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\109.png)

11. 모든 변경 내용을 저장 하 고로 돌아가서 **파이프라인**합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\110.png)

12. 에 **파이프라인** 탭을 선택 **아티팩트 추가**, 선택 합니다 **NorthwindCloud Traders 선박** 에서 **소스 (빌드 정의)** 드롭 다운 목록.

    ![새 아티팩트 추가](media\azure-stack-solution-hybrid-pipeline\111.png)

13. 온 **템플릿을 선택**, 다른 환경을 추가 합니다. 선택할 **Azure App Service 배포** 선택한 후 **적용**합니다.

    ![템플릿 선택](media\azure-stack-solution-hybrid-pipeline\112.png)

14. "Azure Stack"으로 입력 합니다 **환경 이름**합니다.

    ![환경 이름](media\azure-stack-solution-hybrid-pipeline\113.png)

15. 에 **작업** 탭, 찾기 및 Azure Stack을 선택 합니다.

    ![Azure Stack 환경](media\azure-stack-solution-hybrid-pipeline\114.png)

16. **Azure 구독** 드롭 다운 목록에서 Azure Stack 끝점에 대 한 "AzureStack Traders 선박 EP"를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Azure Stack 웹 앱 이름으로 입력 합니다 **App service 이름**합니다.

    ![App service 이름](media\azure-stack-solution-hybrid-pipeline\116.png)

18. 아래 **에이전트 선택**에서 "azurestack의 경우-bDouglas Fir"를 선택 합니다 **에이전트 큐** 드롭 다운 목록.

    ![에이전트를 선택 합니다.](media\azure-stack-solution-hybrid-pipeline\117.png)

19. 에 대 한 **Azure App Service 배포**, 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 온 **파일 또는 폴더 선택**를 선택 **확인** 폴더에 대 한 **위치**합니다.

    ![패키지 또는 폴더 선택](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![위치를 승인 합니다.](media\azure-stack-solution-hybrid-pipeline\119.png)

20. 에 **변수에** 탭에서 명명 된 변수를 찾을 **VSTS_ARM_REST_IGNORE_SSL_ERRORS**합니다. 변수 값을 설정 합니다 **true**, 해당 범위를 설정 하 고 **Azure Stack**합니다.

    ![변수를 구성 합니다.](media\azure-stack-solution-hybrid-pipeline\120.png)

21. 에 **파이프라인** 탭을 선택 합니다 **연속 배포 트리거** 집합과 NorthwindCloud Traders 웹 아티팩트에 대 한 아이콘을 **연속 배포 트리거** 를 **활성화**합니다.  "NorthwindCloud Traders-선박" 아티팩트에 대 한 동일한 작업을 수행 합니다.

    ![연속 배포 트리거 설정](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Azure Stack 환경에 대해 선택 된 **배포 전 조건** 아이콘 트리거를 설정 **릴리스 후**합니다.

    ![배포 전 조건 트리거 설정](media\azure-stack-solution-hybrid-pipeline\122.png)

23. 변경 내용을 모두 저장합니다.

> [!Note]
> 릴리스 작업에 대 한 일부 설정을 수 자동으로 정의 된 [환경 변수](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 파이프라인을 만들 때. 작업 설정에서 이러한 설정은 수정할 수 없습니다. 그러나 부모 환경 항목에서 이러한 설정을 편집할 수 있습니다.

## <a name="create-a-release"></a>릴리스를 만듭니다.

이제 릴리스 파이프라인에 수정 작업을 완료 하면 배포를 시작 하는 시간입니다. 이 작업을 수행 하려면 릴리스 파이프라인에서 릴리스를 만들어야 합니다. 릴리스를 자동으로 만들 수 있습니다. 예를 들어, 연속 배포 트리거는 릴리스 파이프라인에서 설정 됩니다. 즉, 소스 코드를 수정 합니다. 새 빌드를 시작 하는 새 릴리스에서. 그러나이 섹션에서 수동으로 새 릴리스를 만들가 있습니다.

1. 에 **파이프라인** 탭을 열고 합니다 **릴리스** 드롭 다운 나열 하 고 선택 **릴리스 만들기**.

    ![릴리스를 만듭니다.](media\azure-stack-solution-hybrid-pipeline\200.png)

2. 릴리스에 대 한 설명을 입력, 올바르지 아티팩트 선택 되어 있는지 확인 하 고 선택한 **만들기**합니다. 배너를 몇 분 후 새 릴리스를 만든 및 릴리스 이름의 링크로 표시 되는 나타내는 표시 됩니다. 릴리스 요약 페이지를 보려면이 링크를 선택 합니다.

    ![릴리스 생성 배너](media\azure-stack-solution-hybrid-pipeline\201.png)

3. 릴리스 요약 페이지에 대 한 릴리스에 대 한 세부 정보를 보여 줍니다. "릴리스-2"에 대 한 다음 화면 캡처에는 **환경** 섹션 보여줍니다 합니다 **배포 상태** "IN PROGRESS", Azure Stack에 대 한 상태와 Azure가 "SUCCEEDED"에 대 한 합니다. Azure 환경에 대 한 배포 상태를 "SUCCEEDED"로 변경 되 면 릴리스 승인 준비가 되었음을 나타내는 배너가 나타납니다. 때 배포 보류 중이거나 실패 한 파란색 **(i)** 정보 아이콘이 표시 됩니다. 지연 또는 오류에 대 한 이유를 포함 하는 팝업이 표시 아이콘을 마우스로 가리킵니다.

    ![릴리스 요약 페이지](media\azure-stack-solution-hybrid-pipeline\202.png)

목록과 같은 다른 보기 릴리스를 승인 보류 중임을 나타내는 아이콘도 표시 됩니다. 이 아이콘에 대 한 팝업 환경 이름 및 배포와 관련 된 자세한 정보를 보여 줍니다. 관리자로 릴리스와 릴리스에서 승인을 받기 위해 대기 하 고 있는 참조의 전반적인 진행 상황을 확인할에 대 한 것은 쉽습니다.

### <a name="monitor-and-track-deployments"></a>배포를 모니터링 및 추적

이 섹션에는 모니터링 하 고 모든 배포를 추적할 수 있습니다 하는 방법을 보여 줍니다. 두 개의 Azure App Services 웹 사이트를 배포 하기 위한 릴리스 좋은 예를 제공 합니다.

1. "Release-2" 요약 페이지에서 선택 **로그**합니다. 이 페이지를 배포 하는 동안 에이전트에서 실시간 로그를 보여 줍니다. 왼쪽된 창에는 각 환경에 대 한 배포의 각 작업의 상태를 보여 줍니다.

    사용자 아이콘을 선택할 수 있습니다는 **작업** 열 배포를 승인 (또는 거부)가 참조 하는 제공 된 메시지는 배포 전 또는 배포 후 승인 합니다.

2. 배포가 완료 되 면 전체 로그 파일은 오른쪽 창에 표시 됩니다. 선택할 수 있습니다 **단계** 초기화 "작업을" 예: 한 번에 대 한 로그 파일을 보려면 왼쪽된 창에서. 개별 로그를 참조 하는 기능 쉽게 추적 및 전체 배포의 부분을 디버깅 합니다. 할 수도 있습니다 **저장할** 단계에 대 한 로그 파일 또는 **모든 로그를 zip으로 다운로드**합니다.

    ![릴리스 로그](media\azure-stack-solution-hybrid-pipeline\203.png)

3. 엽니다는 **요약** 릴리스에 대 한 일반 정보를 보려면 탭 합니다. 이 보기는 빌드, 배포 된 환경, 배포 상태 및 릴리스에 대 한 기타 정보에 대 한 정보를 표시 합니다.

4. 환경 링크를 선택 (**Azure** 하거나 **Azure Stack**) 특정 환경에 배포 보류 중 및 기존에 대 한 정보를 확인 합니다. 두 환경 모두에 배포 된 동일한 빌드를 확인 하는 빠른 방법으로 이러한 뷰를 사용할 수 있습니다.

5. 엽니다는 **프로덕션 앱을 배포할** 브라우저에서 합니다. 예를 들어, Azure App Services 웹 사이트에 대 한 URL을 열어 `http://[your-app-name].azurewebsites.net`합니다.

## <a name="next-steps"></a>다음 단계

* Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
