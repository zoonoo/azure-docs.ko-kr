---
title: 지속적인 배포 - Azure App Service | Microsoft Docs
description: Azure App Service에 연속 배포를 활성화하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836764"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포

[Azure App Service](overview.md) BitBucket, GitHub에서 연속 배포를 사용 하도록 설정 하 고 [Azure 리포지토리](https://azure.microsoft.com/services/devops/repos/) 최신 업데이트에서 풀링 하 여 리포지토리. 이 문서에서는 지속적으로 Kudu 빌드 서비스를 통해 앱을 배포 하려면 Azure portal을 사용 하는 방법 또는 [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/)합니다. 

원본 제어 서비스에 대 한 자세한 내용은 참조 하세요. [리포지토리 만들기(GitHub)], [리포지토리 만들기(BitBucket)], 또는 [새 Git 리포지토리 (Azure 리포지토리) 만들기]합니다.

포털을 지원 하지 않는 직접,와 같은 클라우드 저장소에서 연속 배포를 수동으로 구성 하려면 [GitLab](https://gitlab.com/)를 참조 하세요 [수동 단계를 사용 하 여 연속 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)합니다.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure App Service 인증 

Azure 리포지토리를 사용 하려면 Azure DevOps 조직에 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정을 설정](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)합니다.

GitHub, Bitbucket에 대 한 Azure App Service 리포지토리에 연결할 권한을 부여 합니다. 소스 제어 서비스를 사용 하 여 한 번 인증 해야 합니다. 

1. 선택 **App Services** 에 [Azure portal](https://portal.azure.com) 왼쪽 탐색을 하 고 배포 하려면 웹 앱을 선택 합니다. 
   
1. 앱 페이지에서 선택 **배포 센터** 왼쪽된 메뉴에서.
   
1. 에 **배포 센터** 페이지에서 **GitHub** 또는 **Bitbucket**를 선택한 후 **Authorize**. 
   
   ![소스 제어 서비스를 선택한 다음 권한 부여를 선택 합니다.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 필요한 경우 서비스에 로그인 하는 권한 부여 지침을 따릅니다. 

## <a name="enable-continuous-deployment"></a>연속 배포 사용 

소스 제어 서비스를 인증 한 후 기본 제공을 통해 연속 배포를 위한 앱 구성 [App Service Kudu 빌드 서버](#option-1-use-the-app-service-build-service), 또는 [Azure 파이프라인](#option-2-use-azure-pipelines)합니다. 

### <a name="option-1-use-the-app-service-build-service"></a>옵션 1: App Service는 빌드 서비스를 사용 합니다.

기본 제공 App Service Kudu 빌드 GitHub, Bitbucket 또는 Azure 리포지토리를 지속적으로 배포 하는 서버를 사용할 수 있습니다. 

1. 선택 **App Services** 에 [Azure portal](https://portal.azure.com) 왼쪽 탐색을 하 고 배포 하려면 웹 앱을 선택 합니다. 
   
1. 앱 페이지에서 선택 **배포 센터** 왼쪽된 메뉴에서.
   
1. 권한 있는 원본 제어 공급자를 선택 합니다 **배포 센터** 페이지에서 선택한 **계속**합니다. GitHub 또는 Bitbucket을 선택할 수도 있습니다 **계정을 변경** 권한 있는 계정을 변경 합니다. 
   
   > [!NOTE]
   > Azure 리포지토리를 사용 하려면 Azure DevOps 서비스 조직에 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정을 설정](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)합니다.
   
1. Azure 리포지토리를 GitHub에에 **빌드 공급자** 페이지에서 **App Service 서비스 빌드**를 선택한 후 **계속**합니다. Bitbucket에는 항상 앱 빌드 서비스를 사용합니다.
   
   ![App Service 빌드 서비스를 선택한 다음 계속을 선택 합니다.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 에 **구성** 페이지:
   
   - GitHub에 대 한 드롭다운을 선택 합니다 **조직**, **리포지토리**, 및 **분기** 지속적으로 배포 하려면.
     
     > [!NOTE]
     > 모든 리포지토리 표시 되지 않는 경우 GitHub의 Azure App Service에 권한을 부여 해야 합니다. GitHub 리포지토리를 찾아 이동할 **설정을** > **응용 프로그램** > **OAuth 앱 권한이**합니다. 선택 **Azure App Service**를 선택한 후 **Grant**합니다.
     
   - Bitbucket, 선택는 Bitbucket **Team**를 **리포지토리**, 및 **분기** 지속적으로 배포 하려면.
     
   - Azure 리포지토리를 선택 합니다 **Azure DevOps 조직**, **프로젝트**를 **리포지토리**, 및 **분기** 지속적으로 배포 하려면.
     
     > [!NOTE]
     > Azure DevOps 조직 목록에 없으면 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps 서비스 계정을 설정](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. 선택 **계속**합니다.
   
   ![리포지토리 정보를 채웁니다 다음 계속을 선택 합니다.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 빌드 공급자를 구성한 후에서 설정을 검토 합니다 **요약** 페이지를 선택한 후 **마침**합니다.
   
   선택한 리포지토리 및 분기를 새 커밋이 이제 App Service 앱에 지속적으로 배포합니다. 커밋 및 배포를 추적할 수 있습니다 합니다 **배포 센터** 페이지입니다.
   
   ![커밋 및 배포 센터에서 배포를 추적 합니다.](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>옵션 2: Azure Pipelines 사용 

계정에 필요한 권한이 있는 경우 Azure 리포지토리 또는 GitHub 리포지토리에서 지속적으로 배포 하려면 Azure 파이프라인을 설정할 수 있습니다. Azure 파이프라인을 통해 배포 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure App Services에 웹 앱 배포](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)합니다.

Azure DevOps 조직에서 지속적인 Azure 파이프라인을 만들려면 Azure App service: 

- Azure 계정 서비스를 만들고 Azure Active Directory에 쓰기 권한이 있어야 합니다. 
  
- Azure 계정에 있어야 합니다 **소유자** Azure 구독에 역할입니다.

- 사용 하려는 Azure DevOps 프로젝트 관리자 여야 합니다.

구성 하려면 Azure 파이프라인 (미리 보기):

1. 선택 **App Services** 에 [Azure portal](https://portal.azure.com) 왼쪽 탐색을 하 고 배포 하려면 웹 앱을 선택 합니다. 
   
1. 앱 페이지에서 선택 **배포 센터** 왼쪽된 메뉴에서.
   
1. 에 **빌드 공급자** 페이지에서 **Azure 파이프라인 (미리 보기)** 를 선택한 후 **계속**합니다. 
   
1. 에 **구성** 페이지의 **코드** 섹션:
   
   - GitHub에 대 한 드롭다운을 선택 합니다 **조직**, **리포지토리**, 및 **분기** 지속적으로 배포 하려면.
     
     > [!NOTE]
     > 모든 리포지토리 표시 되지 않는 경우 GitHub의 Azure App Service에 권한을 부여 해야 합니다. GitHub 리포지토리를 찾아 이동할 **설정을** > **응용 프로그램** > **OAuth 앱 권한이**합니다. 선택 **Azure App Service**를 선택한 후 **Grant**합니다.
     
   - Azure 리포지토리를 선택 합니다 **Azure DevOps 조직**를 **프로젝트**를 **리포지토리**, 및 **분기** 지속적으로 배포 하려면 또는 새 Azure DevOps 조직을 구성 합니다.
     
     > [!NOTE]
     > 기존 Azure DevOps 조직 목록에 없으면 Azure 구독에 연결 해야 합니다. 자세한 내용은 [CD 릴리스 파이프라인 정의](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)합니다.
     
1. 선택 **계속**합니다.
   
1. Azure 리포지토리에 대 한에 **빌드** 섹션에서 Azure 파이프라인을 사용 하 여 빌드 작업을 실행 하려면 다음을 선택 하는 언어 프레임 워크를 지정 **계속**합니다.
   
1. 에 **테스트** 페이지에서 다음을 선택 하 고 부하 테스트를 사용할지 여부 **계속**합니다.
   
1. App Service 계획에 따라 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 표시 될 수 있습니다를 **스테이징에 배포** 페이지입니다. 선택 것인지 [배포 슬롯을 사용 하도록 설정](deploy-staging-slots.md)를 선택한 후 **계속**합니다.
   
   > [!NOTE]
   > Azure 파이프라인 프로덕션 슬롯에 대 한 지속적인 업데이트를 허용 하지 않습니다. 이 제한으로 인해 프로덕션에 실수로 인 한 배포 합니다. 스테이징 슬롯에 대 한 지속적인 업데이트를 설정 하 고, 변경 내용을 확인 하 고 및 다음 준비가 되 면 슬롯을 교환 합니다.
   
1. 빌드 공급자를 구성한 후에서 설정을 검토 합니다 **요약** 페이지를 선택한 후 **마침**합니다.
   
   선택한 리포지토리 및 분기를 새 커밋이 이제 App Service 앱에 지속적으로 배포합니다. 커밋 및 배포를 추적할 수 있습니다 합니다 **배포 센터** 페이지입니다.
   
   ![커밋 및 배포 센터에서 배포를 추적 합니다.](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

연속 배포를 사용 하지 않으려면 선택 **연결 끊기** 앱의 맨 위에 있는 **Deployment Center** 페이지입니다.

![지속적 배포 사용 안 함](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>추가 자료

* [연속 배포를 사용 하 여 일반적인 문제를 조사 합니다.](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell 사용](/powershell/azureps-cmdlets-docs)
* [Git 설명서](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[리포지토리 만들기(GitHub)]: https://help.github.com/articles/create-a-repo
[리포지토리 만들기(BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[새 Git 리포지토리 (Azure 리포지토리) 만들기]: /azure/devops/repos/git/creatingrepo
