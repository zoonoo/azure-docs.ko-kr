---
title: 지속적 배포 구성
description: GitHub, BitBucket, Azure 리포지토리 또는 기타 리포지토리에서 CI/CD를 Azure 앱 서비스에 사용하도록 설정하는 방법을 알아봅니다. 필요에 맞는 빌드 파이프라인을 선택합니다.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437197"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포

[Azure 앱 서비스를](overview.md) 사용하면 최신 업데이트를 끌어들여 GitHub, BitBucket 및 [Azure 리포지토리에서](https://azure.microsoft.com/services/devops/repos/) 지속적인 배포를 수행할 수 있습니다. 이 문서에서는 Azure 포털을 사용하여 Kudu 빌드 서비스 또는 [Azure 파이프라인을](https://azure.microsoft.com/services/devops/pipelines/)통해 앱을 지속적으로 배포하는 방법을 보여 줍니다. 

소스 제어 서비스에 대한 자세한 내용은 [리포지토리 만들기(GitHub)]또는 [리포지토리 만들기(비트버킷)]또는 [새 Git 리포지토리 만들기(Azure Repos)를]참조하십시오.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure 앱 서비스 권한 부여 

Azure 리포지토리를 사용하려면 Azure DevOps 조직이 Azure 구독에 연결되어 있는지 확인합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정 설정을](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)참조하십시오.

Bitbucket 또는 GitHub의 경우 Azure 앱 서비스가 리포지토리에 연결하도록 권한을 부여합니다. 소스 제어 서비스를 한 번만 승인하면 됩니다. 

