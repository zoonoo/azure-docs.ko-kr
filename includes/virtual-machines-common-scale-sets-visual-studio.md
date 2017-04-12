

이 문서는 Visual Studio 리소스 그룹 배포를 사용하여 Azure 가상 컴퓨터 규모 집합을 배포하는 방법을 보여줍니다.

[Azure 가상 컴퓨터 규모 집합](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) 은 자동 크기 조정 및 부하 분산을 위해 손쉽게 통합되는 옵션을 사용하여 유사한 가상 컴퓨터 컬렉션을 배포하고 관리하기 위한 Azure 계산 리소스입니다. [Azure 리소스 관리자(ARM) 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용하여 VM 규모 집합을 프로비전하고 배포할 수 있습니다. ARM 템플릿은 Azure CLI, PowerShell, REST를 사용하여 배포가 가능하고 Visual Studio에서 직접 배포할 수도 있습니다. Visual Studio는 Azure 리소스 그룹 배포 프로젝트의 일부로 배포될 수 있는 예제 템플릿 집합을 제공합니다.

Azure 리소스 그룹 배포는 단일 배포 작업을 통해 관련된 Azure 리소스 집합을 그룹으로 모아서 게시하는 방법입니다. 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../articles/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

## <a name="pre-requisites"></a>필수 구성 요소
Visual Studio에서 VM 규모 집합 배포를 시작하려면 다음 항목이 필요합니다.

* Visual Studio 2013 또는 2015
* Azure SDK 2.7 또는 2.8

참고: 이 지침은 사용자가 [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)과 Visual Studio 2015를 사용하는 것으로 가정합니다.

## <a name="creating-a-project"></a>프로젝트 만들기
1. Visual Studio 2015에서 **파일 | 새로 만들기 | 프로젝트**를 선택하여 새 프로젝트를 만듭니다.
   
    ![파일 새로 만들기][file_new]
2. **Visual C# | 클라우드**에서 **Azure Resource Manager**를 선택하여 ARM 템플릿 배포를 위한 프로젝트를 만듭니다.
   
    ![프로젝트 만들기][create_project]
3. 템플릿 목록에서 Linux 또는 Windows 가상 컴퓨터 규모 집합 템플릿을 선택합니다.
   
   ![템플릿 선택][select_Template]
4. 프로젝트가 만들어지면 PowerShell 배포 스크립트, Azure 리소스 관리자 템플릿, 가상 컴퓨터 규모 집합에 대한 매개 변수 파일이 표시됩니다.
   
    ![솔루션 탐색기][solution_explorer]

## <a name="customize-your-project"></a>프로젝트 사용자 지정
이제 템플릿을 편집하여 응용 프로그램의 요구(예: VM 확장 속성 추가 또는 부하 분산 규칙 편집 등)에 맞게 사용자 지정할 수 있습니다. 기본적으로 VM 규모 집합 템플릿은 자동 크기 조정 규칙을 손쉽게 추가할 수 있게 AzureDiagnostics 확장을 배포하도록 구성됩니다. 또한 부하 분산 장치와 함께 SSH(Linux) 또는 RDP(Windows)로 VM 인스턴스에 연결할 수 있도록 하는 인바운드 NAT 규칙으로 구성된 공용 IP 주소를 배포합니다. 프런트 엔드 포트 범위는 50000에서 시작되며, 이렇게 하면 Linux의 경우, SSH를 공용 IP 주소(또는 도메인 이름)의 50000 포트로 연결하면, 규모 집합 내 첫 번째 VM의 22 포트로 라우팅됩니다. 50001 포트로 연결하면 두 번째 VM의 22 포트로 라우팅되며 이런 방식으로 계속됩니다. 

 Visual Studio에서 템플릿을 편집하는 좋은 방법은 JSON 개요를 사용하여 매개 변수, 변수, 리소스를 구성하는 것입니다. 스키마에 대한 이해를 바탕으로, Visual Studio는 템플릿을 배포하기 전에 거기에 포함된 오류를 알려줄 수 있습니다.

![JSON 탐색기][json_explorer]

## <a name="deploy-the-project"></a>프로젝트 배포
1. VM 규모 집합 리소스를 만들기 위해 ARM 템플릿을 Azure에 배포합니다. 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **배포 | 새 배포**를 선택합니다.
   
    ![템플릿 배포][5deploy_Template]
2. “리소스 그룹에 배포” 대화 상자에서 구독을 선택합니다.
   
    ![템플릿 배포][6deploy_Template]
3. 여기에서 템플릿을 배포할 새로운 Azure 리소스 그룹을 만들 수도 있습니다.
   
    ![새 리소스 그룹][new_resource]
4. 다음으로, **매개 변수 편집** 단추를 선택하여 템플릿에 전달될 매개 변수를 입력합니다. 배포를 만들려면 OS에 대한 사용자 이름 및 암호와 같은 특정한 값이 필요합니다.
   
    ![매개 변수 편집][edit_parameters]
5. 이제 **배포**를 클릭합니다. **출력** 창에 배포 진행률이 표시됩니다. 작업이 **Deploy-AzureResourceGroup.ps1** 스크립트를 실행 중입니다
   
   ![출력 창][output_window]

## <a name="exploring-your-vm-scale-set"></a>VM 규모 집합 탐색
배포가 완료되면 Visual Studio **Cloud Explorer** 에서(목록 새로 고침) 새로운 VM 규모 집합을 볼 수 있습니다. Cloud Explorer를 사용하면 응용 프로그램을 배포하는 동안 Visual Studio에서 Azure 리소스를 관리할 수 있습니다. Azure 포털 및 Azure 리소스 탐색기에서 VM 규모 집합을 볼 수도 있습니다.

![Cloud Explorer][cloud_explorer]

 포털은 웹 브라우저를 사용하여 Azure 인프라를 시각적으로 관리하는 최고의 방법을 제공하며, Azure 리소스 탐색기는 Azure 리소스를 탐색하고 디버그하는 손쉬운 방법을 제공하고, “인스턴스 뷰”에 대한 창을 제공하며 표시되는 리소스에 대한 PowerShell 명령을 보여줍니다. VM 규모 집합이 미리 보기로 제공되는 동안, 리소스 탐색기에 VM 규모 집합이 가장 자세히 표시됩니다.

## <a name="next-steps"></a>다음 단계
Visual Studio를 통해 VM 규모 집합을 성공적으로 배포하고 나면 응용 프로그램 요구 사항에 맞게 프로젝트를 추가로 사용자 지정할 수 있습니다. 예를 들어, Insights 리소스를 추가하여 자동 크기 조정을 설정하거나, 독립 실행형 VM처럼 템플릿에 인프라를 추가하거나, 사용자 지정 스크립트 확장을 사용하여 응용 프로그램을 배포할 수 있습니다. 예제 템플릿에 대한 유용한 소스는 GitHub 리포지토리 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates) 에서 찾을 수 있습니다("vmss" 검색).

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png

<!--HONumber=Jan17_HO3-->


