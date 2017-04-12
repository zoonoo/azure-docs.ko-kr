---
title: "Azure에서 SharePoint Server 팜 만들기 | Microsoft Docs"
description: "Azure Portal Marketplace를 사용하여 Azure에서 새 SharePoint 2013 또는 2016 SharePoint 팜을 신속하게 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 00648ee35962b22fb7eeceaa6d9df7305c801abf
ms.lasthandoff: 03/31/2017


---
# <a name="create-sharepoint-server-farms-using-the-azure-portal-marketplace"></a>Azure Portal Marketplace를 사용하여 SharePoint 서버 팜 만들기

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>SharePoint 2013 팜
Microsoft Azure 포털 마켓플레이스를 사용하면 미리 구성된 SharePoint Server 2013 팜을 신속하게 만들 수 있습니다. 그러면 개발 및 테스팅 환경에 기본 또는 고가용성 SharePoint 팜이 필요하거나 SharePoint Server 2013을 조직의 협력 솔루션으로 평가하는 경우 상당한 시간을 절약할 수 있습니다.

> [!NOTE]
> Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다. 이 항목은 **SharePoint 2013 비 HA 팜** 및 **SharePoint 2013 HA 팜** 항목으로 대체되었습니다.
>
>

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/sharepoint-farm/Non-HAFarm.png)

이 팜 구성을 간소화된 SharePoint 앱 개발 설정이나 SharePoint 2013의 최초 평가에 사용할 수 있습니다.

기본(3-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 비 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 비 HA 팜** 창의 단계에서 설정을 지정한 다음 **만들기**를 클릭합니다.

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/sharepoint-farm/HAFarm.png)

이 팜 구성을 사용하여 SharePoint 팜에 대해 보다 과도한 클라이언트 부하, 외부 SharePoint 사이트의 고가용성 및 SQL Server AlwaysOn 가용성 그룹을 테스트할 수 있습니다. 또한 고가용성 환경에서 SharePoint app 개발에 이 구성을 사용할 수 있습니다.

고가용성(9-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 HA 팜** 창의 7단계에서 설정을 지정한 다음 **만들기**를 클릭합니다.

> [!NOTE]
> Azure 무료 평가판에서는 **SharePoint 2013 비 HA 팜** 또는 **SharePoint 2013 HA 팜**을 만들 수 없습니다.
>
>

Azure 포털은 인터넷 연결 웹 서비스를 사용하여 클라우드 전용 가상 네트워크에 이러한 팜을 모두 만듭니다. 조직 네트워크에 대한 사이트 간 VPN 또는 ExpressRoute 연결은 없습니다.

> [!NOTE]
> Azure Portal을 사용하여 기본 또는 고가용성 SharePoint 팜을 만들 때 기존 리소스 그룹을 지정할 수 없습니다. 이 제한을 해결하려면 Azure PowerShell을 사용하여 이러한 팜을 만듭니다. 자세한 내용은 [Azure PowerShell을 사용하여 SharePoint 2013 개발/테스트 팜 만들기](https://technet.microsoft.com/library/mt743093.aspx#powershell)를 참조하세요.
>
>

## <a name="sharepoint-2016-farms"></a>SharePoint 2016 팜
다음 단일 서버 SharePoint Server 2016 팜을 빌드하는 방법에 대한 지침은 [이 문서](https://technet.microsoft.com/library/mt723354.aspx)를 참조하세요.

![sharepointfarm](./media/sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>SharePoint 팜 관리
원격 데스크톱 연결을 통해 이러한 팜의 서버를 관리할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 로그온](quick-create-portal.md#connect-to-virtual-machine)을 참조하세요.

중앙 관리 SharePoint 사이트에서 내 사이트, SharePoint 응용 프로그램 및 기타 기능을 구성할 수 있습니다. 자세한 내용은 [SharePoint 구성](http://technet.microsoft.com/library/ee836142.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* Azure 인프라 서비스에서 추가 [SharePoint 구성](https://technet.microsoft.com/library/dn635309.aspx) 을 검색합니다.

