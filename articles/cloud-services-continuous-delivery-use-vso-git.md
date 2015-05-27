<properties 
	pageTitle="Azure에서 Visual Studio Online을 사용한 지속적 전송" 
	description="자동으로 빌드되어 Azure 웹 사이트 또는 클라우드 서비스에 배포되도록 Visual Studio Online 팀 프로젝트를 구성하는 방법에 대해 알아봅니다." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>




# Visual Studio Online 및 Git을 사용하여 Azure에 지속적으로 전송

Visual Studio Online 팀 프로젝트를 사용하여 소스 코드용 Git 리포지토리를 호스트할 수 있으며 리포지토리로 커밋을 푸시할 때마다 Azure 웹 사이트 또는 클라우드 서비스를 자동으로 빌드 및 배포할 수 있습니다.

Visual Studio 2013 및 Azure SDK가 설치되어 있어야 합니다. Visual Studio 2013을 아직 설치하지 않은 경우 **www.visualstudio.com**에서 [무료로 시작하기](http://www.visualstudio.com) 링크를 선택하여 다운로드하세요. Azure SDK의 경우 [여기](http://go.microsoft.com/fwlink/?LinkId=239540)에서 설치할 수 있습니다.


> [AZURE.NOTE]이 자습서를 완료하려면 Visual Studio Online 계정이 있어야 합니다. [Visual Studio Online 계정은 무료로 개설](http://go.microsoft.com/fwlink/p/?LinkId=512979)할 수 있습니다.

Visual Studio Online을 사용하여 Azure에 자동으로 빌드 및 배포하도록 클라우드 서비스를 설정하려면 다음 단계를 따르세요.

-   [1단계: Git 리포지토리 만들기][]

-   [2단계: 프로젝트를 만들어 Git 리포지토리로 푸시][]

-   [3단계: Azure에 프로젝트 연결][]

-   [4단계: 변경한 후 다시 빌드 및 다시 배포 트리거][]

-   [5단계: 초기 빌드 다시 배포(선택 사항)][]

-   [6단계: 프로덕션 배포 변경][]

-	[7단계: 작업 분기에서 배포][]

<h2> <a name="step1"></a>1단계: Git 리포지토리 만들기</h2>


1. Visual Studio Online 계정이 없는 경우 [여기](http://go.microsoft.com/fwlink/?LinkId=397665) 지침을 따릅니다. 팀 프로젝트를 만들 때 소스 제어 시스템으로 Git을 선택합니다. 지침을 따라 Visual Studio를 팀 프로젝트에 연결합니다.

2. 팀 탐색기에서 **이 리포지토리 복제** 링크를 선택합니다.![][3]

3. 로컬 복사본의 위치를 지정하고 **복제** 단추를 클릭합니다.
 
<h2><a name="step2"> </a>2단계: 프로젝트를 만들어 리포지토리로 푸시</h2>

1. 팀 편집기의 솔루션 섹션에서 로컬 리포지토리에 새 프로젝트를 만들 수 있는 새 링크를 선택합니다.<br/> ![][4]

2. 이 연습의 단계에 따라 웹 사이트 또는 클라우드 서비스(Azure 응용 프로그램)를 배포할 수 있습니다. 새 Azure 클라우드 서비스 프로젝트 또는 새 ASP.NET MVC 프로젝트를 만듭니다. 프로젝트의 대상을 .NET Framework 4 또는 4.5로 지정했는지 확인하고, 클라우드 서비스 프로젝트를 만드는 경우 ASP.NET MVC 웹 역할 및 작업자 역할을 추가합니다. 웹 사이트를 만들려는 경우 ASP.NET 웹 응용 프로그램 프로젝트 템플릿을 선택한 후 MVC를 선택합니다. [Azure 웹 사이트 및 ASP.NET 시작](web-sites-dotnet-get-started.md)을 참조하세요.

3. 솔루션의 바로 가기 메뉴를 열고 **커밋**을 선택합니다.<br/> ![][7]

4. Visual Studio Online에서 처음으로 Git을 사용하는 경우 Git에서 본인을 식별해 주는 정보를 제공해야 합니다. 팀 탐색기의 **보류 중인 변경 내용** 영역에서 사용자 이름 및 메일 주소를 입력합니다. 커밋에 대한 설명을 입력하고 **커밋**을 선택합니다.<br/> ![][8]

5. 체크 인할 때 특정 변경 내용을 포함하거나 제외하는 옵션을 선택할 수 있습니다. 원하는 변경 내용이 제외된 경우 **모두 포함** 링크를 선택합니다.<br/>

6. 이제 리포지토리에 있는 로컬 복사본의 변경 내용을 커밋했습니다. 그런 다음 변경 내용을 서버와 동기화합니다. **동기화** 링크를 선택합니다.

<h2> <a name="step3"> </a>3단계: Azure에 프로젝트 연결</h2>

1. Visual Studio Online에 일부 소스 코드를 포함한 Git 리포지토리가 있으므로 Azure에 Git 리포지토리를 연결할 준비가 되었습니다. [Azure 포털](http://manage.windowsazure.com)에서 클라우드 서비스 또는 웹 사이트를 선택하거나, 왼쪽 아래에 있는 + 아이콘을 선택하고 **클라우드 서비스** 또는 **웹 사이트**를 선택한 후 **빠른 생성**을 선택하여 새로 만듭니다.<br.> ![][9]

3. 클라우드 서비스의 경우 **Visual Studio Online으로 게시 설정** 링크를 선택합니다. 웹 사이트의 경우 **소스 코드에서 배포 설정** 링크를 선택합니다.<br/> ![][10]

2. 마법사의 텍스트 상자에 Visual Studio Online 계정의 이름을 입력하고 **지금 권한 부여** 링크를 선택합니다. 로그인하라는 메시지가 표시될 수 있습니다.<br/> ![][11]

3. OAuth 팝업 대화 상자에서 **동의함**을 선택하여 Azure에 권한을 부여하고 Visual Studio Online에서 팀 프로젝트를 구성합니다.<br/> ![][12]

4. 권한 부여가 완료되면 Visual Studio Online 팀 프로젝트를 포함하는 드롭다운 목록이 표시됩니다. 이전 단계에서 만든 팀 프로젝트 이름을 선택하고 마법사의 확인 표시 단추를 선택합니다.<br/> ![][13]

다음에 리포지토리에 커밋을 푸시할 때 Visual Studio Online이 프로젝트를 빌드하여 Azure에 배포합니다.<br/>


<h2><a name="step4"> </a>4단계: 프로젝트 다시 빌드 및 다시 배포 트리거</h2>

1. Visual Studio에서 파일을 열어 변경합니다. 예를 들어 _MVC 웹 역할의 Views\\Shared 폴더에서 Layout.cshtml 파일을 변경합니다.<br/> ![][17] 
2. 사이트에 대한 바닥글 텍스트를 편집하고 파일을 저장합니다.<br/> ![][18]

3. 솔루션 탐색기에서 솔루션 노드, 프로젝트 노드 또는 변경한 파일의 바로 가기 메뉴를 열고 **커밋**을 선택합니다.<br/>

4. 설명을 입력하고 **커밋**을 선택합니다.<br/> ![][20]

5. **동기화** 링크를 선택합니다.<br/> ![][38]

6. **푸시** 링크를 선택하여 Visual Studio Online의 리포지토리에 커밋을 푸시합니다. 또한 **동기화** 단추를 사용하여 리포지토리로 커밋을 복사할 수도 있습니다. **동기화**는 리포지토리의 최신 변경 내용을 끌어온다는 점이 다릅니다.<br/> ![][39]

7. 홈 단추를 선택하여 팀 탐색기 홈페이지로 돌아갑니다.<br/> ![][21]

8. **빌드** 링크를 선택하여 진행 중인 빌드를 확인합니다.<br/> ![][22] <br/> 팀 탐색기에서 빌드의 체크 인이 트리거되었음을 보여 줍니다.<br/> ![][23]

9. 빌드가 진행되는 동안 자세한 로그를 보려면 진행 중인 빌드의 이름을 두 번 클릭합니다.<br/>

10. 빌드가 진행되는 동안 마법사를 사용하여 Azure에 연결할 때 생성된 빌드 정의를 살펴봅니다. 빌드 정의의 바로 가기 메뉴를 열고 **빌드 정의 편집**을 선택합니다.<br/> ![][25] <br/> **트리거** 탭에서 기본적으로 체크 인할 때마다 빌드 정의가 빌드되도록 설정된 것을 확인합니다. 클라우드 서비스의 경우 Visual Studio Online에서 마스터 분기를 자동으로 빌드하여 스테이징 환경에 배포합니다. 라이브 사이트에 배포하려면 여전히 수동 단계를 수행해야 합니다. 스테이징 환경이 없는 웹 사이트의 경우 마스터 분기를 라이브 사이트에 직접 배포합니다.<br/> ![][26] <br/> **프로세스** 탭에서 배포 환경이 사용 중인 클라우드 서비스 또는 웹 사이트의 이름으로 설정된 것을 확인할 수 있습니다.<br/> ![][27] <br/> 기본값 이외의 다른 값을 원하는 경우 속성 값을 지정합니다. Azure 게시의 속성은 배포 속성에 있으며 MSBuild 매개 변수를 설정해야 할 수 있습니다. 예를 들어 클라우드 서비스 프로젝트에서 "클라우드" 외의 서비스 구성을 지정하려면 MSbuild 매개 변수를 /p:TargetProfile=*YourProfile*로 설정합니다. 여기서 *YourProfile*은 ServiceConfiguration.*YourProfile*.cscfg와 같은 이름의 서비스 구성 파일입니다. 다음 표에서는 배포 섹션에서 사용할 수 있는 속성을 보여 줍니다. <table> <tr><td><b>속성</b></td><td><b>기본값</b></td></tr>
><tr><td>신뢰할 수 없는 인증서 허용</td><td>false인 경우 루트 인증 기관에서 SSL 인증서에 서명해야 합니다.</td></tr>
<tr><td>업그레이드 허용</td><td>새로운 배포를 만들지 않고 기존 배포를 업데이트할 수 있습니다. IP 주소를 유지합니다.</td></tr><tr><td>삭제 안 함</td><td>true인 경우 관련 없는 기존 배포를 덮어쓰지 않습니다(업그레이드가 허용됨).</td></tr>
<tr><td>배포 설정 경로</td><td>웹 사이트의 .pubxml 파일 경로로, 리포지토리의 루트 폴더에 상대적입니다. 클라우드 서비스의 경우 무시됩니다.</td></tr>
<tr><td>SharePoint 배포 환경</td><td>서비스 이름과 같음</td></tr>
<tr><td>Azure 배포 환경</td><td>웹 사이트 또는 클라우드 서비스 이름</td></tr>
</table> <br/>

11. 이제 빌드가 완료됩니다.<br/> ![][28]

12. 빌드 이름을 두 번 클릭하면 Visual Studio에서 연결된 단위 테스트 프로젝트의 모든 테스트 결과가 포함된 **빌드 요약**을 표시합니다.<br/> ![][29]

13. [Azure 포털](http://manage.windowsazure.com)에서 스테이징 환경을 선택하면 배포 탭에서 연결된 배포를 확인할 수 있습니다.<br/> ![][30]

14.	사용 중인 사이트의 URL로 이동합니다. 웹 사이트의 경우 포털에서 **찾아보기** 단추를 선택합니다. 클라우드 서비스의 경우 클라우드 서비스에 대한 스테이징 환경을 표시하는 **대시보드** 페이지의 **간략 상태** 섹션에서 URL을 선택합니다. 클라우드 서비스의 연속 통합에서 배포는 기본적으로 스테이징 환경에 게시됩니다. 대체 클라우드 서비스 환경 속성을 프로덕션으로 설정하여 이를 변경할 수 있습니다. 다음 스크린샷은 클라우드 서비스의 대시보드 페이지에서 사이트 URL이 어느 위치에 있는지 보여 줍니다. <br/> ![][31] <br/> 새 브라우저 탭이 열리며 실행 중인 사이트를 표시합니다.<br/> ![][32]

15.	프로젝트의 다른 내용을 변경하고 추가 빌드를 트리거하면 여러 배포가 누적됩니다. 최신 배포가 활성으로 표시됩니다.<br/> ![][33]

<h2> <a name="step5"> </a>5단계: 초기 빌드 다시 배포</h2>

이 단계는 옵션입니다. 관리 포털에서 이전 배포를 선택하고 **다시 배포**를 클릭하여 사이트를 이전 체크 인으로 되돌립니다. 그러면 TFS에 새 빌드가 트리거되고 배포 기록에 새 항목이 생성됩니다.<br/> ![][34]

<h2> <a name="step6"> </a>6단계: 프로덕션 배포 변경</h2>

 준비가 되면 관리 포털에서 **교환**을 선택하여 스테이징 환경에서 프로덕션 환경으로 수준을 올릴 수 있습니다. 새로 배포된 스테이징 환경에서 프로덕션으로 수준이 올라가며, 이전 프로덕션 환경이 있는 경우 스테이징 환경으로 변경됩니다. 활성 배포는 프로덕션 환경과 스테이징 환경에서 서로 다를 수 있지만 최근 빌드의 배포 기록은 환경과 관계없이 동일합니다.<br/> ![][35]

<h2> <a name="step7"> </a>6단계: 작업 분기에서 배포</h2>

Git을 사용할 경우 보통 작업 분기에서 변경한 다음 개발이 최종 단계에 도달하면 마스터 분기로 통합합니다. 프로젝트의 개발 단계에서는 작업 분기를 구성하여 Azure에 배포합니다.

1. 팀 탐색기에서 **홈** 단추를 선택한 다음 **분기** 단추를 선택합니다.<br/> ![][40]

2. **새 분기** 링크를 선택합니다.<br/> ![][41]

3. "working"과 같은 분기의 이름을 입력하고 **분기 만들기**를 선택합니다. 그러면 새 로컬 분기가 생성됩니다.<br/> ![][42]

4. 분기를 게시합니다. **게시 취소된 분기**에서 분기 이름을 선택하고 **게시**를 선택합니다.<br/> ![][44]

6. 기본적으로 마스터 분기만 변경하면 연속 빌드가 트리거됩니다. 작업 분기의 연속 빌드를 설정하려면 팀 탐색기에서 빌드 페이지를 선택하고 **빌드 정의 편집**을 선택합니다.

7. **소스 설정** 탭을 선택합니다. **연속 통합 및 빌드의 모니터링된 분기**에서 **새 행을 추가하려면 여기를 클릭하세요.**를 선택합니다.<br/> ![][47]

8. 생성된 분기(예: refs/heads/working)를 지정합니다. ![][48]

9. 코드를 변경하고, 변경된 파일의 바로 가기 메뉴를 열고, **커밋**을 선택합니다.<br/> ![][43]

10. **동기화되지 않은 커밋** 링크를 선택한 다음 **동기화** 단추 또는 **푸시** 링크를 선택하여 Visual Studio Online에서 작업 분기의 복사본 변경 내용을 복사합니다. ![][45]

11. **빌드** 뷰로 이동하여 작업 분기에 대해 트리거된 빌드를 찾습니다.

자세한 내용은 [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861)을 참조하세요. Visual Studio Online에서 Git 사용에 대한 추가 팁은 [Git에서 코드 공유](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)를 참조하고, Visual Studio Online에서 관리하지 않는 Git 리포지토리를 사용하여 Azure에 게시하는 방법에 대한 자세한 내용은 [Git를 사용하여 Azure 웹 사이트에 게시](web-sites-publish-source-control.md)를 참조하세요.

[1단계: Git 리포지토리 만들기]: #step1
[2단계: 프로젝트를 만들어 Git 리포지토리로 푸시]: #step2
[3단계: Azure에 프로젝트 연결]: #step3
[4단계: 변경한 후 다시 빌드 및 다시 배포 트리거]: #step4
[5단계: 초기 빌드 다시 배포(선택 사항)]: #step5
[6단계: 프로덕션 배포 변경]: #step6
[7단계: 작업 분기에서 배포]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!--HONumber=54-->