---
title: "가상 컴퓨터의 여러 IP 주소 - Portal | Microsoft Docs"
description: "Azure Portal | Resource Manager를 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 컴퓨터에 여러 IP 주소 할당

> [!div class="op_single_selector"]
> * [포털](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Azure VM(가상 컴퓨터)에는 하나 이상의 네트워크 인터페이스(NIC)가 연결되어 있습니다. 모든 NIC에는 하나 이상의 정적 또는 동적 공용 및 개인 IP 주소가 할당되어 있을 수 있습니다. VM에 여러 IP 주소를 할당하면 다음을 수행할 수 있습니다.

* 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
* 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
* NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IP 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../load-balancer/load-balancer-multiple-ip.md) 문서를 참조하세요.

VM에 연결된 모든 NIC에는 하나 이상의 IP 구성이 연결되어 있습니다. 각 구성에는 하나의 정적 또는 동적 개인 IP 주소가 할당됩니다. 각 구성에는 하나의 공용 IP 주소 리소스가 연결되어 있을 수도 있습니다. 공용 IP 주소 리소스에는 동적 또는 정적 IP 주소가 할당되어 있습니다. Azure의 IP 주소에 대한 자세한 내용을 알아보려면 [Azure의 IP 주소](virtual-network-ip-addresses-overview-arm.md)를 확인하세요.

이 문서에서는 Azure Portal을 사용하여 Azure Resource Manager 배포 모델을 통해 만든 VM에 여러 IP 주소를 할당하는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>시나리오
단일 NIC가 있는 VM을 만들고 가상 네트워크에 연결합니다. VM은 세 개의 *개인* IP 주소와 두 개의 *공용* IP 주소가 필요합니다. IP 주소는 다음 IP 구성에 할당됩니다.

