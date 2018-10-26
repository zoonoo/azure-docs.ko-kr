---
title: Azure를 사용 하 여 클라우드 간 크기 조정 솔루션 만들기 | Microsoft Docs
description: Azure와 클라우드 간 크기 조정 솔루션을 만드는 방법에 알아봅니다.
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
ms.openlocfilehash: 68021d25e3deab1c6551d29b0febd9f07e144b57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095341"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>자습서: Azure와 클라우드 간 크기 조정 솔루션 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 전환 프로세스를 수동으로 트리거된 웹 앱을 호스트, azure 부하 상태에서 유연 하 고 확장 가능한 클라우드 유틸리티를 확인 하 고 traffic manager를 통해 자동 크기 조정을 사용 하 여 웹 앱을 호스트를 제공 하도록 클라우드 간 솔루션을 만드는 방법에 알아봅니다.

이 패턴에서는 테 넌 트 공용 클라우드에서 응용 프로그램을 실행할 준비가 되지 않을 수도 있습니다. 그러나 없을 경제적으로 온-프레미스 환경에서 앱에 대 한 수요 급증을 처리 하는 데 필요한 용량을 유지 하기 위해 비즈니스에 적합 합니다. 테 넌 트 수는 온-프레미스 솔루션을 사용 하 여 공용 클라우드의 탄력성을 사용 합니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 다중 노드 웹 응용 프로그램을 만듭니다.
> - 구성 및 지속적인 배포 (CD) 프로세스를 관리 합니다.
> - Azure Stack에 웹 앱을 게시 합니다.
> - 릴리스를 만듭니다.
> - 모니터링 하 고 배포를 추적 하는 방법을 알아봅니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 운영에 대 한 검토 하이브리드 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 조건

-   동작합니다. 필요한 경우 만듭니다는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 시작 하기 전에 합니다.

- Azure Stack 통합 시스템 또는 Azure Stack 개발 키트 배포 합니다.
    - Azure Stack에서 설치 지침을 찾을 [Azure Stack Development Kit 설치](/articles/azure-stack/asdk/asdk-install)합니다.
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 이 설치를 완료 하려면 몇 시간 필요할 수 있습니다.

-   배포할 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure Stack에 PaaS 서비스입니다.

