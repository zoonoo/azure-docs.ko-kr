---
title: "ARM 템플릿을 사용하여 가상 네트워크 만들기 | Microsoft Docs"
description: "ARM(Azure 리소스 관리자)에서 ARM 템플릿을 사용하여 가상 네트워크를 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 31b066d85474e781bd282603fceba3fa8ab135d7


---
# <a name="create-a-virtual-network-by-using-an-arm-template"></a>ARM 템플릿을 사용하여 가상 네트워크를 만들기
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 리소스 관리자 배포 모델을 사용하여 VNet을 만드는 방법을 설명합니다. [클래식 배포 모델에서 가상 네트워크를 만들](virtual-networks-create-vnet-classic-pportal.md)수도 있습니다.

GitHub에서 기존 ARM 템플릿을 다운로드한 후 수정하고 GitHub, PowerShell 및 Azure CLI에서 템플릿을 배포하는 방법에 알아봅니다.

변경하지 않고 GitHub에서 직접 ARM 템플릿을 배포하는 경우 [github에서 템플릿 배포](#deploy-the-arm-template-by-using-click-to-deploy)로 건너뜁니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]




<!--HONumber=Nov16_HO2-->


