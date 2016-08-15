<properties
	pageTitle="Azure 리소스 그룹 프로젝트를 사용하여 VS Team Services에서 연속 통합 | Microsoft Azure"
	description="Visual Studio에서 Azure 리소스 그룹 배포 프로젝트를 사용하여 Visual Studio Team Services에서의 연속 통합을 설정하는 방법을 설명합니다."
	services="visual-studio-online"
	documentationCenter="na"
	authors="mlearned"
	manager="erickson-doug"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/01/2016"
	ms.author="mlearned" />

# Azure 리소스 그룹 배포 프로젝트를 사용하여 Visual Studio Team Services에서 연속 통합

Azure 템플릿을 배포하려면 작업을 수행하여 빌드, 테스트, Azure에 복사("스테이징"이라고도 함), 템플릿 배포 등, 다양한 단계를 통과해야 합니다. Visual Studio Team Services(VS Team Services)에서는 두 가지 방법으로 이 작업을 수행할 수 있습니다. 두 방법 모두 결과는 같으므로 사용자의 워크플로에 가장 적합한 방법을 선택하면 됩니다.

-	Azure 리소스 그룹 배포 프로젝트(Deploy-AzureResourceGroup.ps1)에 포함된 PowerShell 스크립트를 실행하는 한 단계를 빌드 정의에 추가합니다. 이 스크립트는 아티팩트를 복사한 뒤 템플릿을 배포합니다.
-	각각 단계 작업을 수행하는 여러 VS Team Services 빌드 단계를 추가합니다.

이 문서에서는 첫 번째 옵션(PowerShell 스크립트를 실행하는 빌드 정의 추가) 사용 방법을 설명합니다. 이 옵션의 이점은 개발자가 Visual Studio에서 사용하는 스크립트가 VS Team Services에서 사용한 것과 동일하다는 것입니다. 이 절차에서는 이미 VS Team Services에 Visual Studio 배포 프로젝트를 체크인했다고 가정합니다.

## 아티팩트를 Azure로 복사 

시나리오에 관계없이 템플릿 배포에 필요한 아티패트가 있으면 해당 항목에 Azure 리소스 관리자 액세스 권한을 부여해야 합니다. 이러한 아티팩트에는 다음과 같은 파일이 포함될 수 있습니다.

-	중첩된 템플릿
-	구성 스크립트 및 DSC 스크립트
-	응용 프로그램 이진 파일

### 중첩된 템플릿 및 구성 스크립트
Visual Studio에서 제공하는 템플릿을 사용할 경우(또는 Visual Studio 스니펫으로 빌드한 템플릿) PowerShell 스크립트가 아티팩트를 스테이징할 뿐 아니라, 다른 배포를 위해 리소스에 대한 URI를 매개 변수화합니다. 그런 다음 스크립트는 아티팩트를 Azure의 보안 컨테이너에 복사하고, 해당 컨테이너에 대한 SaS 토큰을 만든 다음 이 정보를 템플릿 배포에 전달합니다. 중첩된 템플릿에 대한 자세한 내용은 [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx)를 참조하세요.

## VS Team Services에서 연속 배포 설정

VS Team Services에서 PowerShell 스크립트를 호출하려면 빌드 정의를 업데이트해야 합니다. 이 단계는 요약하면 다음과 같습니다.

1.	빌드 정의를 편집합니다.
1.	VS Team Services에서 Azure 권한을 설정합니다.
1.	Azure 리소스 그룹 배포 프로젝트에서 PowerShell 스크립트를 참조하는 Azure PowerShell 빌드 단계를 추가 합니다.
1.	VS Team Services에서 빌드한 프로젝트의 작업을 위해 *-ArtifactsStagingDirectory* 매개 변수의 값을 설정합니다.

### 자세한 연습

다음 단계에서는 VS Team Services에서 연속 배포를 구성하는 데 필요한 단계를 안내합니다.

1.	VS Team Services 빌드 정의를 편집하고 Azure PowerShell 빌드 단계를 추가합니다. **빌드 정의** 범주 아래에서 빌드 정의를 선택하고 **편집** 링크를 선택합니다.

    ![][0]

1.	새 **Azure PowerShell** 빌드 단계를 빌드 정의에 추가한 다음 **빌드 단계 추가...** 단추를 선택합니다.

    ![][1]

1.	**배포 작업** 범주를 선택한 다음 **Azure PowerShell** 작업을 선택하고 해당 항목의 **추가** 버튼을 선택합니다.

    ![][2]

