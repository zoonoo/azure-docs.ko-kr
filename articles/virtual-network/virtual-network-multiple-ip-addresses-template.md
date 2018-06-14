---
title: Azure Virtual Machines의 여러 IP 주소 - 템플릿 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다.
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31435184"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 가상 머신에 여러 IP 주소 할당

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

이 문서는 Azure Resource Manager 템플릿을 사용하여 Resource Manager 배포 모델을 통해 VM(가상 머신)를 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 VM을 배포하는 경우 동일한 NIC에 여러 개의 공용 및 개인 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>템플릿 설명

템플릿을 배포하면 서로 다른 구성 값을 사용하여 Azure 리소스를 빠르고 일관되게 만들 수 있습니다. Azure Resource Manager 템플릿에 익숙하지 않은 경우 [Resource Manager 템플릿 연습](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 읽어보세요. 이 문서에서는 [여러 IP 주소를 사용하여 VM 배포](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) 템플릿이 사용됩니다.

<a name="resources"></a>템플릿을 배포하면 다음 리소스가 만들어집니다.

|리소스|Name|설명|
|---|---|---|
|Linux|*myNic1*|이 문서의 시나리오 섹션에 설명된 3개의 IP 구성이 생성된 후 이 NIC에 할당됩니다.|
|공용 IP 주소 리소스|2개 *myPublicIP* 및 *myPublicIP2*가 만들어집니다.|이러한 리소스는 고정 공용 IP 주소에 할당되고 이 시나리오에 설명된 *IPConfig-1* 및 *IPConfig-2* IP 구성에 할당됩니다.|
|VM|*myVM1*|표준 DS3 VM|
|가상 네트워크|*myVNet1*|*mySubnet*라는 단일 서브넷이 있는 가상 네트워크|
|Storage 계정|배포마다 고유|저장소 계정|

<a name="parameters"></a>템플릿을 배포할 때 다음 매개 변수 값을 지정해야 합니다.

|Name|설명|
|---|---|
|adminUsername|관리자 사용자 이름. 사용자 이름은 [Azure 사용자 이름 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 준수해야 합니다.|
|adminPassword|관리자 암호. 이 암호는 [Azure 암호 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 준수해야 합니다.|
|dnsLabelPrefix|PublicIPAddressName1에 대한 DNS 이름. DNS 이름은 VM에 할당된 공용 IP 주소 중 하나로 확인됩니다. 이 이름은 VM을 만든 Azure 하위 지역(위치) 내에서 고유해야 합니다.|
|dnsLabelPrefix1|PublicIPAddressName2에 대한 DNS 이름. DNS 이름은 VM에 할당된 공용 IP 주소 중 하나로 확인됩니다. 이 이름은 VM을 만든 Azure 하위 지역(위치) 내에서 고유해야 합니다.|
|OSVersion|VM의 Windows/Linux 버전. 운영 체제는 선택한 특정 Windows/Linux 버전의 완전 패치된 이미지입니다.|
|imagePublisher|선택된 VM에 대한 Windows/Linux 이미지 게시자|
|imageOffer|선택된 VM에 대한 Windows/Linux 이미지.|

템플릿에서 배포된 각 리소스는 몇 가지 기본 설정으로 구성됩니다. 다음 방법 중 하나를 통해 이러한 설정을 볼 수 있습니다.

- **GitHub에서 템플릿 보기:** 템플릿에 친숙한 경우 [템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) 내에서 설정을 볼 수 있습니다.
- **배포 후 설정 보기:** 템플릿에 익숙하지 않은 경우 다음 섹션 중 하나의 단계를 사용하여 템플릿을 배포하고 배포 후에 설정을 볼 수 있습니다.

Azure Portal, PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 템플릿을 배포할 수 있습니다. 모든 방법은 동일한 결과를 생성합니다. 템플릿을 배포하려면 다음 섹션 중 하나의 단계를 완료합니다.

## <a name="deploy-using-the-azure-portal"></a>Azure Portal을 사용하여 배포

Azure Portal을 사용하여 템플릿을 배포하려면 다음 단계를 완료합니다.

1. 필요한 경우 템플릿을 수정합니다. 템플릿은 이 문서의 [리소스](#resources) 섹션에 나열된 리소스 및 설정을 배포합니다. 템플릿에 대한 자세한 내용 및 작성 방법에 대해서는 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.
2. 다음 방법 중 하나로 템플릿을 배포합니다.
    - **포털에서 템플릿 선택:** [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) 문서의 단계를 완료합니다. *101-vm-multiple-ipconfig*이라는 기존 템플릿을 선택합니다.
    - **직접:** 다음 단추를 클릭하여 포털에서 직접 템플릿을 엽니다.<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

선택한 방법과 상관 없이, 이 문서 앞부분에 나열된 [매개 변수](#parameters) 값을 제공해야 합니다. VM이 배포된 후에 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 단계를 완료하여 VM에 연결하고 배포된 운영 체제에 개인 IP 주소를 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마십시오.

## <a name="deploy-using-powershell"></a>PowerShell을 사용하여 배포

PowerShell을 사용하여 템플릿을 배포하려면 다음 단계를 완료합니다.

1. [PowerShell을 사용하여 템플릿 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 문서의 단계를 완료하여 템플릿을 배포합니다. 이 문서에서는 템플릿 배포를 위한 여러 옵션을 설명합니다. `-TemplateUri parameter`를 사용하여 배포하도록 선택한 경우 이 템플릿의 URI는 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*입니다. `-TemplateFile` 매개 변수를 사용하여 배포하도록 선택한 경우 GitHub의 [템플릿 파일](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) 내용을 컴퓨터의 새 파일에 복사합니다. 필요한 경우 템플릿 내용을 수정합니다. 템플릿은 이 문서의 [리소스](#resources) 섹션에 나열된 리소스 및 설정을 배포합니다. 템플릿에 대한 자세한 내용 및 작성 방법에 대해서는 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md) 문서를 참조하세요.

    템플릿 배포를 위해 선택한 옵션에 관계 없이, 이 문서의 [매개 변수](#parameters) 섹션에 나열된 매개 변수 값을 제공해야 합니다. 매개 변수 파일을 사용하여 매개 변수를 제공하도록 선택한 경우 GitHub의 [매개 변수 파일](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)을 컴퓨터의 새 파일에 복사합니다. 파일의 값을 수정합니다. 만든 파일을 `-TemplateParameterFile` 매개 변수의 값으로 사용합니다.

    OSVersion, ImagePublisher 및 imageOffer 매개 변수의 유효한 값을 확인하려면 [Windows VM 이미지 탐색 및 선택](../virtual-machines/windows/cli-ps-findimage.md) 문서의 단계를 완료합니다.

    >[!TIP]
    >dnslabelprefix를 사용할 수 있는지 확실하지 않은 경우 `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` 명령을 입력하여 확인합니다. 사용할 수 있으면 이 명령은 `True`를 반환합니다.

2. VM이 배포된 후에 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 단계를 완료하여 VM에 연결하고 배포된 운영 체제에 개인 IP 주소를 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

## <a name="deploy-using-the-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI 1.0을 사용하여 템플릿을 배포하려면 다음 단계를 완료합니다.

1. [Azure CLI를 사용하여 템플릿 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 문서의 단계를 완료하여 템플릿을 배포합니다. 이 문서에서는 템플릿 배포를 위한 여러 옵션을 설명합니다. `--template-uri`(-f)를 사용하여 배포하도록 선택한 경우 이 템플릿의 URI는 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*입니다. `--template-file`(-f) 매개 변수를 사용하여 배포하도록 선택한 경우 GitHub의 [템플릿 파일](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) 내용을 컴퓨터의 새 파일에 복사합니다. 필요한 경우 템플릿 내용을 수정합니다. 템플릿은 이 문서의 [리소스](#resources) 섹션에 나열된 리소스 및 설정을 배포합니다. 템플릿에 대한 자세한 내용 및 작성 방법에 대해서는 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md) 문서를 참조하세요.

    템플릿 배포를 위해 선택한 옵션에 관계 없이, 이 문서의 [매개 변수](#parameters) 섹션에 나열된 매개 변수 값을 제공해야 합니다. 매개 변수 파일을 사용하여 매개 변수를 제공하도록 선택한 경우 GitHub의 [매개 변수 파일](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)을 컴퓨터의 새 파일에 복사합니다. 파일의 값을 수정합니다. 만든 파일을 `--parameters-file`(-e) 매개 변수의 값으로 사용합니다.

    OSVersion, ImagePublisher 및 imageOffer 매개 변수의 유효한 값을 확인하려면 [Windows VM 이미지 탐색 및 선택](../virtual-machines/windows/cli-ps-findimage.md) 문서의 단계를 완료합니다.

2. VM이 배포된 후에 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 단계를 완료하여 VM에 연결하고 배포된 운영 체제에 개인 IP 주소를 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마십시오.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
