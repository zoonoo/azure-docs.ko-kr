---
title: "Azure에 필요한 상태 구성 개요 | Microsoft Docs"
description: "PowerShell 필요한 상태 구성에 대한 Microsoft Azure 확장 처리기를 사용하는 개요입니다. 필수 구성 요소, 아키텍처, cmdlet...을 포함"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure 필요한 상태 구성 확장 처리기 소개

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 에이전트 및 연결된 확장은 Microsoft Azure 인프라 서비스의 일부입니다.
VM 확장은 VM 기능을 확장하고 다양한 VM 관리 작업을 단순화하는 소프트웨어 구성 요소입니다.

Desired State Configuration 확장의 주요 사용 사례는 VM을 [Azure Automation DSC 서비스](../../automation/automation-dsc-overview.md)에 부트스트랩하는 것으로, 이 서비스는 VM 구성을 지속적으로 관리하고 다른 작업 도구(예: Azure 모니터링)와 통합하는 등의 [이점](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service)을 제공합니다.

DSC 확장을 Azure Automation DSC 서비스에서 독립적으로 사용할 수도 있지만 이는 배포하는 동안 발생하는 단일 작업이며 VM 내에 로컬로 구성되는 것 이외에는 이 구성을 지속적으로 보고하거나 관리하는 경우가 없습니다.

이 문서에는 두 가지 시나리오에 대한 정보가 들어 있습니다. 하나는 Azure Automation 온보딩을 위한 DSC 확장이며 다른 하나는 Azure SDK를 사용하여 가상 머신에 구성을 할당하기 위한 도구로 DSC 확장을 사용하는 방법입니다.

## <a name="prerequisites"></a>필수 조건

- **로컬 컴퓨터** - Azure VM 확장과 상호 작용하려면 Azure Portal 또는 Azure PowerShell SDK를 사용해야 합니다.
- **게스트 에이전트** - DSC 구성으로 구성된 Azure VM은 WMF(Windows Management Framework) 4.0 이상을 지원하는 OS여야 합니다. 지원되는 OS 버전의 전체 목록은 [DSC 확장 버전 기록](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/) 페이지에 있습니다.

## <a name="terms-and-concepts"></a>용어 및 개념

이 가이드에서는 다음과 같은 개념에 익숙합니다.

- **구성** - DSC 구성 문서
- **노드** - DSC 구성에 대한 대상. 이 문서에서 "노드"는 Azure VM을 나타냅니다.
- **구성 데이터** - 구성에 대한 환경 데이터를 포함하는 .psd1 파일입니다.

## <a name="architectural-overview"></a>아키텍처 개요