-   [계획/제공 사항을 만듭니다](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure Stack 환경 내에서.

-   [테 넌 트 구독 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure Stack 환경 내에서.

-   테 넌 트 구독 내에서 웹 앱을 만듭니다. 나중에 사용 하 여 새 웹 앱 url에 대 한 참고를 확인 합니다.

-   테 넌 트 구독 내에서 VSTS 가상 컴퓨터를 배포 합니다.

-   필수.NET 3.5를 사용 하 여 Windows Server 2016 VM입니다. 이 VM은 개인 빌드 에이전트와 Azure Stack에서 테 넌 트 구독에 빌드됩니다.

-   [SQL 2017 VM 이미지를 사용 하 여 Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) 는 Azure Stack Marketplace에서 제공 됩니다. 이 이미지는 사용할 수 있는, 환경에 추가 되었는지 확인 하는 Azure Stack 연산자를 사용 하 여 작동 되지 않습니다.

## <a name="issues-and-considerations"></a>문제 및 고려 사항

### <a name="scalability-considerations"></a>확장성 고려 사항

클라우드 간 크기 조정의 핵심 구성 요소 공용 사이의 즉시, 주문형으로 크기 조정을 제공 하는 기능 및 온-프레미스 클라우드 인프라, 지시에 따라 수요를 일관 되 고 신뢰할 수 있는 서비스를 입증 합니다.

### <a name="availability-considerations"></a>가용성 고려 사항

로컬로 배포 된 앱 온-프레미스 하드웨어 구성과 소프트웨어 배포를 통해 고가용성에 대해 구성 된 확인 합니다.

### <a name="manageability-considerations"></a>관리 효율성 고려 사항

클라우드 간 솔루션도 원활히 관리할 및 환경 간에 친숙 한 인터페이스를 확인합니다. 플랫폼 간 관리를 위한 PowerShell은 사용 하는 것이 좋습니다.

## <a name="cross-cloud-scaling"></a>클라우드 간 크기 조정

### <a name="obtain-a-custom-domain-and-configure-dns"></a>사용자 지정 도메인을 가져오고 DNS 구성

도메인에 대 한 DNS 영역 파일을 업데이트 합니다. Azure AD는 사용자 지정 도메인 이름의 소유권을 확인 합니다. 사용 하 여 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure 내에서 Azure/Office 365/외부 DNS 레코드에서 DNS 항목을 추가 하거나 [다른 DNS 등록 기관](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)합니다.

1.  공용 등록을 사용 하 여 사용자 지정 도메인을 등록 합니다.

2.  도메인에 대한 도메인 이름 등록 기관에 로그인합니다. 승인된 관리자 DNS 업데이트를 확인 해야 할 수 있습니다. 

3.  Azure AD가 제공한 DNS 항목을 추가 하 여 도메인에 대 한 DNS 영역 파일을 업데이트 합니다. (DNS 항목은 영향을 주지 메일 라우팅이나 웹 호스팅 동작 합니다.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Azure Stack의 기본 다중 노드 웹 앱 만들기

Azure 및 Azure Stack에 웹 앱을 배포 하려면 하이브리드 연속 통합 및 지속적인 배포 (CI/CD)를 설정 하 고 두 클라우드 모두에 변경 내용 푸시 자동 키를 누릅니다.

> [!Note]  
> 신디케이티드 실행 (Windows Server 및 SQL) 및 App Service 배포에 적절 한 이미지를 사용 하 여 azure Stack가 필요 합니다. App Service 설명서를 검토 합니다. "[App Service on Azure Stack을 사용 하 여 시작 하기 전에](/articles/azure-stack/azure-stack-app-service-before-you-get-started)" Azure Stack 운영자에 대 한 섹션입니다.

### <a name="add-code-to-visual-studio-team-services-project"></a>Visual Studio 팀에 코드 추가 Services 프로젝트

1. VSTS에 대 한 프로젝트 만들기 권한이 있는 계정으로 Visual Studio Team Services (VSTS)을 하에 로그인 합니다.

    CI/CD 하이브리드 응용 프로그램 코드와 인프라 코드를 적용할 수 있습니다. 사용 하 여 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/) 모두 사설 및 호스트 된 클라우드 개발에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image1.JPG)

2. **리포지토리를 복제 합니다** 만들어 기본 웹 앱을 열어서 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>두 클라우드 모두에서 App Services에 대 한 자체 포함 된 웹 앱 배포 만들기

1.  편집 된 **WebApplication.csproj** 파일입니다. 선택 **runtimeidentifier-** 추가한 **win10-x64**합니다. (참조 [자체 포함 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서입니다.) 

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image3.png)

2.  팀 탐색기를 사용 하 여 VSTS 코드에서 확인 합니다.

3.  Visual Studio Team Services에 응용 프로그램 코드에 체크 인 된 있는지 확인 합니다.

## <a name="create-the-build-definition"></a>빌드 정의 만들기

1. 빌드 정의를 만들 수 있는 기능을 확인 하는 VSTS에 로그인 합니다.

2. 추가 **-r win10-x64** 코드입니다. .NET Core를 사용 하 여 자체 포함된 배포를 트리거하는 데 필요한입니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image4.png)

