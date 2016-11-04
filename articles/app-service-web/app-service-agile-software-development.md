---
title: Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발
description: agile 소프트웨어 개발을 지원하는 Azure 앱 서비스를 사용하여 고확장성 복합 응용 프로그램을 만드는 법을 배웁니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin

---
# Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발
이 자습서에서는, [agile 소프트웨어 개발](https://en.wikipedia.org/wiki/Agile_software_development)을 지원하는 [Azure 앱 서비스](/services/app-service/)를 사용하여 고확장성 복합 응용 프로그램을 만드는 법을 배웁니다. 여기서는 사용자가 [Azure에서 복잡한 응용 프로그램 배포](app-service-deploy-complex-application-predictably.md)방법을 이미 알고 있다고 가정합니다.

Agile 방법론의 성공적인 구현을 기술적인 과정의 제약이 나타날 수 있습니다. Azure 앱 서비스를 [지속적인 게시](app-service-continuous-deployment.md), [스테이징 환경](web-sites-staged-publishing.md)(슬롯) 및 [모니터링](web-sites-monitor.md)과 같은 기능을 조합과 [Azure 리소스 관리자](../resource-group-overview.md)의 배포 관리와 잘 결합하면 Agile 소프트웨어 개발자에게 훌륭한 솔루션의 일부가 될 수 있습니다.

다음 표는 Agile 개발과 연관된 요구 사항의 최종 목록과, Azure 서비스가 이것들을 어떻게 사용 가능하게 만드는지 나타냅니다.

| 요구 사항 | Azure 사용할 수 있는 방법 |
| --- | --- |
| -모든 커밋을 사용하여 구축<br>- 빠르게 자동적으로 구축합니다. |지속적인 배포를 구성할 때, Azure 앱 서비스는 개발 분기점에 따라 실시간 구축 기능을 수행합니다. 모든 시간 코드는 분기점에 푸시되고, 자동적으로 Azure에서 구축하고 실시간으로 실행됩니다. |
| - 자체-테스트 빌드 구축하기 |Azure 리소스 관리자 템플릿으로 배포 된 테스트, 웹 테스트 등을 불러옵니다. |
| - 프로덕션 환경의 클론에 테스트를 수행합니다. |Azure 리소스 관리자 템플릿은 신속하고 예측 가능한 테스트를 위해 Azure 프로덕션 환경 (앱 설정, 연결 문자열 템플릿, 크기 조정 등 포함)의 클론을 만들 데 사용할 수 있습니다. |
| -최근 구축 결과를 쉽게 볼 수 있습니다. |리포지토리에서 Azure로의 지속적인 배포는 변경 사항 을 커밋한 즉시 실시간 응용 프로그램에서 테스트 할 수 있다는 것을 의미합니다. |
| -매일 주 분기점에 커밋하기<br>-배포를 자동화하기 |프로덕션 응용 프로그램과 저장소의 주 분기점의 지속적인 통합은 모든 커밋/병합은 프로덕션의 주 분기점으로 자동적으로 배포 됩니다. |

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 수행할 사항
사용자는 Web API backend(BE), Frontend(FE)이 두 개의 [웹앱](/services/app-service/web/)과 [SQL 데이터베이스](/services/sql-database/)로 구성된 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 샘플 응용 프로그램의 새로운 변경 사항을 게시하기 위해 전형적인 개발 테스트 단계 프로덕션 워크플로를 연습합니다. 아래의 배포 아키텍처를 사용 합니다.

![](./media/app-service-agile-software-development/what-1-architecture.png)

단어에 그림을 넣으려면:

* 배포 아키텍처는 3가지 환경(또는 Azure의 [리소스 그룹](../resource-group-overview.md))으로 구분됩니다. 각각은 개별적인 [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), [규모](web-sites-scale.md) 설정, SQL 데이터베이스가 있습니다.
* 각 환경을 별도로 관리할 수 있습니다. 서로 다른 구독에도 존재할 수 있습니다.
* 스테이징과 프로덕션은 같은 앱 서비스 앱의 두 슬롯으로 구현됩니다. 마스터 분기점은 스테이징 슬롯의 연속 통합을 위한 장치 조정입니다.
* 마스터 분기점으로의 커밋이 (프로덕션 데이터를 사용하여) 스테이징 슬롯에서 확인될 때 확인된 스테이징 앱은 [가동 중지 시간 없이](web-sites-staged-publishing.md) 프로덕션 슬롯으로 교체됩니다.

프로덕션 및 스테이징 환경은 [*&lt;repository\_root>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json)의 템플릿에서 정의됩니다.

개발 및 테스트 환경은 [*&lt;repository\_root>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json)의 템플릿으로 정의됩니다.

또한 사용자는 개발 분기점에서 테스트 분기점까지, 그리고 마스터 분기점까지 코드 이동하는 일반적인 분기 전략을 사용합니다(예: 질의 향상).

![](./media/app-service-agile-software-development/what-2-branches.png)

## 필요한 사항
* Azure 계정.
* [GitHub](https://github.com/) 계정
* Git 셸(설치된 [Windows용 GitHub](https://windows.github.com/)) - 이것은 동일한 세션에서 Git와 PowerShell 명령을 실행할 수 있도록 합니다.
* 최신 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) 비트
* 다음의 기본적인 이해:
  * [Azure 리소스 관리자](../resource-group-overview.md) 템플릿 배포([Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-deploy-complex-application-predictably.md) 참조)
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 있어야 합니다.
> 
> * [Azure 계정을 무료로 개설](/pricing/free-trial/)할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 웹앱과 같은 무료 Azure 서비스를 사용할 수 있습니다.
> * [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있음 - Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
> 
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## 프로덕션 환경을 설정합니다.
> [!NOTE]
> 이 자습서에서 사용되는 스크립트는 사용자의 GitHub 저장소에서 자동적으로 구성되어 지속적으로 게시됩니다. 이것은 Azure에 이미 저장되어있는 GitHub 자격 증명이 요구되며, 그렇지 않으면 웹앱에 대한 소스 제어 설정 구성을 시도할 때 스크립트 배포는 실패합니다.
> 
> Azure에서 GitHub 자격 증명을 저장하려면 [Azure 포털](https://portal.azure.com/)에서 웹앱을 생성하고 [GitHub 배포를 구성](app-service-continuous-deployment.md)하세요. 이 작업은 한 번만 필요합니다.
> 
> 

일반적인 DevOps 시나리오에서, 사용자는 Azure에서 실시간으로 실행되는 응용 프로그램을 갖게 되며, 지속적인 게시를 통해 원하는 부분을 변경할 수 있습니다. 이 시나리오에서는 개발, 테스트 및 프로덕션 환경에 배포하는 데 사용하는 템플릿을 사용할 수 있습니다. 이 섹션에서는 설정 합니다.

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp)저장소에서 사용자 고유 포크를 생성합니다. 분기를 만드는 방법에 대한 자세한 내용은 [리포지토리 분기](https://help.github.com/articles/fork-a-repo/)를 참조하세요. 사용자의 포크를 생성하면, 브라우저에서 볼 수 있습니다.
   
   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Git 셸 세션을 엽니다. Git 셸이 없는 경우 [Windows용 GitHub](https://windows.github.com/)를 설치합니다.
3. 다음 명령을 실행하여 사용자 포크의 로컬 클론을 만듭니다.
   
     git clone https://github.com/<your_fork>/ToDoApp.git 
4. 로컬 클론을 만든 후 *&lt;repository\_root>*\\ARMTemplates로 이동하여 다음과 같이 deploy.ps1 스크립트를 실행합니다.
   
     .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git
5. 메시지가 표시되면 데이터베이스 액세스에 대한 원하는 사용자이름과 암호를 입력합니다.
   
   다양한 Azure 리소스의 프로비전 진행 상태가 표시되어야 합니다. 배포가 완료되면, 스크립트가 브라우저에서 응용 프로그램을 시작되고, 친숙한 소리가 납니다.
   
   ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
   
   > [!TIP]
   > 고유 ID를 사용한 리소스 제공 방법을 알아보려면 *&lt;repository\_root>*\\ARMTemplates\\Deploy.ps1을 살펴보세요. 충돌하는 리소스 이름에 대해 걱정하지 않고 동일한 배포의 클론을 만들려면 동일한 방식을 사용할 수 있습니다.
   > 
   > 
6. Git 셸 세션으로 돌아가서 다음을 실행합니다.
   
     .\swap –Name ToDoApp<unique_string>master
   
   ![](./media/app-service-agile-software-development/production-4-swap.png)
7. 스크립트가 완료되면 프런트 엔드의 주소(http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/)로 돌아가 프로덕션에서 응용 프로그램이 실행되는 것을 확인합니다.
8. [Azure 포털](https://portal.azure.com/)에 로그인하고 생성된 것을 살펴보세요.
   
   같은 리소스 그룹에서 두 개의 웹앱이 나타나야 하며, 하나는 이름에 `Api` 접미사가 있습니다. 리소스 그룹 보기를 보면 SQL 데이터베이스 및 서버, 앱 서비스 계획 및 웹앱에 대한 스테이징 슬롯도 표시 됩니다. *&lt;repository\_root>*\\ARMTemplates\\ProdAndStage.json을 사용하여 다른 리소스를 통해 검색 및 비교하여 템플릿에서 어떻게 구성되는지 볼 수 있습니다.
   
   ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

이제 프로덕션 환경을 설정 했습니다. 다음으로, 응용 프로그램에 대한 새 업데이트를 시작 합니다.

## Dev를 생성하고 분기점을 테스트 합니다.
이제 Azure의 프로덕션 내에서 실행되는 복합 응용 프로그램을 갖게 되었으므로, agile방법론에 따라 사용자의 응용 프로그램의 업데이트를 생성합니다. 이 섹션에서는 업데이트 요구 사항을 만드는 데 필요한 분기점을 테스트하고 dev를 생성합니다.

1. 먼저 테스트 환경을 만듭니다. Git 셸 세션에서 다음 명령을 실행하여 **NewUpdate**라는 새로운 분기에 대한 환경을 만듭니다.
   
     git checkout -b NewUpdate
     git push origin NewUpdate 
     .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate
2. 메시지가 표시되면 데이터베이스 액세스에 대한 원하는 사용자이름과 암호를 입력합니다.
   
   배포가 완료되면, 스크립트가 브라우저에서 응용 프로그램을 시작되고, 친숙한 소리가 납니다. 이렇게 해서, 사용자는 자체 테스트 환경이 포함된 새로운 분기점을 갖게되었습니다. 잠시 테스트환경에 관한 몇 가지 사항을 검토 합니다.
   
   * 모든 Azure 구독에서 만들 수 있습니다. 즉, 테스트 환경에서 프로덕션 환경을 별도로 관리할 수 있습니다.
   * 테스트 환경을 라이브 Azure에서 실행 중입니다.
   * 테스트 환경은 스테이징 슬롯 및 크기 조정 설정을 제외하고 프로덕션 환경과 동일합니다. ProdandStage.json과 Dev.json 간의 유일한 차이점으로 이것을 알 수 있습니다.
   * 다른 가격 계층(예: **무료**)으로 자체 앱 서비스 계획에서 테스트 환경을 관리할 수 있습니다.
   * 테스트 환경을 삭제하는 것은 리소스 그룹을 삭제하는 것 만큼 간단합니다. 이 작업을 수행하는 방법은 [나중에](#delete) 살펴보겠습니다.
3. 다음 명령을 실행하여 개발 분기점 생성으로 넘어갑니다.
   
     git checkout -b Dev
     git push origin Dev
     .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev
4. 메시지가 표시되면 데이터베이스 액세스에 대한 원하는 사용자이름과 암호를 입력합니다.
   
   잠시 개발 환경에 관한 몇가지 사항을 검토 합니다.
   
   * 개발 환경은 테스트 환경과 같은 템플릿을 사용하여 배포되었으므로 구성이 동일합니다.
   * 각 개발 환경은 개발자의 Azure 구독에서 생성 가능하며, 별도로 관리하는 테스트 환경을 남겨둘 수 있습니다.
   * 개발 환경이 Azure에서 실시간 작동 중입니다.
   * 개발 환경을 삭제하는 것은 리소스 그룹을 삭제하는 것만큼 간단합니다. 이 작업을 수행하는 방법은 [나중에](#delete) 살펴보겠습니다.

> [!NOTE]
> 여러 개발자가 새로운 업데이트를 개발할 때, 각각의 개발자들은 다음을 수행하면 쉽게 분기 및 전용 개발 환경을 생성할 수 있습니다.
> 
> 1. GitHub에서 리포지토리의 고유한 분기를 만듭니다([리포지토리 분기](https://help.github.com/articles/fork-a-repo/) 참조).
> 2. 자신의 로컬 컴퓨터에서 포크를 복제 합니다.
> 3. 자신의 개발 포크 및 환경을 만들려면 동일한 명령을 실행 합니다.
> 
> 

완료되면, GitHub 포크는 세 개의 분기가 있어야 합니다.

![](./media/app-service-agile-software-development/test-1-github-view.png)

3 개의 별도 리소스 그룹에서 6개의 웹앱(한 그룹에 2개의 응용 프로그램)이 있어야 합니다.

![](./media/app-service-agile-software-development/test-2-all-webapps.png)

> [!NOTE]
> ProdandStage.json은 프로덕션 응용 프로그램의 확장성에 적합한 **표준** 가격 책정 계층을 사용하기 위한 프로덕션 환경을 지정합니다.
> 
> 

## 모든 커밋을 구축하고 테스트합니다.
ProdAndStage.json 및 Dev.json 템플릿 파일은 웹앱에 대한 연속 게시 설정 기본값인 소스 제어 매개 변수를 이미 지정합니다. 따라서, GitHub분기의 모든 커밋은 해당 분기에서 Azure로 자동 배포를 트리거합니다. 이제 설치 작업을 하는 방법에 대해 알아보겠습니다.

1. 사용자가 로컬 저장소의 개발 분기에 있는지 확인합니다. 이렇게 하려면 Git 셸에서 다음 명령을 실행합니다.
   
     git checkout Dev
2. [부트스트랩](http://getbootstrap.com/components/) 목록을 사용하도록 코드를 변경하여 앱의 UI 계층을 간단히 변경합니다. *&lt;repository\_root>*\\src\\MultiChannelToDo.Web\\index.cshtml을 열고 아래 강조 표시된 대로 변경합니다.
   
   ![](./media/app-service-agile-software-development/commit-1-changes.png)
   
   > [!NOTE]
   > 위의 이미지가 나타나지 않으면:
   > 
   > * 18번 줄에서 `check-list`를 `list-group`으로 변경합니다.
   > * 19 번 줄에서 `class="check-list-item"`을 `class="list-group-item"`로 변경합니다.
   > 
   > 
3. 변경 내용을 저장합니다. Git 셸로 돌아가서 다음 명령을 실행합니다.
   
     cd <repository_root>
     git add .
     git commit -m "changed to bootstrap style"
     git push origin Dev
   
   이러한 git 명령은 TFS와 같은 다른 소스 제어 시스템의 ‘사용자 코드에서 확인”과 비슷합니다. `git push`를 실행하는 경우 새로운 커밋은 Azure로 자동 코드 푸시를 트리거한 다음 개발 환경의 변경 사항을 반영하기 위해 해당 응용 프로그램을 다시 빌드합니다.
4. 개발 환경에서 코드 푸시가 나타나는지 확인하려면 개발 환경의 웹앱 블레이드에서 **배포** 부분을 확인하세요. 그곳에서 최신 커밋 메시지를 볼 수 있습니다.
   
   ![](./media/app-service-agile-software-development/commit-2-deployed.png)
5. 여기서 **찾아보기**를 클릭하여 Azure의 라이브 응용 프로그램의 새로운 변경 사항을 확인하세요.
   
   ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)
   
   이것은 응용 프로그램의 사소한 변경입니다. 그러나, 복잡한 웹 응용 프로그램의 많은 새로운 변경 사항들은 의도하지 않고 원하지 않는 부작용을 일으킵니다. 라이브 빌드에서 모든 커밋을 쉽게 테스트 하여 이러한 문제들을 고객이 먼저 발견하기 전에 수정할 수 있습니다.

이제 **NewUpdate** 프로젝트의 개발자로서 개발 환경을 쉽게 만든 후 모든 커밋을 빌드하고 모든 빌드를 테스트할 수 있다는 것을 확인했습니다.

## 테스트 환경에 코드를 병합
개발 환경 분기에서 새 업데이트 분기까지 코드 푸시가 준비된 경우는 표준 git 프로세스입니다.

1. 새 업데이트의 모든 커밋을 GitHub의 개발 분기에 병합합니다. (예: 다른 개발자가 만든 커밋) GitHub의 모든 커밋을 개발 환경 안에서 코드 푸시하고 구축하는 데 트리거합니다. 그리고 나서, 사용자는 개발분기의 코드가 새 업데이트 분기의 최신 비트와 여전히 작동 되는지 확인합니다.
2. 개발 분기의 모든 새 커밋을 GitHub의 새 업데이트 브랜치로 병합합니다. 이 작업은 테스트 환경의 코드 푸시와 빌드를 트리거합니다.

지속적인 배포를 이미 git 분기와 같이 설치했기 때문에 사용자는 통합 빌드 실행과 같은 다른 어떤 작업도 할 필요가 없다는 것을 다시 한번 명심하세요. 사용자는 단순히 git를 사용하여 표준 소스 제어 사례를 수행하고, Azure는 사용자를 위해 모든 구축 프로세스를 수행합니다.

이제 **NewUpdate** 분기에 사용자의 코드를 푸시해 보겠습니다. Git 셸에서 다음 명령을 실행합니다.

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

끝났습니다.

이제 테스트 환경에 푸시된 (NewUpdate 분기에 병합) 새 커밋을 보려면 테스트 환경에 대한 웹앱 블레이드로 이동합니다. **찾아보기**를 클릭하여 스타일 변경을 볼 수 있으면 Azure에서 실시간으로 실행 중입니다.

## 프로덕션에 업데이트 배포
코드를 스테이징 및 프로덕션 환경에 푸시하는 것은 이미 작업했던 테스트환경에 코드를 푸시하는 것과 다르지 않습니다. 매우 간단합니다.

Git 셸에서 다음 명령을 실행합니다.

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

ProdandStage.json에서 스테이징 및 프로덕션 환경이 설정된 방식에 따라 새 코드가 **스테이징** 슬롯으로 푸시되고 실행됩니다. 스테이징 슬롯의 URL로 이동하면 여기서 실행 되는 새 코드를 볼 수 있습니다. 이렇게 하려면 Git 셸에서 `Show-AzureWebsite` cmdlet을 실행하세요.

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging

스테이징 슬롯에서 업데이트를 확인 후, 남은 것은 이것을 프로덕션으로 교체하는 것입니다. Git 셸에서 바로 다음 명령을 실행합니다.

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

축하합니다. 프로덕션 웹 응용 프로그램에 새 업데이트를 성공적으로 게시 했습니다. 더욱 축하할 일은, 쉽게 개발과 테스트 환경을 생성하고, 모든 커밋을 구축하고 테스트함으로써 완료했다는 것입니다. 이들은 agile 소프트웨어 개발의 중요한 구성 요소입니다.

<a name="delete"></a>

## 개발 환경과 테스트 환경 삭제
사용자가 의도적으로 자체 포함된 리소스 그룹으로 개발 환경과 테스트 환경을 설계했기 때문에 삭제하는 것은 매우 쉽습니다. 이 자습서에서 만든 GitHub 분기와 Azure 결과물 둘 중에 하나를 지우려먼, Git 셸에서 다음 명령을 실행하기만 하면 됩니다.

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## 요약
Agile 소프트웨어 개발은 Azure를 응용 프로그램 플랫폼으로 채택하려는 대부분의 회사에게 필수품입니다. 이 자습서에서는, 프로덕션 환경, 심지어 복잡한 응용 프로그램의 정확하거나 비슷한 복제본을 쉽게 만들고 분리하는 방법에 대해 배웠습니다. 또한, Azure에서 모든 단일 커밋을 구축하고 테스트하는 개발 프로세스를 생성하는 기능을 활용하는 방법을 배웠습니다. 이 자습서는 사용자가 Azure 앱 서비스와 Azure 리소스 관리자를 함께 사용하여 agile 방법론에 부합하는 DevOps 솔루션을 제공하는 최상의 방법을 보여줍니다. 다음으로 [프로덕션에서 테스트](app-service-web-test-in-production-get-start.md)와 고급 DevOps 기술을 수행하여 시나리오를 계속 빌드할 수 있습니다. 일반적인 프로덕션 시나리오 테스트의 경우 [Azure 앱 서비스에서 Flighting 배포(베타 테스트)](app-service-web-test-in-production-controlled-test-flight.md)를 참조하세요.

## 추가 리소스
* [Azure에서 예측 가능하도록 복잡한 응용 프로그램을 배포](app-service-deploy-complex-application-predictably.md)
* [Agile 개발 연습에서: 현대화 개발 주기의 팁과 트릭](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
* [리소스 관리자 템플릿을 사용하여 Azure 웹앱의 고급 배포 전략](http://channel9.msdn.com/Events/Build/2015/2-620)
* [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)
* [JSONLint-JSON 유효성 검사기](http://jsonlint.com/)
* [ARMClient – 사이트로 GitHub 게시를 설정](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
* [Git 분기-기본 분기 및 병합](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [David Ebbo의 블로그](http://blog.davidebbo.com/)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure 플랫폼간 명령줄 도구](../xplat-cli-install.md)
* [Azure AD에서 사용자 만들기 또는 편집](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
* [프로젝트 Kudu Wiki](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0803_2016-->