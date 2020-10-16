---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977210"
---
1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결**을 선택합니다. 드롭다운에서 **Bastion**을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Bastion 선택" border="false":::

1. 드롭다운에서 Bastion을 선택하면 다음과 같은 세 개의 탭이 있는 사이드바가 나타납니다. RDP, SSH 및 Bastion Bastion이 가상 네트워크용으로 프로비저닝되었기 때문에 Bastion 탭은 기본적으로 활성화되어 있습니다. **Bastion 사용**을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Bastion 선택" border="false":::

1. **Azure Bastion을 사용하여 연결** 페이지에서 가상 머신의 사용자 이름과 암호를 입력한 다음, **연결**을 선택합니다.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Bastion 선택" border="false":::

1. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해).

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Bastion 선택" border="false":::