3. 빌드를 실행 합니다. 합니다 [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure Stack에서 실행 될 수 있는 아티팩트를 게시 합니다.

## <a name="use-an-azure-hosted-agent"></a>Azure를 사용 하 여 호스트 된 에이전트

VSTS에서 호스트 된 에이전트를 사용 하 여는 웹 앱 빌드 및 배포 하는 편리한 옵션입니다. 유지 관리 및 업그레이드는 Microsoft Azure에 지속적인 중단 없이 개발, 테스트 및 배포를 사용 하도록 설정 하 여 자동으로 수행 됩니다.

### <a name="manage-and-configure-the-cd-process"></a>관리 하 고 CD 프로세스를 구성 합니다.

Visual Studio Team Services 및 Team Foundation Server (TFS)는 항상 구성 및 관리 가능한 파이프라인에 대 한 제공 개발과 같은 여러 환경에 릴리스 스테이징, QA 및 프로덕션 환경; 특정 단계에서 승인 필요를 포함 합니다.

## <a name="create-release-definition"></a>릴리스 정의 만들기

![대체 텍스트](media\azure-stack-solution-cloud-burst\image5.png)

1.  선택 합니다 **plus** 아래에서 새 릴리스를 추가 하려면 단추를 **릴리스 탭** VSO의 빌드 및 릴리스 페이지에서.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image6.png)

2. Azure App Service 배포 템플릿을 적용 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image7.png)

3. 아티팩트 추가에서 Azure 클라우드 앱의 빌드 아티팩트를 추가 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image8.png)

4. 파이프라인 탭을 선택 합니다 **단계를 작업** 환경에 연결 하 고 Azure 클라우드 환경 값을 설정 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image9.png)

5. 설정 된 **환경 이름** 선택한 Azure **구독** Azure 클라우드 끝점에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image10.png)

6. 환경 이름 set 필수 **Azure app service 이름**합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image11.png)

7. 입력 **호스팅된 VS2017** Azure 클라우드 호스팅 환경에 대 한 에이전트 큐에서 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image12.png)

8. Azure App Service 배포 메뉴에서 선택 된 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 선택 **확인** 하 **폴더 위치**합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image13.png)

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image14.png)

9. 모든 변경 내용을 저장 하 고로 돌아가서 **릴리스 파이프라인**합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image15.png)

10. Azure Stack 앱에 대 한 빌드를 선택 하면 새 아티팩트를 추가 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image16.png)

11. Azure App Service 배포를 적용 하는 하나의 자세한 환경을 추가 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image17.png)

12. 새 Azure Stack 환경을 이름을 지정 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image18.png)

13. Azure Stack 환경에서 찾을 **태스크** 탭 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image19.png)

14. Azure Stack 끝점에 대 한 구독을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image20.png)

15. Azure Stack에 웹 앱 이름이 앱 서비스 이름으로 설정 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image21.png)

16. Azure Stack 에이전트를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image22.png)

17. 배포할 Azure App Service 섹션 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 선택 **확인** 폴더 위치에 있습니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image23.png)

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image24.png)

18. 변수 탭에서 명명 된 변수를 추가 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, 해당 값으로 설정 **true**, 및 Azure Stack에 대 한 범위입니다.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image25.png)

19. 선택 합니다 **연속** 아티팩트와 enable 배포 트리거 아이콘 합니다 **계속** 배포 트리거.

    ![대체 텍스트](media\azure-stack-solution-cloud-burst\image26.png)

20. 선택 합니다 **pre-deployment** Azure Stack 환경에서 조건 아이콘 트리거를 설정 하 고 **릴리스 후 합니다.**

21. 모든 변경 내용을 저장 합니다.

