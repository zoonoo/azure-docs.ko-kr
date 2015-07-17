<properties 
	pageTitle="Azure에서 Visual Studio Online을 사용한 연속 배달" 
	description="자동으로 빌드되어 Azure 앱 서비스 또는 클라우드 서비스에 배포되도록 Visual Studio Online 팀 프로젝트를 구성하는 방법을 알아봅니다." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/08/2015" 
	ms.author="kempb"/>


# Visual Studio Online을 사용하여 Azure에 지속적으로 전송

  Azure 웹앱 또는 클라우드 서비스에 자동으로 빌드 및 배포하도록 Visual Studio Online 팀 프로젝트를 구성할 수 있습니다. *온-프레미스* Team Foundation Server를 사용하여 연속 빌드 및 배포 시스템을 설정하는 방법에 대한 자세한 내용은 [Azure 클라우드 서비스의 지속적인 전송](cloud-services-dotnet-continuous-delivery.md)(영문)을 참조하세요.

이 자습서에서는 Visual Studio 2013 및 Azure SDK가 설치되어 있다고 가정합니다. Visual Studio 2013을 아직 설치하지 않은 경우 **www.visualstudio.com**에서 [무료로 시작하기](http://www.visualstudio.com) 링크를 선택하여 다운로드하세요. Azure SDK의 경우 [여기](http://go.microsoft.com/fwlink/?LinkId=239540)에서 설치할 수 있습니다.

> [AZURE.NOTE]이 자습서를 완료하려면 Visual Studio Online 계정이 있어야 합니다. [Visual Studio Online 계정은 무료로 개설](http://go.microsoft.com/fwlink/p/?LinkId=512979)할 수 있습니다.

Visual Studio Online을 사용하여 Azure에 자동으로 빌드 및 배포하도록 클라우드 서비스를 설정하려면 다음 단계를 따르세요.

-   [1단계: 팀 프로젝트 만들기][]

-   [2단계: 소스 제어에 프로젝트 체크 인][]

-   [3단계: Azure에 프로젝트 연결][]

-   [4단계: 변경한 후 다시 빌드 및 다시 배포 트리거][]

-   [5단계: 초기 빌드 다시 배포(선택 사항)][]

-   [6단계: 프로덕션 배포 변경(클라우드 서비스만)][]

-	[7단계: 단위 테스트 실행(선택 사항)][]

## <a name="step1"></a>1단계: 팀 프로젝트 만들기

[여기](http://go.microsoft.com/fwlink/?LinkId=512980)에 나와 있는 지침에 따라 팀 프로젝트를 만들고 Visual Studio에 연결합니다. 이 연습에서는 TFVC(Team Foundation 버전 제어)를 소스 제어 솔루션으로 사용하고 있는 것으로 가정합니다. 버전 제어를 위해 Git를 사용하려는 경우 [이 연습의 Git 버전](http://go.microsoft.com/fwlink/p/?LinkId=397358)을 참조하세요.

## <a name="step2"> </a>2단계: 소스 제어에 프로젝트 체크 인

1. Visual Studio에서 배포할 솔루션을 열거나 새 솔루션을 만듭니다. 이 연습의 단계에 따라 웹앱 또는 클라우드 서비스(Azure 응용 프로그램)를 배포할 수 있습니다. 새 솔루션을 만들려는 경우 새 Azure 클라우드 서비스 프로젝트 또는 새 ASP.NET MVC 프로젝트를 만듭니다. 프로젝트의 대상을 .NET Framework 4 또는 4.5로 지정했는지 확인하고, 클라우드 서비스 프로젝트를 만드는 경우 ASP.NET MVC 웹 역할 및 작업자 역할을 추가하고 웹 역할을 위한 인터넷 응용 프로그램을 선택합니다. 메시지가 표시되면 **인터넷 응용 프로그램**을 선택합니다. 웹앱을 만들려는 경우 ASP.NET 웹 응용 프로그램 프로젝트 템플릿을 선택한 후 MVC를 선택합니다. [Azure 앱 서비스에서 ASP.NET 웹 응용 프로그램 만들기](../web-sites-dotnet-get-started.md)

> [AZURE.NOTE]Visual Studio Online은 현재 Visual Studio 웹 응용 프로그램의 CI 배포만 지원합니다. 웹 사이트 프로젝트는 범위를 벗어납니다.


2. 솔루션의 상황에 맞는 메뉴를 열고 **소스 제어에 솔루션 추가**를 선택합니다.<br/>
![][5]

3. 기본값을 그대로 사용하거나 변경한 후 **확인** 단추를 선택합니다. 프로세스가 완료되면 소스 제어 아이콘이 솔루션 탐색기에 표시됩니다.<br/>
![][6]

4. 솔루션의 바로 가기 메뉴를 열고 **체크 인**을 선택합니다.<br/>
![][7]

5. 팀 탐색기의 보류 중인 변경 내용 영역에서 체크 인에 대한 설명을 입력하고 **체크 인** 단추를 선택합니다.<br/>
![][8]

<br/> 체크 인할 때 특정 변경 내용을 포함하거나 제외하는 옵션을 선택할 수 있습니다. 원하는 변경 내용이 제외된 경우 **모두 포함** 링크를 선택합니다.<br/>
![][9]

## <a name="step3"> </a>3단계: Azure에 프로젝트 연결

1. 일부 소스 코드를 포함한 VSO 팀 프로젝트가 있으므로 Azure에 팀 프로젝트를 연결할 준비가 되었습니다. [Azure 포털](http://manage.windowsazure.com)에서 클라우드 서비스 또는 웹앱을 선택하거나, 왼쪽 아래에 있는 + 아이콘을 선택하고 **클라우드 서비스** 또는 **웹앱**을 선택한 후 **빠른 생성**을 선택하여 새로 만듭니다. **Visual Studio Online으로 게시 설정** 링크를 선택합니다.<br/>
![][10]

2. 마법사의 텍스트 상자에 Visual Studio Online 계정의 이름을 입력하고 **지금 권한 부여** 링크를 클릭합니다. 로그인하라는 메시지가 표시될 수 있습니다.<br/>
![][11]

3. OAuth 팝업 대화 상자에서 **동의함**을 선택하여 Azure에 권한을 부여하고 VSO에서 팀 프로젝트를 구성합니다.<br/>
![][12]

4. 권한 부여가 완료되면 Visual Studio Online 팀 프로젝트 목록이 포함된 드롭다운이 표시됩니다. 이전 단계에서 만든 팀 프로젝트 이름을 선택하고 마법사의 확인 표시 단추를 선택합니다.<br/>
![][13]

5. 프로젝트가 연결되면 Visual Studio Online 팀 프로젝트에 대한 변경 내용을 체크 인하는 몇 가지 지침이 표시됩니다. 다음번에 체크 인할 때 Visual Studio Online에서 프로젝트를 Azure에 빌드 및 배포합니다. **Visual Studio에서 체크 인** 링크와 **Visual Studio 시작** 링크(또는 포털 화면 아래에 있는 해당 **Visual Studio** 단추)를 차례로 클릭하여 지금 이 작업을 시도합니다.<br/>
![][14]

## <a name="step4"> </a>4단계: 프로젝트 다시 빌드 및 다시 배포 트리거

1. Visual Studio의 팀 탐색기에서 **소스 제어 탐색기** 링크를 클릭합니다.<br/>
![][15]

2. 솔루션 파일로 이동하여 해당 파일을 엽니다.<br/>
![][16]

3. 솔루션 탐색기에서 파일을 열어 변경합니다. 예를 들어 _MVC 웹 역할의 Views\Shared 폴더에서 Layout.cshtml 파일을 변경합니다.<br/>
![][17] 

4. 사이트 로고를 편집하고 Ctrl+S를 눌러 파일을 저장합니다.<br/>
![][18]

5. 팀 탐색기에서 **보류 중인 변경 내용** 링크를 선택합니다.<br/>
![][19]

6. 설명을 입력하고 **체크 인** 단추를 선택합니다.<br/>
![][20]

7. 홈 단추를 선택하여 팀 탐색기 홈페이지로 돌아갑니다.<br/>
![][21]

8. **빌드** 링크를 선택하여 진행 중인 빌드를 확인합니다.<br/>
![][22]
<br/>
팀 탐색기에서 빌드의 체크 인이 트리거되었음을 보여 줍니다.<br/>
![][23]

9. 진행 중인 빌드 이름을 두 번 클릭하여 빌드가 진행되는 동안 생성되는 자세한 로그를 확인합니다.<br/>
![][24]

10. 빌드가 진행되는 동안 마법사를 사용하여 TFS를 Azure에 연결할 때 생성된 빌드 정의를 살펴봅니다. 빌드 정의의 바로 가기 메뉴를 열고 **빌드 정의 편집**을 선택합니다.<br/>
![][25]
<br/>
**트리거** 탭에서 기본적으로 체크 인할 때마다 빌드 정의가 빌드되도록 설정된 것을 확인합니다.<br/>
![][26]
<br/>
**프로세스** 탭에서 배포 환경이 사용 중인 클라우드 서비스 또는 웹앱의 이름으로 설정된 것을 확인할 수 있습니다. 웹앱에 대해 작업하고 있는 경우 표시되는 속성은 여기에서 표시된 속성과 다릅니다.<br/>
![][27]
<br/>
기본값 이외의 다른 값을 원하는 경우 속성 값을 지정합니다. Azure 게시의 속성은 배포 섹션에 있습니다. 다음 테이블에서는 배포 섹션에서 사용할 수 있는 속성을 보여 줍니다.
	<table>
<tr><td><b>속성</b></td><td><b>기본값</b></td></tr>
><tr><td>신뢰할 수 없는 인증서 허용</td><td>false인 경우 루트 인증 기관에서 SSL 인증서에 서명해야 합니다.</td></tr>
<tr><td>업그레이드 허용</td><td>배포 환경에서 새 배포를 만드는 대신 기존 배포를 업데이트하도록 허용합니다. IP 주소를 유지합니다.</td></tr><tr><td>삭제 안 함</td><td>true인 경우 관련 없는 기존 배포를 덮어쓰지 않습니다(업그레이드가 허용됨).</td></tr>
<tr><td>배포 설정의 경로</td><td>보고서의 루트 폴더를 기준으로 웹앱의 .pubxml 파일 경로입니다. 클라우드 서비스의 경우 무시됩니다.</td></tr>
<tr><td>SharePoint 배포 환경</td><td>서비스 이름과 같음</td></tr>
<tr><td>Azure 배포 환경</td><td>웹앱 또는 클라우드 서비스 이름</td></tr>
</table> <br/>

여러 서비스 구성(.cscfg 파일)을 사용하는 경우 **빌드, 고급, MSBuild 인수** 설정에서 원하는 서비스 구성을 지정할 수 있습니다. 예를 들어 ServiceConfiguration.Test.cscfg를 사용하려면 MSBuild 인수 줄 옵션인 /p:TargetProfile=Test를 설정합니다.<br/>
![][38]

11. 이제 빌드가 완료됩니다.<br/>
![][28]

12. 빌드 이름을 두 번 클릭하면 Visual Studio에서 연결된 단위 테스트 프로젝트의 모든 테스트 결과가 포함된 **빌드 요약**을 표시합니다.<br/>
![][29]

13. [Azure 포털](http://manage.windowsazure.com)에서 스테이징 환경을 선택하면 배포 탭에서 연결된 배포를 확인할 수 있습니다.<br/>
![][30]

14.	사용 중인 사이트의 URL로 이동합니다. (웹앱의 경우 명령 모음의 찾아보기 단추를 클릭하면 됩니다. 클라우드 서비스의 경우 클라우드 서비스에 대한 스테이징 환경을 표시하는 **대시보드** 페이지의 **간략 상태** 섹션에서 URL을 선택합니다. 클라우드 서비스의 연속 통합에서 배포는 기본적으로 스테이징 환경에 게시됩니다. 대체 클라우드 서비스 환경 속성을 프로덕션으로 설정하여 이를 변경할 수 있습니다. 다음 스크린샷은 클라우드 서비스의 대시보드 페이지에서 사이트 URL이 어느 위치에 있는지 보여 줍니다. <br/> ![][31] <br/> 새 브라우저 탭이 열리며 실행 중인 사이트를 표시합니다.<br/>
![][32]

15.	클라우드 서비스의 경우 프로젝트의 다른 내용을 변경하고 추가 빌드를 트리거하면 여러 배포가 누적됩니다. 최신 배포가 활성으로 표시됩니다.<br/>
![][33]

## <a name="step5"> </a>5단계: 초기 빌드 다시 배포

이 단계는 클라우드 서비스에 적용되며 옵션입니다. 관리 포털에서 이전 배포를 선택하고 **다시 배포** 단추를 클릭하여 사이트를 이전 체크 인으로 되돌립니다. 그러면 TFS에 새 빌드가 트리거되고 배포 기록에 새 항목이 생성됩니다.<br/>
![][34]

## <a name="step6"> </a>6단계: 프로덕션 배포 변경

이 단계는 웹앱이 아닌 클라우드 서비스에만 적용됩니다. 준비가 되면 관리 포털에서 교환 단추를 선택하여 스테이징 환경에서 프로덕션 환경으로 수준을 올릴 수 있습니다. 새로 배포된 스테이징 환경에서 프로덕션으로 수준이 올라가며, 이전 프로덕션 환경이 있는 경우 스테이징 환경으로 변경됩니다. 활성 배포는 프로덕션 환경과 스테이징 환경에서 서로 다를 수 있지만 최근 빌드의 배포 기록은 환경과 관계없이 동일합니다.<br/>
![][35]

## <a name="step7"> </a>7단계: 단위 테스트 실행

이 단계는 클라우드 서비스가 아닌 웹앱에만 적용됩니다. 배포에 대한 품질 관문을 배치하기 위해 단위 테스트를 실행하고 실패할 경우 배포를 중지할 수 있습니다.

1.  Visual Studio에서 단위 테스트 프로젝트를 추가합니다.<br/>
![][39]

2.  테스트하려는 프로젝트에 프로젝트 참조를 추가합니다.<br/>
![][40]

3.  일부 단위 테스트를 추가합니다. 시작하려면 항상 통과하는 더미 테스트를 시도해 봅니다.

		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;
		
		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}

4.  빌드 정의를 편집하고 프로세스 탭을 선택한 후 테스트 노드를 확장합니다.


5.  **테스트 실패 시 빌드 실패**를 True로 설정합니다. 이는 테스트를 통과하지 못하면 배포가 수행되지 않는다는 의미입니다.<br/>
![][41]

6.  새 빌드를 큐에 넣습니다.<br/>
![][42]
<br/>
![][43]

7. 빌드가 처리되는 동안 진행 상태를 확인합니다.<br/>
![][44]
<br/>
![][45]

7. 빌드가 완료되면 테스트 결과를 확인합니다.<br/>
![][46]
<br/>
![][47]

8.  실패로 끝나는 테스트를 만들어 봅니다. 첫 번째 테스트를 복사하고 이름을 바꾼 후 NotImplementedException는 예상되는 예외라고 설명하는 주석을 추가하여 새 테스트를 추가합니다.

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. 변경 내용을 체크 인하여 새 빌드를 큐에 대기시킵니다.<br/>
![][48]

10. 테스트 결과에서 실패에 대한 세부 정보를 확인합니다.<br/>
![][49]
<br/>
![][50]

Visual Studio Online의 단위 테스트에 대한 자세한 내용은 [빌드에서 단위 테스트 실행](http://go.microsoft.com/fwlink/p/?LinkId=510474)을 참조하세요.

자세한 내용은 [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861)을 참조하세요. Git를 사용하는 경우 [Git에서 코드를 공유](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) 및 [Azure 앱 서비스에 GIT를 사용하여 연속 배포](../web-sites-publish-source-control.md).

[1단계: 팀 프로젝트 만들기]: #step1
[2단계: 소스 제어에 프로젝트 체크 인]: #step2
[3단계: Azure에 프로젝트 연결]: #step3
[4단계: 변경한 후 다시 빌드 및 다시 배포 트리거]: #step4
[5단계: 초기 빌드 다시 배포(선택 사항)]: #step5
[6단계: 프로덕션 배포 변경(클라우드 서비스만)]: #step6
[7단계: 단위 테스트 실행(선택 사항)]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
 

<!----HONumber=62-->