<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="Visual Studio Online 팀 프로젝트를 만들고 지속적인 배포를 설정하는 방법 - Microsoft Azure" metaKeywords="Visual Studio Online 팀 프로젝트 만들기, Azure에 대한 지속적인 배포" description="Visual Studio Online 팀 프로젝트를 만들고 Windows Azure에 연속 배포할 수 있도록 구성하는 방법에 대해 알아봅니다." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#Visual Studio Online 프로젝트 만들기 및 Azure에 지속적인 배포 설정 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Azure 관리 포털에서는 Visual Studio Online에서 팀 프로젝트를 만들고 웹 사이트에 대한 지속적인 배포가 가능하도록 웹 응용 프로그램을 구성할 수 있습니다.

##목차##

* [팀 프로젝트를 만드는 방법](#create_team_project)
* [새 웹 응용 프로그램을 만들고 Git 버전 제어에 추가하는 방법](#create_web_app)
* [지속적인 배포를 설정하는 방법](#continuous_deployment)

## <a name="create_team_project"></a>팀 프로젝트를 만드는 방법

1. 관리 포털에 로그인합니다.
2. 왼쪽 아래 모서리에서 **새로 만들기**를 클릭합니다.
3. **팀 프로젝트**를 클릭합니다.
4. 팀 프로젝트에 이름을 지정합니다. 팀 프로젝트가 만들어진 후에는 팀 프로젝트의 이름을 변경할 수 없습니다.
5. 프로젝트에 사용할 버전 제어 유형을 선택합니다. Git(분산된 버전 제어 시스템) 또는 Team Foundation 버전 제어(중앙 버전 제어 시스템)를 사용할 수 있습니다. 어느 시스템을 사용할지 잘 모르는 경우 [여기](http://msdn.microsoft.com/ko-kr/library/ms181368.aspx)를 참조하세요.
6. 프로세스 템플릿을 선택합니다. 프로세스 템플릿을 비교하여 보려면 [팀 프로젝트 아티팩트 작업](http://msdn.microsoft.com/ko-kr/library/ms400752.aspx)을 참조하세요.
7. 이 팀 프로젝트를 만드는 데 사용할 Visual Studio Online 계정을 선택하고 사용자를 추가하고 리소스 사용을 모니터링합니다.
8. 새 팀 프로젝트가 자동으로 시작 보드에 나타나도록 **Add to Startboard** 확인란을 선택된 상태로 둡니다.
9. **만들기**를 클릭합니다.

## <a name="create_web_app"></a>새 웹 응용 프로그램을 만들고 Git 버전 제어에 추가하는 방법

1. 시작 보드에서 새 팀 프로젝트를 클릭합니다.
2. **코드** 렌즈의 **리포지토리** 파트에서 팀 프로젝트 이름을 따서 이름이 지정된 Git 리포지토리를 클릭합니다.
3. 리포지토리/분기 블레이드에서 **Visual Studio** 블레이드 수준 명령을 클릭하여 Visual Studio에서 새 리포지토리를 엽니다. 웹 브라우저에서 Visual Studio 시작을 승인하라는 메시지가 표시됩니다.
4. Visual Studio의 팀 탐색기 도구 창에서 **Clone this repository**를 클릭하여 로컬 디스크에 새 리포지토리의 로컬 클론을 설정합니다.
5. 팀 탐색기 홈페이지의 **솔루션** 섹션에서 **새로 만들기...**를 클릭하여 방금 복제한 리포지토리에 새 프로젝트를 만듭니다.
6. 새 프로젝트 대화 상자에서 프로그래밍 언어 기본 설정에 따라 Visual Basic 또는 Visual C# 노드를 확장한 후 **웹**을 선택합니다.
7. 사용할 수 있는 프로젝트 템플릿 목록에서 **ASP.NET 웹 응용 프로그램**을 클릭하고 웹 응용 프로그램의 이름을 입력합니다.
8. **확인**을 클릭합니다.
9. 팀 탐색기 도구 창으로 전환하고, 변경 내용 페이지로 이동한 후, 커밋 메시지를 입력합니다.
10. **커밋** 단추에서 드롭다운 화살표를 클릭하고 **커밋 후 동기화**를 클릭하여 변경 내용을 커밋하고 이전에 복제한 원격 리포지토리에 이 커밋을 푸시합니다.

## <a name="continuous_deployment"></a>지속적인 배포를 설정하는 방법

1. 관리 포털에 로그인합니다.
2. 시작 보드에서 이전에 만든 팀 프로젝트를 클릭합니다.
3. **빌드** 렌즈에서 **연속 배포 설정** 파트를 클릭합니다.
4. 웹 응용 프로그램을 배포할 웹 사이트를 선택합니다.
5. 소스 코드가 있는 리포지토리를 선택합니다(이때 팀 프로젝트에 리포지토리가 하나만 있음).
6. 빌드할 분기를 선택합니다. Visual Studio Online이 콘텐츠에서 이 분기의 가장 최근 커밋에 있는 단일 Visual Studio 솔루션 파일(*.sln)을 검사합니다. 파일을 찾으면 해당 솔루션을 빌드하기 위한 새 빌드 정의가 구성됩니다(이름이 "_CD"로 끝남). 솔루션 파일을 찾지 못하거나 파일을 여러 개 찾는 경우에도 새 빌드 정의가 구성되지만 빌드 정의가 사용되지 않도록 설정되며, 빌드할 솔루션을 지정하려면 Visual Studio에서 빌드 정의를 편집해야 합니다. 
7. **만들기**를 클릭합니다.
8. Visual Studio Online이 새 빌드 정의를 만들고 웹 응용 프로그램 프로젝트를 빌드 및 배포하고 해당 정의의 새 빌드를 큐에 넣습니다.

###배포가 완료되었는지 확인하려면###

1. 시작 보드에서 이전에 만든 팀 프로젝트를 클릭합니다.
2. **빌드** 렌즈에서 **최신 빌드** 파트를 클릭하여 빌드 블레이드를 엽니다.
3. 빌드 블레이드에서 **배포** 파트의 첫 번째 항목을 클릭하여 연결된 웹 사이트를 엽니다.
4. 웹 사이트 블레이드에서 **찾아보기** 블레이드 수준 명령을 클릭하여 웹 사이트를 찾고 웹 응용 프로그램의 배포를 확인합니다.
