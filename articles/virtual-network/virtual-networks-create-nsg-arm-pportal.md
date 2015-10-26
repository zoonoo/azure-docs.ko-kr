<properties 
   pageTitle="Preview 포털을 사용하여 ARM 모드에서 NSG를 만드는 방법 | Microsoft Azure"
   description="Preview 포털을 사용하여 ARM에서 NSG를 만들고 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="telmos" />

# Preview 포털을 사용하여 NSG를 관리하는 방법

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-classic-ps.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## 클릭하여 배포하는 방식으로 ARM 템플릿 배포

이번에는 미리 보기에서 NSG를 만들 수 없습니다. 그러나 기존 NSG를 관리할 수는 있습니다. NSG를 관리하려면 먼저 공용 저장소에서 사용할 수 있는 샘플 템플릿을 사용하여 위의 시나리오에 설명된 리소스를 만듭니다. [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)을 배포하고 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

## 기존 NSG에서 규칙 만들기

Preview 포털의 기존 NSG에 규칙을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **찾아보기>** > **네트워크 보안 그룹**을 클릭합니다.

![Preview 포털 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. NSG 목록에서 **NSG-FrontEnd** > **인바운드 보안 규칙**을 클릭합니다.

![Preview 포털 - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. **인바운드 보안 규칙** 목록에서 **추가**를 클릭합니다.

![Preview 포털 - 규칙 추가](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. **인바운드 보안 규칙 추가** 블레이드에서 우선순위 *200*의 *web-rule*이라는 규칙을 만들어 *TCP* 및 포트 *80*을 통한 모든 원본의 모든 VM으로 액세스를 허용한 후 **확인**을 클릭합니다. 이러한 설정 중 대부분은 이미 기본 값입니다.

![Preview 포털 - 규칙 설정](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. 몇 초 후는 NSG에 새 규칙이 표시됩니다.

![Preview 포털 - 새 규칙](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=Oct15_HO3-->