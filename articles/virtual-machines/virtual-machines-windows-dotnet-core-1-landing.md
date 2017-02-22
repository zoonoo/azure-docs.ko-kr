---
title: "Azure Windows 가상 컴퓨터 DotNet Core 자습서 1 | Microsoft Docs"
description: "Azure 가상 컴퓨터 DotNet Core 자습서"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 14d5f250-1f76-49d4-898f-07b58fd39e7c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 64ad540536dcf3f4d4a73f49d99fb6142b33ea9c
ms.openlocfilehash: 31fd217e46bbc79bac9e09045677edbc12bcf6c2


---
# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Azure 가상 컴퓨터에 대한 응용 프로그램 배포 자동화
네 부분으로 구성된 이 시리즈에서는 Azure Resource Manage 템플릿을 사용하여 Azure 리소스 및 응용 프로그램을 배포 및 구성하는 과정을 자세히 설명합니다. 이 시리즈에서는 샘플 템플릿이 배포되고 배포 템플릿이 검사됩니다. 이 시리즈의 목적은 Azure 리소스 간 관계를 익히고 완전히 통합된 Azure Resource Manager 템플릿을 배포하는 과정을 경험해보는 것입니다. 이 문서에서는 Azure Resource Manager에 대한 기본적인 지식이 있다고 가정하므로 이 자습서를 시작하기 전에 기본적인 Azure Resource Manager 개념을 숙지하시기 바랍니다.

## <a name="music-store-application"></a>Music Store 응용 프로그램
이 시리즈에 사용된 샘플은 Music Store 쇼핑 환경을 시뮬레이트하는 .Net Core 응용 프로그램입니다. 이 응용 프로그램은 Linux 또는 Windows 가상 시스템에 배포할 수 있으며 둘 다에 대해 샘플 배포가 만들어져 있습니다. 이 응용 프로그램에는 웹 응용 프로그램 및 SQL Database가 포함되어 있습니다. 이 시리즈의 문서를 읽기 전에 이 페이지에 있는 배포 단추를 사용하여 응용 프로그램을 배포합니다. 완전히 배포하면 응용 프로그램/Azure 아키텍처가 다음 다이어그램과 같이 표시됩니다. 

Music Store Resource Manager 템플릿은 [Music Store Linux 템플릿](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)에서 확인할 수 있습니다.

![Music Store 응용 프로그램](./media/virtual-machines-windows-dotnet-core/music-store.png)

관련된 템플릿 JSON을 포함하여 이러한 각 구성 요소는 다음 네 가지 문서에서 검토됩니다.

* [**응용 프로그램 아키텍처**](virtual-machines-windows-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) – 웹 사이트 및 데이터베이스와 같은 응용 프로그램 구성 요소는 가상 컴퓨터 및 Azure SQL Database와 같은 Azure 컴퓨터 리소스에 호스트되어야 합니다. 이 문서에서는 계산 요구를 Azure 리소스에 매핑하고 이러한 리소스를 Azure Resource Manager 템플릿을 통해 배포하는 과정을 안내합니다. 
* [**액세스 및 보안**](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) – Azure에서 응용 프로그램을 호스트할 때 응용 프로그램에 액세스되는 방법과 응용 프로그램 구성 요소가 서로 액세스하는 방법을 고려해야 합니다. 이 문서에서는 응용 프로그램에 대한 인터넷 액세스와 응용 프로그램 구성 요소 간 액세스를 제공하고 보안을 유지하는 방법을 자세히 설명합니다.
* [**가용성 및 크기 조정**](virtual-machines-windows-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) – 가용성 및 크기 조정은 인프라 가동 중지 시간 동안 응용 프로그램이 실행 상태를 유지하는 능력과 응용 프로그램 요구에 맞게 계산 리소스의 규모를 조정하는 기능을 나타냅니다. 이 문서에서는 부하 분산되고 가용성이 높은 응용 프로그램을 배포하는 데 필요한 구성 요소에 대해 자세히 설명합니다.
* [**응용 프로그램 배포**](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) -Azure 가상 컴퓨터에 응용 프로그램을 배포할 때 응용 프로그램 이진 파일이 가상 컴퓨터에 설치되는 방법을 고려해야 합니다. 이 문서에서는 Azure 가상 컴퓨터 사용자 지정 스크립트 확장을 사용하여 응용 프로그램 설치를 자동화하는 방법을 자세히 설명합니다.

Azure Resource Manager 템플릿을 개발할 때의 목표는 Azure 인프라의 배포와 이 Azure 인프라에 호스트되는 응용 프로그램의 설치 및 구성을 자동화하는 것입니다. 이러한 문서를 따라 작업하면서 이러한 작업의 예제를 볼 수 있습니다.

## <a name="deploy-the-music-store-application"></a>Music Store 응용 프로그램 배포
이 단추를 사용하여 Music Store 응용 프로그램을 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Azure Resource Manager 템플릿에는 다음 매개 변수 값을 지정해야 합니다.

| 매개 변수 이름 | 설명 |
| --- | --- |
| ADMINUSERNAME |가상 컴퓨터와 Azure SQL Database에 사용되는 관리자 사용자 이름입니다. |
| ADMINPASSWORD |Azure Virtual Machine과 SQL Database에 사용되는 암호입니다. |
| NUMBEROFINSTANCES |만들 가상 컴퓨터의 수입니다. 이러한 각 가상 컴퓨터는 Music Store 웹 응용 프로그램을 호스트하며 모든 트래픽의 부하가 분산됩니다. |
| PUBLICIPADDRESSDNSNAME |공용 IP 주소와 연결된 전역 고유 DNS 이름입니다. |

템플릿 배포가 완료되면 인터넷 브라우저를 사용하여 이 공용 IP 주소로 이동됩니다. .NET Core Music 사이트가 나타납니다.

## <a name="next-steps"></a>다음 단계
<hr>

[1단계 - Azure Resource Manager 템플릿을 사용하는 응용 프로그램 아키텍처](virtual-machines-windows-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[2단계 - Azure Resource Manager 템플릿의 액세스 및 보안](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[3단계 - Azure Resource Manager 템플릿의 가용성 및 크기 조정](virtual-machines-windows-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[4단계 - Azure Resource Manager 템플릿을 사용한 응용 프로그램 배포](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO4-->


