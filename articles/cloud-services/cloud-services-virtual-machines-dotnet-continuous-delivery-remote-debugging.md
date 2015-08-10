<properties
	pageTitle="연속 배달을 통해 원격 디버깅 사용"
	description="연속 배달을 사용하여 Azure에 배포할 경우 원격 디버깅을 사용하도록 설정하는 방법에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter=".net"
	authors="kempb"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="kempb"/>
# 연속 배달을 사용하여 Azure에 게시할 경우 원격 디버깅 사용

다음 단계를 따라 [연속 배달](cloud-services-dotnet-continuous-delivery.md)을 사용하여 Azure에 게시할 경우 Azure에서 원격 디버깅을 사용할 수 있습니다.

항목 내용

[클라우드 서비스에 원격 디버깅 사용](#cloudservice)

[가상 컴퓨터에 원격 디버깅 사용](#virtualmachine)

## <a name="cloudservice"></a> 클라우드 서비스에 원격 디버깅 사용

1. 빌드 에이전트에서 [Azure에 대한 명령줄 빌드](http://msdn.microsoft.com/library/hh535755.aspx)에 간략히 설명된 Azure에 대한 초기 환경을 설정합니다.
2. 패키지에 원격 디버그 런타임(msvsmon.exe)이 필요하므로, [Visual Studio 2015 RC용 원격 도구](http://www.microsoft.com/download/details.aspx?id=46874)(또는 Visual Studio 2013을 사용할 경우 [Visual Studio 2013 업데이트 5 RC용 원격 도구](https://www.microsoft.com/ko-kr/download/details.aspx?id=46870))를 설치합니다. 또는 Visual Studio를 설치한 시스템에서 원격 디버그 이진을 복사할 수 있습니다.
3. [Azure용 서비스 인증서 만들기](http://msdn.microsoft.com/library/azure/gg432987.aspx)에 간략히 설명된 인증서를 만듭니다. .pfx 및 RDP 인증서 지문을 유지하고 대상 클라우드 서비스에 인증서를 업로드합니다.
4. MSBuild 명령줄에 다음 옵션을 사용하여 원격 디버그를 사용하도록 설정한 상태로 빌드 및 패키지화합니다. (꺾쇠 괄호가 항목의 경우 시스템 및 프로젝트 파일에 대한 실제 경로를 대체합니다.)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.6" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` Visual Studio용 원격 도구에서 msvsmon.exe를 포함하는 폴더의 경로입니다.

5. 위의 단계에서 생성한 패키지 및 .cscfg 파일을 사용하여 대상 클라우드 서비스에 게시합니다.
6. Visual Studio 및 Azure SDK for .NET이 설치된 컴퓨터로 인증서(.pfx 파일)를 가져옵니다.

## <a name="virtualmachine"></a>가상 컴퓨터에 원격 디버깅 사용

1. Azure 가상 컴퓨터를 만듭니다. [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial.md) 또는 [Visual Studio에서 Azure 가상 컴퓨터 만들기](http://msdn.microsoft.com/library/azure/dn569263.aspx)를 참조하세요.
2. [Azure 포털 페이지](http://go.microsoft.com/fwlink/p/?LinkID=269851)에서, 가상 컴퓨터 대시보드를 보고 가상 컴퓨터의 "RDP 인증서 지문"을 확인합니다. 이는 확장 구성에서 `ServerThumbprint` 값에 사용됩니다.
3. [Azure용 서비스 인증서 만들기](http://msdn.microsoft.com/library/azure/gg432987.aspx)에 간략히 설명된 대로 클라이언트 인증서를 만듭니다(.pfx 및 RDP 인증서 지문 유지).
4. Microsoft 다운로드 세터에서 [Azure Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)(버전 0.7.4 이상)을 설치합니다.
5. 다음 스크립트를 실행하여 RemoteDebug 확장을 사용합니다. 경로 및 개인 데이터를 사용자의 데이터(예: 구독 이름, 서비스 이름 및 지문)로 바꿉니다. (참고: 이 스크립트는 Visual Studio 2015 RC에서 구성되었습니다. Visual Studio 2013을 사용하는 경우, ReferenceName과 ExtensionName에 "RemoteDebugVS2013"을 사용합니다.)

	<pre>
Add-AzureAccount

Select-AzureSubscription "My Microsoft Subscription"

$vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

$endpoints = @(
,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
)

foreach($endpoint in $endpoints)
{
Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
}

$referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
$publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
$extensionName = "RemoteDebugVS2015"
$version = "1.*"
$publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

$vm | Set-AzureVMExtension `
-ReferenceName $referenceName `
-Publisher $publisher `
-ExtensionName $extensionName `
-Version $version `
-PublicConfiguration $publicConfiguration

foreach($extension in $vm.VM.ResourceExtensionReferences)
{
if(($extension.ReferenceName -eq $referenceName) `
-and ($extension.Publisher -eq $publisher) `
-and ($extension.Name -eq $extensionName) `
-and ($extension.Version -eq $version))
{
$extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
break
}
}

$vm | Update-AzureVM
</pre>

6. Visual Studio 및 Azure SDK for .NET 2.4가 설치된 컴퓨터로 인증서(.pfx)를 가져옵니다.
 

<!---HONumber=July15_HO5-->