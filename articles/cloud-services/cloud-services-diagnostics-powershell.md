<properties 
	pageTitle="PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용 | Microsoft Azure" 
	description="PowerShell을 사용하여 클라우드 서비스에 진단을 사용하도록 설정하는 방법을 알아봅니다." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용

Azure 진단 확장을 사용하여 클라우드 서비스로부터 응용 프로그램 로그, 성능 카운터 등과 같은 진단 데이터를 수집할 수 있습니다. 이 문서는 PowerShell을 사용하여 클라우드 서비스에 대해 Azure 진단 확장을 사용하도록 설정하는 방법을 설명합니다. 이 문서에 요구되는 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

## 클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정

이 방식은 진단 확장을 사용하도록 설정할 수 있는 연속적인 통합 유형의 시나리오에 적합합니다. [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) cmdlet에 *ExtensionConfiguration* 매개 변수를 전달하여 클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정할 수 있습니다. *ExtensionConfiguration* 매개 변수는 [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) cmdlet을 사용하여 만들 수 있는 진단 구성 배열을 사용합니다.

다음 예제는 각각 진단 구성이 다른 WebRole 및 WorkerRole을 사용하여 클라우드 서비스에 대해 진단을 사용하도록 설정하는 방법을 보여줍니다.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## 기존 클라우드 서비스에 진단 확장을 사용하도록 설정

[Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) cmdlet을 사용하여 이미 실행 중인 클라우드 서비스에 진단을 사용하도록 설정할 수 있습니다.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## 현재 진단 확장 구성 가져오기
[Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) cmdlet을 사용하여 클라우드 서비스에 대한 현재 진단 구성을 가져올 수 있습니다.
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## 진단 확장 제거
클라우드 서비스에서 진단을 해제하려면 [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) cmdlet을 사용합니다.

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

*Role* 매개 변수 없이 *Set-AzureServiceDiagnosticsExtension* 또는 *New-AzureServiceDiagnosticsExtensionConfig*를 사용하여 진단 확장을 사용하도록 설정한 경우에는 *Role* 매개 변수 없이 *Remove-AzureServiceDiagnosticsExtension*을 사용하여 확장을 제거할 수 있습니다. *Role* 매개 변수가 확장을 사용하도록 설정할 때 사용되었으면, 확장을 제거할 때도 사용되어야 합니다.

각각의 개별 역할에서 진단 확장을 제거하려면:

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## 다음 단계

- 문제 해결을 위한 Azure 진단 및 기타 기법 사용에 대한 추가 지침은 [Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 사용](cloud-services-dotnet-diagnostics.md)을 참조하세요.
- [진단 구성 스키마](https://msdn.microsoft.com/library/azure/dn782207.aspx)는 진단 확장에 대한 다양한 XML 구성 옵션을 설명합니다.
- 가상 컴퓨터에 대해 진단 확장을 사용하도록 설정하는 방법을 알아보려면 [Azure 리소스 관리자 템플릿을 사용한 모니터링 및 진단으로 Windows 가상 컴퓨터 만들기(영문)](virtual-machines-extensions-diagnostics-windows-template.md)를 참조하세요.  

<!---HONumber=Nov15_HO4-->