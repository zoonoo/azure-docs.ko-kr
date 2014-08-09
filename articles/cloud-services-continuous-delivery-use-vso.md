<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure web sites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />

Visual Studio Online을 사용하여 Azure에 지속적으로 전송
=======================================================

Visual Studio Online(이전에는 Team Foundation Service)은 사용자 지정 가능한 소스 코드 및 빌드 관리, Agile 개발 및 팀 프로세스 워크플로, 문제 및 작업 항목 추적 등을 제공하는 인기 있는 Microsoft TFS(Team Foundation Server) 소프트웨어의 클라우드 호스티드 서비스 버전입니다. Azure 웹 사이트 또는 클라우드 서비스에 자동으로 빌드 및 배포하도록 Visual Studio Online 팀 프로젝트를 구성할 수 있습니다. 온-프레미스 Team Foundation Server를 사용하여 연속 빌드 및 배포 시스템을 설정하는 방법에 대한 자세한 내용은 [Azure 클라우드 서비스의 지속적인 전송](../cloud-services-dotnet-continuous-delivery)(영문)을 참조하십시오.

이 자습서에서는 Visual Studio 2013 및 Azure SDK가 설치되어 있다고 가정합니다. Visual Studio 2013을 아직 설치하지 않은 경우 [www.visualstudio.com](http://www.visualstudio.com)에서 **무료로 시작하기** 링크를 선택하여 다운로드하십시오. Azure SDK의 경우 [여기](http://go.microsoft.com/fwlink/?LinkId=239540)에서 설치할 수 있습니다.

Visual Studio Online을 사용하여 Azure에 자동으로 빌드 및 배포하도록 클라우드 서비스를 설정하려면 다음 단계를 따르십시오.

-   [1단계: Visual Studio Online에 등록](#step1)

-   [2단계: 소스 제어에 프로젝트 체크 인](#step2)

-   [3단계: Azure에 프로젝트 연결](#step3)

-   [4단계: 변경한 후 다시 빌드 및 다시 배포 트리거](#step4)

-   [5단계: 초기 빌드 다시 배포(옵션)](#step5)

-   [6단계: 프로덕션 배포 변경(클라우드 서비스만)](#step6)

Visual Studio Online에 등록1단계: Visual Studio Online에 등록
-------------------------------------------------------------

1.  <http://www.visualstudio.com>으로 이동하여 Visual Studio Online 계정을 만듭니다. **로그인** 링크를 클릭합니다. Microsoft 계정을 사용하여 로그인해야 합니다. 처음으로 로그인하는 경우 이름 및 전자 메일 주소와 같은 일부 개인 정보를 지정하라는 메시지가 표시됩니다. ![](./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG)
![][0]

2.  이전에 로그인한 적이 있는 경우에는 로그인할 때 다음 화면이 표시됩니다. **지금 무료 계정 만들기** 링크를 클릭합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG)

3.  새 프로젝트용 계정 URL을 만듭니다. 계정 형식은 https://&lt;계정이름\>.visualstudio.com과 같습니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG)

4.  이제 첫 번째 프로젝트를 만들 수 있습니다. 프로젝트 이름 및 설명을 입력합니다. 사용할 버전 제어 시스템을 선택합니다. TFVC(Team Foundation Version Control) 또는 Git가 모두 지원됩니다. 이러한 옵션에 대한 자세한 내용은 [버전 제어 사용](http://go.microsoft.com/fwlink/?LinkId=324037)에서 찾아볼 수 있습니다. 이 연습에서는 TFVC를 사용한다고 가정합니다. 그런 다음, 조직에서 사용하는 프로세스 템플릿을 선택하고 **Create Project** 단추를 선택합니다. 프로세스 템플릿에 대한 자세한 내용은 [팀 프로젝트 아티팩트 작업, 프로세스 템플릿 선택](http://go.microsoft.com/fwlink/?LinkId=324035)을 참조하십시오.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs1.png)

5.  프로젝트가 생성되면 **Open with Visual Studio to connect** 단추를 클릭하여 팀 프로젝트에 연결된 Visual Studio를 자동으로 시작합니다. 보안 대화 상자가 표시되면 허용을 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs2.png)

소스 제어에 프로젝트 체크 인.2단계: 소스 제어에 프로젝트 체크 인
----------------------------------------------------------------

1.  Visual Studio에서 배포할 솔루션을 열거나 새 솔루션을 만듭니다. 이 연습의 단계에 따라 웹 사이트 또는 클라우드 서비스(Azure 응용 프로그램)를 배포할 수 있습니다. 새 솔루션을 만들려는 경우 새 Azure 클라우드 서비스 프로젝트 또는 새 ASP.NET MVC 프로젝트를 만듭니다. 프로젝트의 대상을 .NET Framework 4 또는 4.5로 지정했는지 확인하고, 클라우드 서비스 프로젝트를 만드는 경우 ASP.NET MVC 웹 역할 및 작업자 역할을 추가하고 웹 역할을 위한 인터넷 응용 프로그램을 선택합니다. 메시지가 표시되면 **인터넷 응용 프로그램**을 선택합니다. 웹 사이트를 만들려는 경우 ASP.NET 웹 응용 프로그램 프로젝트 템플릿을 선택한 후 MVC를 선택합니다. 자세한 내용은 [Azure 및 ASP.NET 시작](http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-dotnet-get-started/)(영문)을 참조하십시오.

2.  솔루션의 상황에 맞는 메뉴를 열고 **소스 제어에 솔루션 추가**를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs5.png)

3.  기본값을 그대로 사용하거나 변경한 후 **확인** 단추를 선택합니다. 프로세스가 완료되면 소스 제어 아이콘이 솔루션 탐색기에 표시됩니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs6.png)

4.  솔루션의 바로 가기 메뉴를 열고 **체크 인**을 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs7.png)

5.  팀 탐색기의 보류 중인 변경 내용 영역에서 체크 인에 대한 설명을 입력하고 **체크 인** 단추를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs8.png)
<br/>


체크 인할 때 특정 변경 내용을 포함하거나 제외하는 옵션을 선택할 수 있습니다. 원하는 변경 내용이 제외된 경우 **모두 포함** 링크를 선택합니다.

![][2]

Azure에 프로젝트 연결3단계: Azure에 프로젝트 연결
-------------------------------------------------

1.  일부 소스 코드를 포함한 VSO 팀 프로젝트가 있으므로 Azure에 팀 프로젝트를 연결할 준비가 되었습니다. [Azure 포털](http://manage.windowsazure.com)에서 클라우드 서비스 또는 웹 사이트를 선택하거나, 왼쪽 아래에 있는 + 아이콘을 선택하고 **클라우드 서비스** 또는 **웹 사이트**를 선택한 후 **빠른 생성**을 선택하여 새로 만듭니다. **Visual Studio Online으로 게시 설정** 링크를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs10.png)

2.  마법사의 텍스트 상자에 Visual Studio Online 계정의 이름을 입력하고 **지금 권한 부여** 링크를 클릭합니다. 로그인하라는 메시지가 표시될 수 있습니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs11.png)

3.  OAuth 팝업 대화 상자에서 **동의함**을 선택하여 Azure에 권한을 부여하고 VSO에서 팀 프로젝트를 구성합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs12.png)

4.  권한 부여가 완료되면 Visual Studio Online 팀 프로젝트 목록이 포함된 드롭다운이 표시됩니다. 이전 단계에서 만든 팀 프로젝트 이름을 선택하고 마법사의 확인 표시 단추를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs13.png)