1. Azure [포털에서](https://portal.azure.com)앱 **서비스를** 검색하고 선택합니다.

   ![앱 서비스를 검색합니다.](media/app-service-continuous-deployment/search-for-app-services.png)

1. 배포할 앱 서비스를 선택합니다.

   ![앱을 선택합니다.](media/app-service-continuous-deployment/select-your-app.png)
   
1. 앱 페이지에서 왼쪽 메뉴에서 **배포 센터를** 선택합니다.
   
1. 배포 **센터** 페이지에서 **GitHub** 또는 **비트버킷을**선택한 다음 **권한 부여를 선택합니다.** 
   
   ![소스 제어 서비스를 선택한 다음 권한 부여를 선택합니다.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 필요한 경우 서비스에 로그인하고 권한 부여 프롬프트를 따릅니다. 

## <a name="enable-continuous-deployment"></a>연속 배포 사용 

소스 제어 서비스를 승인한 후 기본 제공 [Kudu App Service](#option-1-kudu-app-service) 빌드 서버 또는 Azure 파이프라인을 통해 지속적인 배포를 위해 앱을 [구성합니다.](#option-2-azure-pipelines) 

### <a name="option-1-kudu-app-service"></a>옵션 1: 쿠두 앱 서비스

기본 제공 Kudu 앱 서비스 빌드 서버를 사용하여 GitHub, 비트버킷 또는 Azure 리포지토리에서 지속적으로 배포할 수 있습니다. 

1. Azure [포털에서](https://portal.azure.com)앱 **서비스를**검색한 다음 배포할 앱 서비스를 선택합니다. 
   
1. 앱 페이지에서 왼쪽 메뉴에서 **배포 센터를** 선택합니다.
   
1. **배포 센터** 페이지에서 권한 있는 소스 제어 공급자를 선택하고 **계속을**선택합니다. GitHub 또는 Bitbucket의 경우 **계정 변경을** 선택하여 승인된 계정을 변경할 수도 있습니다. 
   
   > [!NOTE]
   > Azure 리포지토리를 사용하려면 Azure DevOps 서비스 조직이 Azure 구독에 연결되어 있는지 확인합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정 설정을](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)참조하십시오.
   
1. GitHub 또는 Azure 리포지토리의 경우 **빌드 공급자** 페이지에서 **앱 서비스 빌드 서비스를**선택한 다음 **계속을**선택합니다. 비트버킷은 항상 앱 서비스 빌드 서비스를 사용합니다.
   
   ![앱 서비스 빌드 서비스를 선택한 다음 계속을 선택합니다.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 구성 페이지에서 다음을 **수행합니다.**
   
   - GitHub의 경우 드롭다운하고 **Organization**조직, **리포지토리**및 **분기를** 선택하여 지속적으로 배포할 수 있습니다.
     
     > [!NOTE]
     > 리포지토리가 표시되지 않으면 GitHub에서 Azure 앱 서비스에 권한을 부여해야 할 수 있습니다. GitHub 리포지토리를 찾아보고 **설정** > **응용 프로그램** > **공인 OAuth 앱으로**이동합니다. **Azure 앱 서비스를**선택한 다음 **[권한 부여]** 조직 리포지토리의 경우 권한을 부여하려면 조직의 소유자여야 합니다.
     
   - 비트버킷의 경우 지속적으로 배포할 비트버킷 **팀,** **리포지토리**및 **분기를** 선택합니다.
     
   - Azure 리포지토리의 경우 지속적으로 배포할 **Azure DevOps 조직,** **프로젝트,** **리포지토리**및 **분기를** 선택합니다.
     
     > [!NOTE]
     > Azure DevOps 조직이 목록에 없는 경우 Azure 구독에 연결되어 있는지 확인합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정 설정을](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)참조하십시오.
     
1. **계속**을 선택합니다.
   
   ![리포지토리 정보를 입력한 다음 계속을 선택합니다.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 빌드 공급자를 구성한 후 **요약** 페이지의 설정을 검토한 다음 **완료를**선택합니다.
   
1. 선택한 리포지토리 및 분기의 새 커밋이 이제 앱 서비스 앱에 지속적으로 배포됩니다. **배포 센터** 페이지에서 커밋 및 배포를 추적할 수 있습니다.
   
   ![배포 센터에서 커밋 및 배포 추적](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>옵션 2: Azure 파이프라인 

계정에 필요한 권한이 있는 경우 GitHub 또는 Azure Repos에서 지속적으로 배포하도록 Azure 파이프라인을 설정할 수 있습니다. Azure 파이프라인을 통한 배포에 대한 자세한 내용은 [Azure 앱 서비스에 웹 앱 배포를](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)참조하세요.

#### <a name="prerequisites"></a>사전 요구 사항

Azure App Service에서 Azure 파이프라인을 사용하여 연속 배달을 만들려면 Azure DevOps 조직에 다음 권한이 있어야 합니다. 

- Azure 계정에는 Azure Active Directory에 쓰고 서비스를 만들 수 있는 권한이 있어야 합니다. 
  
- Azure 계정에 Azure 구독의 **소유자** 역할이 있어야 합니다.

- 사용하려는 Azure DevOps 프로젝트의 관리자여야 합니다.

#### <a name="github--azure-pipelines"></a>GitHub + Azure 파이프라인

1. Azure [포털에서](https://portal.azure.com)앱 **서비스를**검색한 다음 배포할 앱 서비스를 선택합니다. 
   
1. 앱 페이지에서 왼쪽 메뉴에서 **배포 센터를** 선택합니다.

1. **배포 센터** 페이지에서 **GitHub를** 소스 제어 공급자로 선택하고 **계속을**선택합니다. **GitHub의**경우 **계정 변경을** 선택하여 승인된 계정을 변경할 수 있습니다.

    ![원본 제어(source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. 공급자 **빌드** 페이지에서 **Azure 파이프라인(미리 보기)을**선택한 다음 **계속을**선택합니다.

    ![빌드 공급자](media/app-service-continuous-deployment/select-build-provider.png)
   
1. **구성** 페이지에서 **코드** 섹션에서 계속 배포할 **조직,** **리포지토리**및 **분기를** 선택하고 **계속을**선택합니다.
     
     > [!NOTE]
     > 리포지토리가 표시되지 않으면 GitHub에서 Azure 앱 서비스에 권한을 부여해야 할 수 있습니다. GitHub 리포지토리를 찾아보고 **설정** > **응용 프로그램** > **공인 OAuth 앱으로**이동합니다. **Azure 앱 서비스를**선택한 다음 **[권한 부여]** 조직 리포지토리의 경우 권한을 부여하려면 조직의 소유자여야 합니다.
       
    **빌드** 섹션에서 Azure DevOps 조직, 프로젝트, Azure 파이프라인빌드 작업을 실행 하는 데 사용 해야 하는 언어 프레임 워크를 지정 하 고 **계속**을 선택 합니다.

   ![빌드 공급자](media/app-service-continuous-deployment/build-configure.png)

1. 빌드 공급자를 구성한 후 **요약** 페이지의 설정을 검토한 다음 **완료를**선택합니다.

   ![빌드 공급자](media/app-service-continuous-deployment/summary.png)
   
1. 선택한 리포지토리 및 분기의 새 커밋이 이제 앱 서비스에 지속적으로 배포됩니다. **배포 센터** 페이지에서 커밋 및 배포를 추적할 수 있습니다.
   
   ![배포 센터에서 커밋 및 배포 추적](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure 리포지토리 + Azure 파이프라인

1. Azure [포털에서](https://portal.azure.com)앱 **서비스를**검색한 다음 배포할 앱 서비스를 선택합니다. 
   
1. 앱 페이지에서 왼쪽 메뉴에서 **배포 센터를** 선택합니다.

1. **배포 센터** 페이지에서 **Azure 리포지토리를** 소스 제어 공급자로 선택하고 **계속을**선택합니다.

    ![원본 제어(source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. 공급자 **빌드** 페이지에서 **Azure 파이프라인(미리 보기)을**선택한 다음 **계속을**선택합니다.

    ![원본 제어(source control)](media/app-service-continuous-deployment/azure-pipelines.png)

1. **구성** 페이지에서 **코드** 섹션에서 계속 배포할 **조직,** **리포지토리**및 **분기를** 선택하고 **계속을**선택합니다.

   > [!NOTE]
   > 기존 Azure DevOps 조직이 나열되지 않은 경우 Azure 구독에 연결해야 할 수 있습니다. 자세한 내용은 [CD 릴리스 파이프라인 정의를](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)참조하십시오.

   **빌드** 섹션에서 Azure DevOps 조직, 프로젝트, Azure 파이프라인빌드 작업을 실행 하는 데 사용 해야 하는 언어 프레임 워크를 지정 하 고 **계속**을 선택 합니다.

   ![빌드 공급자](media/app-service-continuous-deployment/build-configure.png)

1. 빌드 공급자를 구성한 후 **요약** 페이지의 설정을 검토한 다음 **완료를**선택합니다.  
     
   ![빌드 공급자](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. 선택한 리포지토리 및 분기의 새 커밋이 이제 앱 서비스에 지속적으로 배포됩니다. **배포 센터** 페이지에서 커밋 및 배포를 추적할 수 있습니다.

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

연속 배포를 사용하지 않도록 설정하려면 앱의 **배포 센터** 페이지 상단에서 **연결을** 선택합니다.

![지속적 배포 사용 안 함](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>지원되지 않는 리포지토리 사용

Windows 앱의 경우 포털이 [GitLab](https://gitlab.com/)과 같이 직접 지원하지 않는 클라우드 Git 또는 Mercurial 리포지토리에서 연속 배포를 수동으로 구성할 수 있습니다. **배포 센터** 페이지에서 외부 상자를 선택하여 이 작업을 수행합니다. 자세한 내용은 [수동 단계를 사용하여 연속 배포 설정을](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [지속적인 배포를 통해 일반적인 문제 조사](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell 사용](/powershell/azureps-cmdlets-docs)
* [Git 설명서](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[리포지토리 만들기(GitHub)]: https://help.github.com/articles/create-a-repo
[리포지토리 만들기(BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[새 Git 리포지토리 만들기(Azure 리포지토리)]: /azure/devops/repos/git/creatingrepo