1.	**Azure PowerShell** 빌드 단계를 선택하고 해당 값을 입력합니다.

    1.	이미 VS Team Services에 추가된 Azure 서비스 끝점이 있는 경우 **Azure 구독** 드롭다운 목록 상자에서 구독을 선택하고 다음 섹션으로 건너뜁니다.

        VS Team Services에 Azure 서비스 끝점이 없으면 추가해야 합니다. 이 하위 섹션에서는 이 프로세스를 안내합니다. Azure 계정에서 Microsoft 계정(예: Hotmail)을 사용할 경우 다음 단계를 통해 서비스 주체 인증을 가져와야 합니다.

    1.	**Azure 구독** 드롭다운 목록 상자 옆의 **관리** 링크를 클릭합니다.

        ![][3]

    1. **새 서비스 끝점** 드롭다운 목록 상자에서 **Azure**를 선택합니다.

        ![][4]

    1.	**Azure 구독 추가** 대화 상자에서 **서비스 사용자** 옵션을 선택합니다.

        ![][5]

    1.	Azure 구독 정보를 **Azure 구독 추가** 대화 상자에 추가합니다. 다음 정보를 제공해야 합니다.
        -	구독 ID
        -	구독 이름
        -	서비스 주체 ID
        -	서비스 주체 키
        -	테넌트 ID

    1.	**구독** 이름 상자 옆에 선택의 이름을 추가합니다. 나중에 이 값은 VS Team Services의 **Azure 구독** 드롭다운 목록에 표시됩니다.

    1.	Azure 구독 ID를 모르는 경우 다음 명령 중 하나를 사용하여 확인할 수 있습니다.
        
        PowerShell 스크립트의 경우

        `Get-AzureRmSubscription`

        Azure CLI의 경우

        `azure account show`
    

    1.	서비스 주체 ID, 서비스 주체 키 및 테넌트 ID를 가져오려면 [Active Directory 응용 프로그램 및 서비스 주체 만들기](resource-group-create-service-principal-portal.md) 또는 [Azure 리소스 관리자를 사용한 서비스 주체 인증](resource-group-authenticate-service-principal.md)의 절차에 따릅니다.

    1.	서비스 주체 ID, 서비스 주체 키 및 테넌트 ID 값을 **Azure 구독 추가**에 추가한 다음 **확인** 단추를 선택합니다.

        이제 Azure PowerShell 스크립트를 실행는 데 사용할 유효한 서비스 주체를 만들었습니다.

1.	빌드 정의를 편집하고 **Azure PowerShell** 빌드 단계를 선택합니다. **Azure 구독** 드롭다운 목록 상자의 구독을 선택합니다. 구독이 나타나지 않으면 **관리** 링크 옆의 **새로 고침** 단추를 선택합니다.

    ![][8]

1.	Deploy-AzureResourceGroup.ps1 PowerShell 스크립트에 대한 경로를 제공합니다. 이 작업을 수행하려면 **스크립트 경로** 상자 옆의 말줄임표(...) 버튼을 선택하고, 프로젝트의 **Scripts** 폴더에서 Deploy-AzureResourceGroup.ps1 PowerShell 스크립트로 이동한 다음 **확인** 단추를 선택합니다.

    ![][9]

1. 스크립트를 선택한 다음 Build.StagingDirectory에서 실행되도록 스크립트 경로를 업데이트합니다(*ArtifactsLocation*이 설정된 것과 동일한 디렉터리). "$(Build.StagingDirectory)/"를 스크립트 경로 앞에 추가하면 됩니다.

    ![][10]

1.	**스크립트 인수** 상자에 다음 매개 변수를 입력합니다(한 줄로). Visual Studio에서 스크립트를 실행하면 **출력** 창에서 VS가 매개 변수를 어떻게 사용하는지 확인할 수 있습니다. 이 정보를 빌드 단계에서 매개 변수 값을 설정하기 위한 출발점으로 사용할 수 있습니다.

    | 매개 변수 | 설명|
    |---|---|
    | -ResourceGroupLocation | 리소스 그룹이 위치한 geo-location 값(예: **eastus** 또는 **'East US'**). 이름에 공백이 있을 경우 작은따옴표를 추가합니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/ko-KR/regions/)을 참조하세요.| |
    | -ResourceGroupName | 이 배포에 사용되는 리소스 그룹의 이름입니다.| |
    | -UploadArtifacts | 이 매개 변수가 있는 경우 로컬 시스템에서 Azure에 업로드해야 하는 아티팩트를 지정합니다. 템플릿 배포에서 PowerShell 스크립트를 사용하여 스테이징하려는 추가 아티펙트가 필요한 경우에만 이 스위치를 설정합니다(예: 구성 스크립트 또는 중첩된 템플릿). |
    | -StorageAccountName | 이 배포에 대한 스테이지 아티팩트에 사용되는 저장소 계정 이름. Azure에 아티팩트를 복사하는 경우에만 이 매개 변수가 필요합니다. 이 저장소 계정은 배포에서 자동으로 만들어지지 않으며 기존에 있어야 합니다.| |
    | -StorageAccountResourceGroupName | 저장소 계정과 연결된 리소스 그룹의 이름. Azure에 아티팩트를 복사하는 경우에만 이 매개 변수가 필요합니다.| |
    | -TemplateFile | Azure 리소스 그룹 배포 프로젝트의 템플릿 파일에 대한 경로. 유연성을 증대하려면 이 매개 변수에 절대 경로 대신 PowerShell 스크립트의 위치에 상대적인 경로를 사용합니다.|
    | -TemplateParametersFile | Azure 리소스 그룹 배포 프로젝트의 매개 변수 파일에 대한 경로. 유연성을 증대하려면 이 매개 변수에 절대 경로 대신 PowerShell 스크립트의 위치에 상대적인 경로를 사용합니다.|
    | -ArtifactStagingDirectory | 이 매개 변수는 프로젝트의 이진 파일을 복사해 올 폴더를 PowerShell 스크립트에 알려줍니다. 이 값은 PowerShell 스크립트에서 사용하는 기본값보다 우선합니다. VS Team Services 사용을 위해 이 값을 -ArtifactStagingDirectory $(Build.StagingDirectory)로 설정합니다. |

    스크립트 인수 예는 다음과 같습니다(읽기 쉽도록 줄 구분).

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    완료되면 **스크립트 인수** 상자가 다음과 유사하게 보입니다.

    ![][11]

1.	Azure PowerShell 빌드 단계에 모든 필요한 항목을 추가한 후 **큐** 빌드 버튼을 선택하여 프로젝트를 빌드합니다. **빌드** 화면에 PowerShell 스크립트의 출력이 표시됩니다.

## 다음 단계

Azure 리소스 관리자 및 Azure 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하세요.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0803_2016-->