5.  프로젝트가 연결되면 Visual Studio Online 팀 프로젝트에 대한 변경 내용을 체크 인하는 몇 가지 지침이 표시됩니다. 다음번에 체크 인할 때 Visual Studio Online에서 프로젝트를 Azure에 빌드 및 배포합니다. **Visual Studio에서 체크 인** 링크와 **Visual Studio 시작** 링크(또는 포털 화면 아래에 있는 해당 **Visual Studio** 단추)를 차례로 클릭하여 지금 이 작업을 시도합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs14.png)

다시 빌드 트리거4단계: 프로젝트 다시 빌드 및 다시 배포 트리거
-------------------------------------------------------------

1.  Visual Studio의 팀 탐색기에서 **소스 제어 탐색기** 링크를 클릭합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs15.png)

2.  솔루션 파일로 이동하여 해당 파일을 엽니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs16.png)

3.  솔루션 탐색기에서 파일을 열어 변경합니다. 예를 들어 MVC 웹 역할의 Views\\Shared 폴더에서 \_Layout.cshtml 파일을 변경합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs17.png)

4.  사이트 로고를 편집하고 Ctrl+S를 눌러 파일을 저장합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs18.png)

5.  팀 탐색기에서 **보류 중인 변경 내용** 링크를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs19.png)

6.  설명을 입력하고 **체크 인** 단추를 선택합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs20.png)

7.  홈 단추를 선택하여 팀 탐색기 홈페이지로 돌아갑니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs21.png)

8.  **빌드** 링크를 선택하여 진행 중인 빌드를 확인합니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs22.png)<br/>
<br/>
     팀 탐색기에서 빌드의 체크 인이 트리거되었음을 보여 줍니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs23.png)

9.  진행 중인 빌드 이름을 두 번 클릭하여 빌드가 진행되는 동안 생성되는 자세한 로그를 확인합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs24.png)

10. 빌드가 진행되는 동안 마법사를 사용하여 TFS를 Azure에 연결할 때 생성된 빌드 정의를 살펴봅니다. 빌드 정의의 바로 가기 메뉴를 열고 **빌드 정의 편집**을 선택합니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs25.png)
<br/>
     **트리거** 탭에서 기본적으로 체크 인할 때마다 빌드 정의가 빌드되도록 설정된 것을 확인합니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs26.png)
