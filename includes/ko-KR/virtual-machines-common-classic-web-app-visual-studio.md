

Azure용 웹 응용 프로그램 프로젝트를 만들면 Azure에서 가상 컴퓨터를 프로비전할 수 있습니다. 그런 후 추가 소프트웨어를 사용하여 가상 컴퓨터를 구성하거나 진단 또는 디버깅 용도로 가상 컴퓨터를 사용할 수 있습니다.

웹 응용 프로그램을 만들 때 가상 컴퓨터를 만들려면 다음 단계를 따르세요.

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트** > **웹**을 클릭하고 **ASP.NET 웹 응용 프로그램**을 선택합니다(**Visual C#** 또는 **Visual Basic** 노드 아래).
2. **새 ASP.NET 프로젝트** 대화 상자에서 원하는 웹 응용 프로그램 유형을 선택하고 대화 상자의 Azure 섹션(오른쪽 아래 구석)에서 **Host in the cloud(클라우드의 호스트)** 확인란을 선택합니다(이 확인란의 경우 일부 설치에서 **Create remote resources(원격 리소스 만들기)**로 표시됨).
   
    ![][0]
3. 이 예제의 경우 Microsoft Azure의 드롭다운 목록에서 **가상 컴퓨터(v1)**를 선택한 다음 **확인** 단추를 클릭합니다.
4. 메시지가 나타나는 경우 Azure에 로그인합니다. **가상 컴퓨터 만들기** 대화 상자가 나타납니다.
   
    ![][2]
5. **DNS 이름** 상자에 가상 컴퓨터의 이름을 입력합니다. DNS 이름은 Azure에서 고유해야 합니다. 입력한 이름을 사용할 수 없으면 빨간색 느낌표가 표시됩니다.
6. **이미지** 목록에서 가상 컴퓨터에 기반하는 이미지를 선택합니다. 표준 Azure 가상 컴퓨터 이미지 중 하나를 선택하거나 Azure에 업로드한 자체 이미지를 선택할 수 있습니다.
7. 다른 웹 서버를 설치할 계획이 아니라면 **Enable IIS and Web Deploy(IIS 및 웹 배포 사용)** 확인란을 선택된 상태로 둡니다. 웹 배포를 사용하지 않도록 설정하면 Visual Studio에서 게시할 수 없게 됩니다. 자체 사용자 지정 이미지를 비롯하여 패키지된 Windows Server 이미지 중 하나에 IIS 및 웹 배포를 추가할 수 있습니다.
8. **크기** 목록에서 가상 컴퓨터의 크기를 선택합니다.
9. 이 가상 컴퓨터의 로그인 자격 증명을 지정합니다. 원격 데스크톱을 통해 이 컴퓨터에 액세스할 때 필요하므로 이러한 자격 증명을 적어둡니다.
10. **위치** 목록에서 가상 컴퓨터를 호스팅하는 지역을 선택합니다.
11. **확인** 단추를 클릭하여 가상 컴퓨터 만들기를 시작합니다. **결과** 창에서 작업의 진행률을 확인할 수 있습니다.
    
    ![][3]
12. 가상 컴퓨터가 프로비전되면 솔루션의 **PublishScripts** 노드에 게시된 스크립트가 만들어집니다. 게시된 스크립트가 실행되고 Azure에서 가상 컴퓨터를 프로비전합니다. **출력** 창에 상태가 표시됩니다. 이 스크립트는 가상 컴퓨터 설치를 위해 다음을 수행합니다.
    
    * 가상 컴퓨터가 없는 경우 만듭니다.
    * 이름이 `devtest`로 시작하는 저장소 계정을 만듭니다. 단, 지정된 지역에 이러한 저장소 계정이 없어야 합니다.
    * 가상 컴퓨터에 대한 컨테이너로 클라우드 서비스를 만들고 웹 응용 프로그램에 대한 웹 역할을 만듭니다.
    * 가상 컴퓨터에서 웹 배포를 구성합니다.
    * 가상 컴퓨터에서 IIS 및 ASP.NET을 구성합니다.
    
    ![][4]
13. (선택 사항)새 가상 컴퓨터에 연결할 수 있습니다. **Server Explorer(서버 탐색기)**에서 **Virtual Machines(가상 컴퓨터)** 노드를 확장하고 만든 가상 컴퓨터에 대한 노드를 선택한 후 **Connect with Remote Desktop(원격 데스크톱으로 연결)**을 선택합니다. 또는 **클라우드 탐색기**에서 바로 가기 메뉴의 **포털에서 열기**를 선택하고 가상 컴퓨터에 연결할 수 있습니다.
    
    ![][5]

## <a name="next-steps"></a>다음 단계
앞서 만든 게시된 스크립트를 사용자 지정하려면 [Windows PowerShell 스크립트를 사용하여 개발 및 테스트 환경에 게시](http://msdn.microsoft.com/library/dn642480.aspx)에서 자세한 내용을 확인하세요.

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