> [!Note]  
> 작업에 대 한 일부 설정을 수 자동으로 정의 된 [환경 변수](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 정의 만들 때. 작업 설정;에서 이러한 설정은 수정할 수 없습니다. 대신 이러한 설정을 편집 하려면 부모 환경 항목을 선택 해야 합니다.

## <a name="publish-to-azure-stack-via-visual-studio"></a>Visual Studio 통해 Azure Stack에 게시

끝점을 만들면 Visual Studio Online (VSTO) 빌드는 Azure Stack에 Azure 서비스 앱을 배포할 수 있습니다. VSTS는 Azure Stack에 연결 하는 빌드 에이전트에 연결 합니다.

1.  VSTO에 로그인 하 고 앱 설정 페이지로 이동 합니다.

2.  **설정**에서 **보안**을 선택합니다.

3.  **VSTS 그룹**를 선택 **의해 끝점 작성자**합니다.

4.  에 **멤버** 탭을 선택 **추가**합니다.

5.  **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.

6.  **변경 내용 저장**을 선택합니다.

7.  에 **VSTS 그룹** 목록에서 **끝점 관리자**합니다.

8.  에 **멤버** 탭을 선택 **추가**합니다.

9.  **사용자 및 그룹 추가**사용자 이름을 입력 하 고 사용자 목록에서 해당 사용자를 선택 합니다.

10. **변경 내용 저장**을 선택합니다.

끝점 정보가 했으므로 Azure Stack 연결 VSTS 사용할 준비가 되었습니다. Azure Stack에서 빌드 에이전트는 VSTS에서 지침을 가져옵니다. 에이전트에서 Azure Stack을 사용 하 여 통신에 대 한 끝점 정보를 전달 하는 다음을

## <a name="develop-the-application-build"></a>응용 프로그램 빌드를 개발 합니다.

> [!Note]  
> 신디케이티드 실행 (Windows Server 및 SQL) 및 App Service 배포에 적절 한 이미지를 사용 하 여 azure Stack가 필요 합니다. App Service 설명서를 검토 합니다. "[App Service on Azure Stack을 사용 하 여 시작 하기 전에](/articles/azure-stack/azure-stack-app-service-before-you-get-started)" Azure Stack 운영자에 대 한 섹션입니다.

사용 하 여 [웹과 같은 Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/) 두 클라우드 모두에 배포 하는 VSTS에서 앱 코드입니다.

### <a name="add-code-to-a-vsts-project"></a>VSTS 프로젝트에 코드 추가

1.  Azure Stack에 대 한 프로젝트 만들기 권한이 있는 계정으로 VSTS에 로그인 합니다. 다음 화면 캡처 HybridCICD 프로젝트에 연결 하는 방법을 보여 줍니다.

2.  **리포지토리를 복제 합니다** 만들어 기본 웹 앱을 열어서 합니다.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>두 클라우드 모두에서 App Services에 대 한 자체 포함 된 웹 앱 배포 만들기

1.  편집 합니다 **WebApplication.csproj** 파일: 선택 **runtimeidentifier-** 다음 win10-x64를 추가 합니다. 자세한 내용은 [자체 포함된 배포](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 설명서.

2.  팀 탐색기를 사용 하 여 VSTS에 코드를 확인 합니다.

3.  Visual Studio Team Services에 응용 프로그램 코드를 체크 인 있는지 확인 합니다.

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1.  빌드 정의 만들 수 있는 계정으로 VSTS에 로그인 합니다.

2.  로 이동 합니다 **웹 응용 프로그램 빌드** 프로젝트에 대 한 페이지입니다.

3.  **인수**에 추가 **-r win10-x64** 코드입니다. .NET Core를 사용 하 여 자체 포함된 배포를 트리거하려면 반드시 확인 해야 합니다.

4.  빌드를 실행 합니다. 합니다 [자체 포함된 배포 빌드](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) 프로세스는 Azure 및 Azure Stack에서 실행 될 수 있는 아티팩트를 게시 합니다.

#### <a name="use-an-azure-hosted-build-agent"></a>Azure를 사용 하 여 호스트 된 빌드 에이전트

VSTS에서 호스트 된 빌드 에이전트를 사용 하는 웹 앱 빌드 및 배포에 대 한 편리한 옵션입니다. 에이전트 유지 관리 및 업그레이드를 중단 없이 연속적으로 연속 개발 주기를 사용 하도록 설정 하는 Microsoft Azure에서 자동으로 수행 됩니다.

### <a name="configure-the-continuous-deployment-cd-process"></a>연속 배포 (CD) 프로세스를 구성 합니다.

Visual Studio Team Services (VSTS) 및 Team Foundation Server (TFS) 제공 항상 구성 및 관리 가능한 파이프라인 개발과 같은 여러 환경에 릴리스를 스테이징, QA (품질 보증), 및 프로덕션 합니다. 이 프로세스는 응용 프로그램 수명 주기의 특정 단계에서 승인 필요를 포함할 수 있습니다.

#### <a name="create-release-definition"></a>릴리스 정의 만들기

릴리스 정의 만드는 경우 응용 프로그램의 마지막 단계는 빌드 프로세스 이 릴리스 정의 릴리스를 만들고 빌드 배포에 사용 됩니다.

1.  VSTS에 로그인 하 고 이동할 **빌드 및 릴리스** 프로젝트용입니다.

2.  에 **릴리스에서** 탭을 선택 **[+]** 선택 하 고 **릴리스 정의 만들기**합니다.

3.  **템플릿을 선택**, 선택 **Azure App Service 배포**를 선택한 후 **적용**합니다.

4.  온 **아티팩트 추가**에서 * * 소스 (빌드 정의) Azure 클라우드 빌드 앱을 선택 합니다.

5.  에 **파이프라인** 탭을 선택 합니다 **1 단계**, **작업이 1 개** 연결할 **환경 작업 보기**.

6.  에 **작업** 탭을 입력으로 Azure를 **환경 이름** AzureCloud Traders 웹 EP에서 선택 하 고는 **Azure 구독** 목록입니다.

7.  입력 된 **Azure app service 이름**는 `northwindtraders` 다음 화면 캡처에 합니다.

8.  에이전트 단계에 대 한 선택 **호스팅된 VS2017** 에서 합니다 **에이전트 큐** 목록입니다.

9.  **Azure App Service 배포**, 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다.

10. **선택 파일 또는 폴더**를 선택 **확인** 하 **위치**합니다.

11. 모든 변경 내용을 저장 하 고로 돌아가서 **파이프라인**합니다.

12. 에 **파이프라인** 탭을 선택 **아티팩트 추가**, 선택는 **NorthwindCloud Traders 선박** 에서 * * 소스 (빌드 정의) * * 목록입니다.

13. 온 **템플릿을 선택**, 다른 환경을 추가 합니다. 선택할 **Azure App Service 배포** 선택한 후 **적용**합니다.

14. 입력 `Azure Stack` 으로 **환경 이름**합니다.

15. 에 **작업** 탭, 찾기 및 Azure Stack을 선택 합니다.

16. **Azure 구독** 목록에서 **AzureStack Traders 선박 EP** Azure Stack 끝점에 대 한 합니다.

17. Azure Stack 웹 앱 이름으로 입력 합니다 **App service 이름**합니다.

18. 아래 **에이전트 선택**, 선택 **azurestack의 경우-b Douglas Fir** 에서 합니다 **에이전트 큐** 목록입니다.

19. 에 대 한 **Azure App Service 배포**, 선택 유효한 **패키지 또는 폴더가** 환경에 대 한 합니다. 온 **파일 또는 폴더 선택**를 선택 **확인** 폴더에 대 한 **위치**합니다.

20. 에 **변수에** 탭에서 명명 된 변수를 찾을 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`합니다. 변수 값을 설정 합니다 **true**, 해당 범위를 설정 하 고 **Azure Stack**합니다.

21. 에 **파이프라인** 탭을 선택 합니다 **연속 배포 트리거** 집합과 NorthwindCloud Traders 웹 아티팩트에 대 한 아이콘을 **연속 배포 트리거** 를 **활성화**합니다. 에 대 한 동일한 작업을 수행 합니다 **NorthwindCloud Traders 선박** 아티팩트입니다.

22. Azure Stack 환경에 대해 선택 된 **배포 전 조건** 아이콘 트리거를 설정 **릴리스 후**합니다.

23. 모든 변경 내용을 저장 합니다.

> [!Note]  
> 릴리스 작업에 대 한 일부 설정으로 자동으로 정의 됩니다 [환경 변수](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) 템플릿에서 릴리스 정의 만들 때. 이러한 설정은 작업 설정을 수정할 수 없습니다 되지만 부모 환경 항목을 수정할 수 있습니다.

## <a name="create-a-release"></a>릴리스를 만듭니다.

1.  에 **파이프라인** 탭을 열고 합니다 **릴리스** 나열 하 고 선택 **릴리스 만들기**합니다.

2.  릴리스에 대 한 설명을 입력, 올바르지 아티팩트 선택 되어 있는지 확인 하 고 선택한 **만들기**합니다. 배너를 몇 분 후 새 릴리스를 만든 및 릴리스 이름의 링크로 표시 되는 나타내는 표시 됩니다. 릴리스 요약 페이지를 보려면이 링크를 선택 합니다.

3.  릴리스 요약 페이지에 대 한 릴리스에 대 한 세부 정보를 보여 줍니다. "릴리스-2"에 대 한 다음 화면 캡처에는 **환경** 섹션 보여줍니다 합니다 **배포 상태** "IN PROGRESS", Azure Stack에 대 한 상태와 Azure가 "SUCCEEDED"에 대 한 합니다. Azure 환경에 대 한 배포 상태를 "SUCCEEDED"로 변경 되 면 릴리스 승인 준비가 되었음을 나타내는 배너가 나타납니다. 때 배포 보류 중이거나 실패 한 파란색 **(i)** 정보 아이콘이 표시 됩니다. 지연 또는 오류에 대 한 이유를 포함 하는 팝업이 표시 아이콘을 마우스로 가리킵니다.

4.  릴리스 목록 등의 다른 보기도 승인을 나타내는 아이콘이 표시 보류 중입니다. 이 아이콘에 대 한 팝업 환경 이름 및 배포와 관련 된 자세한 정보를 보여 줍니다. 관리자로 릴리스와 릴리스에서 승인을 받기 위해 대기 하 고 있는 참조의 전반적인 진행 상황을 확인할에 대 한 것은 쉽습니다.

## <a name="monitor-and-track-deployments"></a>배포를 모니터링 및 추적

1.  에 **Release 2** 요약 페이지에서 선택 **로그**합니다. 이 페이지를 배포 하는 동안 에이전트에서 실시간 로그를 보여 줍니다. 왼쪽된 창에는 각 환경에 대 한 배포의 각 작업의 상태를 보여 줍니다.

2.  사용자 아이콘을 선택 합니다 **동작** 배포를 승인 (또는 거부)가 참조 하는 제공 된 메시지는 배포 전 또는 배포 후 승인에 대 한 열입니다.

3.  배포가 완료 되 면 전체 로그 파일은 오른쪽 창에 표시 됩니다. 선택할 **단계** 와 같은 단계에 대 한 로그 파일을 보려면 왼쪽된 창에서 **초기화 작업**합니다. 개별 로그를 참조 하는 기능 쉽게 추적 및 전체 배포의 부분을 디버깅 합니다. **저장할** 단계에 대 한 로그 파일 또는 **모든 로그를 zip으로 다운로드**합니다.

4.  엽니다는 **요약** 릴리스에 대 한 일반 정보를 보려면 탭 합니다. 이 보기는 빌드, 배포 된 환경, 배포 상태 및 릴리스에 대 한 기타 정보에 대 한 정보를 표시 합니다.

5.  환경 링크를 선택 (**Azure** 하거나 **Azure Stack**) 특정 환경에 배포 보류 중 및 기존에 대 한 정보를 확인 합니다. 이러한 뷰를 사용 하 여 두 환경 모두에 배포 된 동일한 빌드를 확인 하려면 신속 하 게 합니다.

6.  엽니다는 **프로덕션 앱을 배포할** 브라우저에서 합니다. 예를 들어, Azure App Services 웹 사이트에 대 한 URL을 열어 [http://[앱 이름 your\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net)합니다.

**확장 가능한 클라우드 간 솔루션을 제공 하는 Azure 및 Azure Stack의 통합**

데이터 보안을 다시 제공 하는 유연 하 고 강력한 다중 클라우드 서비스 위쪽 및 중복성, 일관적이 고 신속 하 게 가용성, 확장 가능한 저장소 및 분포 및 지역 규격 라우팅입니다. 수동으로 트리거된 하면 안정적이 고 효율적으로 부하를 호스트 하는 웹 앱에 중요 한 데이터의 즉각적인 가용성 보장 간에 전환 합니다. 

## <a name="next-steps"></a>다음 단계
- Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.