<br/>
     **프로세스** 탭에서 배포 환경이 사용 중인 클라우드 서비스 또는 웹 사이트의 이름으로 설정된 것을 확인할 수 있습니다. 웹 사이트에 대해 작업하고 있는 경우 표시되는 속성은 여기에서 표시된 속성과 다릅니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs27.png)
<br/>
     기본값 이외의 다른 값을 원하는 경우 속성 값을 지정합니다. 다음 표는 클라우드 서비스 속성의 기본값을 보여 줍니다.

    <table data-morhtml="true">
<tr data-morhtml="true"><td data-morhtml="true"><b data-morhtml="true">속성</b></td><td data-morhtml="true"><b data-morhtml="true">기본값</b></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">업그레이드 허용</td><td data-morhtml="true">true</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">클라우드 서비스 환경</td><td data-morhtml="true">스테이징</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">클라우드 서비스 이름</td><td data-morhtml="true">연결된 서비스 이름</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">배포 레이블</td><td data-morhtml="true">서비스 이름과 같음</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">서비스 구성</td><td data-morhtml="true">ServiceConfiguration.Cloud.cscfg</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">저장소 계정 이름</td><td data-morhtml="true">비어 있음, 저장소 계정을 검색할 수 있음</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">게시 프로필</td><td data-morhtml="true">.azurePubxml 파일. 프로필을 체크 인한 경우 여기에서 선택할 수 있음</td></tr>
</table>
<br/>
 저장소 계정 속성을 비워둔 경우 Azure에서 저장소 계정을 검색합니다. 클라우드 서비스와 동일한 이름의 저장소 계정이 있는 경우 그 이름을 사용합니다. 그렇지 않으면 다른 저장소 계정을 사용하거나, 저장소 계정이 없는 경우에는 계정을 새로 만듭니다. 저장소 계정은 저장소 파일 및 기타 데이터의 Azure 위치를 제공합니다. 자세한 내용은 [저장소 계정 정의](http://www.windowsazure.com/ko-kr/documentation/articles/storage-whatis-account)(영문)를 참조하십시오.

1.  이제 빌드가 완료됩니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs28.png)

2.  빌드 이름을 두 번 클릭하면 Visual Studio에서 연결된 단위 테스트 프로젝트의 모든 테스트 결과가 포함된 **빌드 요약**을 표시합니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs29.png)

3.  [Azure 포털](http://manage.windowsazure.com)에서 스테이징 환경을 선택하면 배포 탭에서 연결된 배포를 확인할 수 있습니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs30.png)

4.  사용 중인 사이트의 URL로 이동합니다. 웹 사이트의 경우 명령 모음의 찾아보기 단추를 클릭하면 됩니다. 클라우드 서비스의 경우 클라우드 서비스에 대한 스테이징 환경을 표시하는 **대시보드** 페이지의 **간략 상태** 섹션에서 URL을 선택합니다. 클라우드 서비스의 연속 통합에서 배포는 기본적으로 스테이징 환경에 게시됩니다. 대체 클라우드 서비스 환경 속성을 프로덕션으로 설정하여 이를 변경할 수 있습니다. 다음 스크린샷은 클라우드 서비스의 대시보드 페이지에서 사이트 URL이 어느 위치에 있는지 보여 줍니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs31.png)
<br/>
     새 브라우저 탭이 열리며 실행 중인 사이트를 표시합니다.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs32.png)

5.  클라우드 서비스의 경우 프로젝트의 다른 내용을 변경하고 추가 빌드를 트리거하면 여러 배포가 누적됩니다. 최신 배포가 활성으로 표시됩니다.

     ![](./media/cloud-services-continuous-delivery-use-vso/tfs33.png)

초기 빌드 다시 배포5단계: 초기 빌드 다시 배포
---------------------------------------------

이 단계는 클라우드 서비스에 적용되며 옵션입니다. 관리 포털에서 이전 배포를 선택하고 **다시 배포** 단추를 클릭하여 사이트를 이전 체크 인으로 되돌립니다. 그러면 TFS에 새 빌드가 트리거되고 배포 기록에 새 항목이 생성됩니다.
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs34.png)

프로덕션 배포 변경6단계: 프로덕션 배포 변경
-------------------------------------------

이 단계는 웹 사이트가 아닌 클라우드 서비스에만 적용됩니다. 준비가 되면 관리 포털에서 교환 단추를 선택하여 스테이징 환경에서 프로덕션 환경으로 수준을 올릴 수 있습니다. 새로 배포된 스테이징 환경에서 프로덕션으로 수준이 올라가며, 이전 프로덕션 환경이 있는 경우 스테이징 환경으로 변경됩니다. 활성 배포는 프로덕션 환경과 스테이징 환경에서 서로 다를 수 있지만 최근 빌드의 배포 기록은 환경과 관계없이 동일합니다.
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs35.png)

자세한 내용은 [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861)을 참조하십시오. Git를 사용하는 경우 [Git에서 코드 공유](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) 및 [소스 제어에서 Azure 웹 사이트로 게시](http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-publish-source-control)(영문)를 참조하십시오.

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