* **IPConfig-1:** *동적* 개인 IP 주소(기본값)와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-2:** *정적* 개인 IP 주소와 *정적* 공용 IP 주소를 할당합니다.
* **IPConfig-3:** *동적* 개인 IP 주소를 할당하고 공용 IP 주소를 할당하지 않습니다.
  
    ![여러 IP 주소](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

NIC를 만들 때 IP 구성이 NIC에 연결되고 VM을 만들 때 NIC가 VM에 연결됩니다. 시나리오에 사용되는 IP 주소 유형은 예시입니다. 필요한 모든 IP 주소 및 할당 유형을 지정할 수 있습니다.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

여러 IP 주소를 사용하여 VM을 만들려면 PowerShell 또는 Azure CLI를 사용하여 만들어야 합니다. 방법을 보려면 이 문서 맨 위에 있는 PowerShell 또는 CLI 옵션을 클릭합니다. [Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 또는 [Linux VM 만들기](../virtual-machines/virtual-machines-linux-quick-create-portal.md) 문서의 단계에 따라 포털을 사용하여 단일 정적 개인 IP 주소 및 (선택적으로) 단일 공용 IP 주소를 사용하여 VM을 만들 수 있습니다. VM을 만든 후에 이 문서의 [VM에 IP 주소 추가](#add) 섹션에 나오는 단계에 따라 포털을 사용하여 IP 주소 형식을 변경하고 추가 IP 주소를 추가할 수 있습니다.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 NIC에 추가할 수 있습니다. 다음 섹션의 예제는 이 문서의 [시나리오](#Scenario)에서 설명한 3개의 IP로 구성된 VM이 이미 있다는 가정 하에 진행하되 필수 사항은 아닙니다.

> [!NOTE]
> 이 문서를 통해 모든 IP 구성을 단일 NIC에 할당하면 여러 IP 구성도 VM의 모든 NIC에 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 [여러 NIC를 사용하여 VM 만들기](virtual-network-deploy-multinic-arm-ps.md) 문서를 참조하세요.

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>핵심 단계

1. 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)로 보내어 미리 보기를 등록합니다. 다음 작업이 끝나기 전까지 나머지 단계를 완료하려 하지 마세요.
    - 미리 보기에 적용되었음을 알리는 전자 메일을 받을 때까지
    - 수신 메일의 지침을 따르기 전에
2. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
3. 포털에서 **더 많은 서비스**를 클릭하고 필터 상자에 *가상 컴퓨터*를 입력하고 **가상 컴퓨터**를 클릭합니다.
4. **가상 컴퓨터** 블레이드에서 IP 주소를 추가하려는 VM을 클릭합니다. 나타나는 가상 컴퓨터 블레이드에서 **네트워크 인터페이스**를 클릭한 다음 IP 주소를 추가할 네트워크 인터페이스를 선택합니다. 다음 그림의 예제에서는 *myVM*이라는 VM에서 *myNIC*라는 NIC가 선택됩니다.

    ![네트워크 인터페이스](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. 선택한 NIC에 대해 표시되는 블레이드에서 다음 그림과 같이 **IP 구성**을 클릭합니다.

    ![IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

추가할 IP 주소 형식에 따라, 이어지는 섹션 중 하나의 단계를 완료합니다.

### <a name="add-a-private-ip-address"></a>**개인 IP 주소 추가**

새 개인 IP 주소를 추가하려면 다음 단계를 완료합니다.

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. **추가**를 클릭합니다. 다음 그림과 같이 나타나는 **IP 구성 추가** 블레이드에서 *고정* 개인 IP 주소가 *10.0.0.7*인 *IPConfig 4*라는 IP 구성을 만든 후 **확인**을 클릭합니다.

    ![개인 IP 추가](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > 고정 IP 주소를 추가할 경우 NIC가 연결된 서브넷의 사용하지 않은 유효한 주소를 지정해야 합니다. 선택한 주소를 사용할 수 없는 경우 포털에 IP 주소로 X가 표시되며 다른 주소를 선택해야 합니다.

    개인 IP 주소 **할당 방법**으로 *동적*을 원할 경우 이 옵션을 선택합니다. IP 주소를 지정할 필요는 없습니다.
3. 확인을 클릭하면 다음 그림과 같이 블레이드가 닫히고 새 IP 구성이 나열됩니다.

    ![IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    **확인**을 클릭하여 **IP 구성 추가** 블레이드를 닫습니다.
4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다.

### <a name="add-a-public-ip-address"></a>공용 IP 주소 추가

공용 IP 주소 리소스를 새 IP 구성 또는 기존 IP 구성에 연결하면 공용 IP 주소가 추가됩니다.

> [!NOTE]
> 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip"></a>공용 IP 주소 리소스 만들기

공용 IP 주소는 공용 IP 주소 리소스에 대한 한 가지 설정입니다. IP 구성에 연결하려는 IP 구성에 현재 연결되어 있지 않은 공용 IP 주소 리소스가 있는 경우 다음 단계를 건너뛰고 필요에 따라 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다. 사용 가능한 공용 IP 주소 리소스가 없는 경우 다음 단계를 완료하여 리소스를 하나 만듭니다.

1. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
3. 포털에서 **새로 만들기** > **네트워킹** > **공용 IP 주소**를 클릭합니다.
4. 다음 그림에 표시된 것처럼 나타나는 **공용 IP 주소 만들기** 블레이드에서 **이름**을 입력하고 **IP 주소 할당** 형식, **구독**, **리소스 그룹** 및 **위치**를 선택하고 **만들기**를 클릭합니다.

    ![공용 IP 주소 리소스 만들기](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 공용 IP 주소 리소스를 IP 구성에 연결하려면 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>새 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. **추가**를 클릭합니다. 나타나는 **IP 구성 추가** 블레이드에서 *IPConfig-4*라는 IP 구성을 만듭니다. 다음 그림과 같이 나타나는 **공용 IP 주소 선택** 블레이드에서 **공용 IP 주소**를 사용하도록 설정하고 사용 가능한 기존 공용 IP 주소 리소스를 선택합니다.

    ![새 IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    공용 IP 주소 리소스를 선택한 후 **확인**을 클릭하여 블레이드를 닫습니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다. 

3. 다음 그림에 나와 있는 것처럼 새 IP 구성을 검토합니다.

    ![IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > 개인 IP 주소가 명시적으로 할당되지 않았더라도 모든 IP 구성에는 개인 IP 주소가 있어야 하므로 IP 구성에 자동으로 주소가 하나 할당되어 있습니다.
    >

4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>기존 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. 공용 IP 주소 리소스를 추가할 IP 구성을 선택하고, 공용 IP 주소를 사용하도록 설정하고, 사용 가능한 기존 공용 IP 주소 리소스를 선택합니다. 다음 그림에 표시된 예제에서 *myPublicIp3* 공용 IP 주소 리소스는 *IPConfig-3*에 연결됩니다.

    ![기존 IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    공용 IP 주소 리소스를 선택한 후 **저장**을 클릭하여 블레이드를 닫습니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다.

3. 다음 그림에 나와 있는 것처럼 새 IP 구성을 검토합니다.

    ![IP 구성](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


