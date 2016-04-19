<properties
	pageTitle="호스트 이름 및 ssl 인증서와 함께 MSDeploy를 사용하여 웹앱 배포"
	description="MSDeploy를 사용하고 사용자 지정 호스트 이름 및 SSL 인증서를 설정하는 웹앱을 배포하는 Azure 리소스 관리자 템플릿 사용"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="john.dehavilland"/>

# MSDeploy, 사용자 지정 호스트 이름 및 SSL 인증서를 사용하여 웹앱 배포

이 가이드에서는 Azure 웹앱에 대한 종단 간 배포 만들기, MSDeploy 활용과 사용자 지정 호스트 이름 및 SSL 인증서를 ARM 템플릿에 추가하는 작업에 대해 설명합니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하십시오.

###응용 프로그램 예제 만들기

ASP.NET 웹 응용 프로그램을 배포하겠습니다. 첫 번째 단계는 간단한 웹 응용 프로그램을 만드는 것입니다. 또는 기존 웹 응용 프로그램을 선택할 수 있습니다. 이 경우 이 단계를 건너뛸 수 있습니다.

Visual Studio 2015를 열고 파일 > 새 프로젝트를 선택합니다. 표시되는 대화 상자에서 웹 > ASP.NET 웹 응용 프로그램을 선택합니다. 템플릿 아래에서 웹을 선택하고 MVC 템플릿을 선택합니다. _인증 형식 변경_을 _인증 안 함_으로 선택합니다. 가능한 간단한 응용 프로그램 예제를 만들기 위한 것입니다.

이때 배포 프로세스의 일부로 사용하기 위해 기본 ASP.Net 웹앱을 준비해야 합니다.

###MSDeploy 패키지 만들기

다음 단계는 Azure에 웹앱을 배포하는 패키지를 만드는 것입니다. 이렇게 하려면 프로젝트를 저장한 후 명령줄에서 다음을 실행하세요.

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

그러면 PackageLocation 폴더에 압축된 패키지가 만들어집니다. 이제 응용 프로그램이 배포할 준비가 되었고 이 작업을 수행하기 위해 Azure 리소스 관리자 템플릿을 작성할 수 있습니다.

###ARM 템플릿 만들기
먼저 웹 응용 프로그램 및 호스팅 계획을 만드는 기본 ARM 템플릿을 사용하여 시작해 보겠습니다(매개 변수 및 변수는 간단하게 표시되지 않음).

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

다음으로 중첩 MSDeploy 리소스를 가져오도록 웹앱 리소스를 수정해야 합니다. 이렇게 하면 이전에 만든 패키지를 참조하고 Azure 리소스 관리자가 MSDeploy를 사용하여 Azure WebApp에 패키지를 배포하도록 할 수 있습니다. 다음은 중첩 MSDeploy 리소스를 사용하는 Microsoft.Web/sites 리소스입니다.

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

이제 MSDeploy 리소스는 다음과 같이 정의된 **packageUri** 속성을 사용합니다.

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

이 **packageUri**는 패키지 zip을 업로드할 저장소 계정을 가리키는 저장소 계정 uri를 사용합니다. Azure 리소스 관리자는 템플릿 배포 시 저장소 계정에서 로컬로 패키지를 가져오는 데 [공유 액세스 서명](../storage/storage-dotnet-shared-access-signature-part-1.md)을 활용합니다. 패키지를 업로드하고, 필요한 키를 만들고 템플릿에 매개 변수(*\_artifactsLocation* 및 *\_artifactsLocationSasToken*)로 전달하기 위해 Azure 관리 API를 호출하는 PowerShell 스크립트를 통해 이 프로세스를 자동화할 수 있습니다. 저장소 컨테이너에 업로드되는 패키지의 파일 이름 및 폴더에 대한 매개 변수를 정의해야 합니다.

다음으로 사용자 지정 도메인을 활용하도록 호스트 이름 바인딩을 설정하려면 다른 중첩된 리소스에 추가해야 합니다. 먼저 호스트 이름을 소유하고 소유한 Azure에서 확인하도록 설정해야 합니다. [Azure 앱 서비스에서 사용자 지정 도메인 이름 구성](web-sites-custom-domain-name.md)을 참조하세요. 이 작업을 완료했으면 Microsoft.Web/sites 리소스 섹션의 템플릿에 다음을 추가할 수 있습니다.

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

마지막으로 다른 최상위 수준 리소스, Microsoft.Web/certificates를 추가해야 합니다. 이 리소스는 SSL 인증서를 포함하며 웹앱 및 호스팅 계획과 동일한 수준에 존재합니다.

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

이 리소스를 설정하려면 유효한 SSL 인증서를 사용해야 합니다. 유효한 인증서가 있는 경우 base64 문자열로 pfx 바이트를 추출해야 합니다. 이를 추출하는 한 가지 옵션으로 다음 PowerShell 명령을 사용합니다.

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

ARM 배포 템플릿에 매개 변수로 이를 전달할 수 있습니다.

이제 ARM 템플릿이 준비되었습니다.

###템플릿 배포

마지막 단계는 이 모두를 전체 종단 간 배포로 종합하는 것입니다. 보다 쉬운 배포를 위해 템플릿에 필요한 모든 아티팩트를 업로드하는 데 도움이 되도록 Visual Studio에서 Azure 리소스 그룹 프로젝트를 만들 때 추가된 **Deploy-AzureResourceGroup.ps1** PowerShell 스크립트를 활용할 수 있습니다. 사용하려는 저장소 계정을 미리 만들어야 합니다. 이 예제의 경우 업로드할 package.zip에 대한 공유 저장소 계정을 만들었습니다. 스크립트는 저장소 계정에 패키지를 업로드하는 AzCopy를 활용합니다. 아티팩트 폴더 위치에 전달하면 스크립트는 저장소 컨테이너라는 해당 디렉터리 내의 모든 파일을 자동으로 업로드합니다. Deploy-AzureResourceGroup.ps1을 호출한 후 SSL 인증서로 사용자 지정 호스트 이름에 매핑할 SSL 바인딩을 업데이트해야 합니다.

다음 PowerShell은 Deploy-AzureResourceGroup.ps1을 호출하는 전체 배포를 보여 줍니다.

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

이제 응용 프로그램이 배포되었고 https://www.yourcustomdomain.com을 통해 탐색할 수 있어야 합니다.

<!---HONumber=AcomDC_0406_2016-->