---
title: "연속 배달을 통해 원격 디버깅 사용 | Microsoft Docs"
description: "연속 배달을 사용하여 Azure에 배포할 경우 원격 디버깅을 사용하도록 설정하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: .net
author: TomArcher
manager: douge
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d011223dbb1ad7b5752875e94a4822be1b6cbe6e
ms.lasthandoff: 03/25/2017


---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>연속 배달을 사용하여 Azure에 게시할 경우 원격 디버깅 사용
클라우드 서비스 또는 가상 컴퓨터의 경우 다음 단계를 따라 [연속 배달](cloud-services-dotnet-continuous-delivery.md) 을 사용하여 Azure에 게시하면 Azure에서 원격 디버깅을 사용할 수 있습니다.

## <a name="enabling-remote-debugging-for-cloud-services"></a>클라우드 서비스에 원격 디버깅 사용
1. 빌드 에이전트에서 [Azure에 대한 명령줄 빌드](http://msdn.microsoft.com/library/hh535755.aspx)에 간략히 설명된 Azure에 대한 초기 환경을 설정합니다.
2. 패키지에 원격 디버그 런타임(msvsmon.exe)이 필요하므로, **Visual Studio용 원격 도구**를 설치합니다.

    * [Visual Studio 2017용 원격 도구](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Visual Studio 2015용 원격 도구](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Visual Studio 2013용 원격 도구 업데이트 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    또는 Visual Studio를 설치한 시스템에서 원격 디버그 이진을 복사할 수 있습니다.

3. [Azure 클라우드 서비스에 대한 인증서 개요](cloud-services-certs-create.md)에서 간략히 설명된 인증서를 만듭니다. .pfx 및 RDP 인증서 지문을 유지하고 대상 클라우드 서비스에 인증서를 업로드합니다.
4. MSBuild 명령줄에 다음 옵션을 사용하여 원격 디버그를 사용하도록 설정한 상태로 빌드 및 패키지화합니다. (꺾쇠 괄호가 항목의 경우 시스템 및 프로젝트 파일에 대한 실제 경로를 대체합니다.)
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath` Visual Studio용 원격 도구에서 msvsmon.exe를 포함하는 폴더의 경로입니다.
    `RemoteDebuggerConnectorVersion`은 클라우드 서비스의 Azure SDK 버전입니다. 또한 Visual Studio와 함께 설치된 버전과 일치해야 합니다.
5. 위의 단계에서 생성한 패키지 및 .cscfg 파일을 사용하여 대상 클라우드 서비스에 게시합니다.
6. Visual Studio 및 Azure SDK for .NET이 설치된 컴퓨터로 인증서(.pfx 파일)를 가져옵니다. `CurrentUser\My` 인증서 저장소로 로 가져와야 합니다. 그렇지 않은 경우 Visual Studio의 디버거에 연결는 데 실패합니다.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>가상 컴퓨터에 원격 디버깅 사용
1. Azure 가상 컴퓨터를 만듭니다. [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Visual Studio에서 Azure Virtual Machine 만들기 및 관리](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.
2. [Azure 클래식 포털 페이지](http://go.microsoft.com/fwlink/p/?LinkID=269851)에서, 가상 컴퓨터 대시보드를 보고 가상 컴퓨터의 **RDP 인증서 지문**을 확인합니다. 이 값은 확장 구성에서 `ServerThumbprint` 값에 사용됩니다.
3. [Azure 클라우드 서비스에 대한 인증서 개요](cloud-services-certs-create.md) 에서 간략히 설명한 대로 클라이언트 인증서를 만듭니다(.pfx 및 RDP 인증서 지문 유지).
4. Azure PowerShell(0.7.4 이후 버전)을 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)에 간략히 설명한 대로 설치합니다.
5. 다음 스크립트를 실행하여 RemoteDebug 확장을 사용합니다. 경로 및 개인 데이터를 사용자의 데이터(예: 구독 이름, 서비스 이름 및 지문)로 바꿉니다.
   
   > [!NOTE]
   > 이 스크립트는 Visual Studio 2015에서 구성되었습니다. Visual Studio 2013 또는 Visual Studio 2017을 사용할 경우 아래 `$referenceName` 및 `$extensionName` 할당을 `RemoteDebugVS2013` 또는 `RemoteDebugVS2017`으로 수정합니다.

    ```powershell   
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

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

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
    ```

6. Visual Studio 및 Azure SDK for .NET 2.4가 설치된 컴퓨터로 인증서(.pfx)를 가져옵니다.