Azure DSC 확장은 Azure VM 에이전트 프레임워크를 사용하여 Azure VM에서 실행되는 DSC 구성을 제공하고 적용하며 보고합니다.
DSC 확장은 구성 문서 및 매개 변수 집합을 허용합니다.
파일이 제공되지 않는 경우에는 [기본 구성 스크립트](###default-configuration-script)가 [로컬 구성 관리자](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)에서 메타데이터 설정에만 사용되는 확장에 포함되어 있습니다.

확장이 처음으로 호출되면 다음 논리를 사용하여 WMF(Windows Management Framework)의 버전을 설치합니다.

1. Azure VM OS가 Windows Server 2016이면 아무 작업도 수행되지 않습니다. Windows Server 2016에는 이미 최신 버전의 PowerShell이 설치되어 있습니다.
2. `wmfVersion` 속성을 지정하면 VM의 OS와 호환되지 않는 경우가 아닌 경우 해당 버전의 WMF가 설치됩니다.
3. `wmfVersion` 속성을 지정하면 WMF의 적용 가능한 최신 버전이 설치됩니다.

WMF를 설치하려면 다시 부팅해야 합니다.
다시 부팅한 후에 확장은 `modulesUrl` 속성(제공된 경우)에 지정된 .zip 파일을 다운로드합니다.
이 위치가 Azure Blob 저장소인 경우 SAS 토큰은 `sasToken` 속성에 지정되어 파일에 액세스할 수 있습니다.
.zip을 다운로드하고 압축을 푼 후에 `configurationFunction`에 정의된 구성 함수를 실행하여 MOF 파일을 생성합니다.
그런 다음 확장은 생성된 MOF 파일을 사용하여 `Start-DscConfiguration -Force`를 실행합니다.
확장은 출력을 캡처하고 Azure 상태 채널에 작성합니다.

### <a name="default-configuration-script"></a>기본 구성 스크립트

Azure DSC 확장에는 Azure Automation DSC 서비스에 VM을 온보딩할 때 사용할 수 있도록 기본 구성 스크립트가 포함되어 있습니다.
스크립트 매개 변수는 [로컬 구성 관리자](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)의 구성 가능한 속성과 정렬됩니다.
스크립트 매개 변수는 [문서화](extensions-dsc-template.md##default-configuration-script)되어 있으며 전체 스크립트는 [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)에서 사용할 수 있습니다.

## <a name="dsc-extension-in-arm-templates"></a>ARM 템플릿의 DSC 확장

ARM(Azure Resource Manager) 배포 템플릿은 대부분의 시나리오에서 DSC 확장을 사용하기 위해 예상된 방법입니다.
ARM 배포 템플릿에 DSC 확장을 포함하는 자세한 내용 및 예제는 전용 설명서 페이지 [Desired State Configuration 확장과 Azure Resource Manager 템플릿](extensions-dsc-template.md)을 참조하세요.

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 확장 PowerShell Cmdlet

DSC 확장 관리용 PowerShell cmdlet은 대화형 문제 해결 및 정보 수집 시나리오에 가장 적합합니다.
Cmdlet은 DSC 확장 배포를 패키징하고 게시하며 모니터링하는 데 사용할 수 있습니다.
DSC 확장용 cmdlet은 아직 [기본 구성 스크립트](###default-configuration-script)를 사용할 수 있도록 업데이트되지 않았습니다.

`Publish-AzureRMVMDscConfiguration` 은 구성 파일을 받고 종속 DSC 리소스를 검색하며 구성 및 구성을 적용하는 데 필요한 DSC 리소스를 포함하는 .zip 파일을 만듭니다.
또는 `-ConfigurationArchivePath` 매개 변수를 사용하여 패키지를 로컬로 만들 수도 있습니다.
그렇지 않으면 Azure Blob 저장소에 .zip 파일을 게시하고 SAS 토큰으로 보호합니다.

이 cmdlet에서 만든 .zip 파일은 보관 폴더의 루트에서 .ps1 구성 스크립트를 포함합니다.
리소스에는 보관 폴더에 위치한 모듈 폴더가 있습니다.

`Set-AzureRMVMDscExtension`은 VM 구성 개체에 PowerShell DSC 확장에서 필요한 설정을 삽입합니다.

`Get-AzureRMVMDscExtension`은 특정 VM의 DSC 확장 상태를 검색합니다.

`Get-AzureRMVMDscExtensionStatus` 는 DSC 확장 처리기에 의해 적용되는 DSC 구성의 상태를 검색합니다.
이 작업을 단일 VM 또는 VM 그룹에 대해 수행할 수 있습니다.

`Remove-AzureRMVMDscExtension` 은 지정된 가상 머신에서 확장 처리기를 제거합니다.
이 cmdlet은 구성을 제거하거나 WMF를 제거하거나 가상 머신에 적용된 설정을 변경하지 **않습니다** .
확장 처리기를 제거합니다. 

AzureRM DSC 확장 cmdlet에 대한 중요 정보:

- Azure Resource Manager cmdlets는 동기입니다.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version, Location은 모두 필수 매개 변수입니다.
- ArchiveResourceGroupName은 선택적 매개 변수입니다. 저장소 계정이 가상 머신을 만들 위치가 아닌 다른 리소스 그룹에 속해 있는 경우 이 매개 변수를 지정할 수 있습니다.
- AutoUpdate 스위치가 확장 처리기를 사용할 수 있는 경우 최신 버전으로 자동으로 업데이트할 수 있습니다. 이 매개 변수를 사용하면 새 버전의 WMF가 릴리스될 때 VM이 다시 시작될 가능성이 있습니다.

### <a name="getting-started-with-cmdlets"></a>Cmdlet으로 시작

Azure DSC 확장은 배포 중에 Azure VM을 구성하기 위해 DSC 구성 문서를 직접 사용할 수 있지만 Azure Automation에 노드가 등록되지 않으므로 중앙에서 노드를 관리할 수 **없습니다*.

구성의 간단한 예제가 뒤따릅니다.
"IisInstall.ps1"으로 다음과 같이 로컬로 저장합니다.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

다음 단계는 지정된 VM에서 IisInstall.ps1 스크립트를 배치하고 구성을 실행하며 상태를 다시 보고합니다.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Azure Portal 기능

VM으로 이동합니다. 설정 -> 일반에서 "확장"을 클릭합니다.
새 창을 만듭니다.
"추가"를 클릭하고 PowerShell DSC를 선택합니다.

포털에 입력해야 합니다.
**구성 모듈 또는 스크립트**: 이 필드는 필수입니다. [기본 구성 스크립트](###default-configuration-script)의 양식이 아직 업데이트되지 않았습니다.
.zip 파일 내의 모듈 폴더에서 루트 및 모든 종속 리소스 루트에 구성 스크립트를 포함하는 .ps1 파일 또는 .ps1 구성 스크립트를 포함하는 .zip 파일이 필요합니다.
Azure PowerShell SDK에 포함된 `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet으로 만들 수 있습니다.
.zip 파일은 SAS 토큰에 의해 보호된 사용자 Blob 저장소에 업로드됩니다.

**구성 데이터 PSD1 파일**: 이 필드는 선택적 필드입니다.
구성에 .psd1의 구성 데이터 파일이 필요한 경우 이 필드를 사용하여 선택하고 사용자 Blob 저장소에 업로드합니다. 이 저장소는 SAS 토큰으로 보호됩니다.

**구성의 모듈 정규화된 이름**: .ps1 파일에는 여러 개의 구성 함수가 있을 수 있습니다.
예를 들어 '\'에 이어서 구성 .ps1 스크립트의 이름 및 구성 함수의 이름을 입력합니다.
예를 들어 .ps1 스크립트 이름이 "configuration.ps1"이고 구성이 "IisInstall"이면 `configuration.ps1\IisInstall`

**구성 인수**: 구성 함수가 인수를 사용하는 경우 `argumentName1=value1,argumentName2=value2` 형식으로 여기에 입력합니다.
PowerShell cmdlet 또는 Resource Manager 템플릿을 통해 구성 인수를 수락하는 방법과는 다른 서식입니다.

## <a name="logging"></a>로깅
로그는 다음에 배치됩니다.

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>다음 단계
PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터를 방문하세요](https://msdn.microsoft.com/powershell/dsc/overview). 

[DSC 확장에 대한 Azure Resource Manager 템플릿](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 검토합니다. 

PowerShell DSC로 관리할 수 있는 추가 기능을 찾으려면 추가 DSC 리소스는 [PowerShell 갤러리에서 찾아보세요](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) .

중요한 매개 변수를 구성에 전달하는 세부 정보는 [DSC 확장 처리기로 안전하게 자격 증명 관리](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
