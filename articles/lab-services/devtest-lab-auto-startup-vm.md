---
title: Azure DevTest Labs에서 VM에 대 한 자동 시작 설정 구성 Microsoft Docs
description: 랩에서 Vm에 대 한 자동 시작 설정을 구성 하는 방법에 대해 알아봅니다. 이 설정을 사용 하면 랩의 Vm이 일정에 따라 자동으로 시작 됩니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807915"
---
# <a name="auto-startup-lab-virtual-machines"></a>자동 시작 랩 가상 컴퓨터  
Azure DevTest Labs를 사용 하 여 랩의 가상 컴퓨터가 일정에 따라 자동으로 시작 및 종료 되도록 구성할 수 있습니다. 자동 종료 설정을 구성 하는 방법에 대 한 자세한 내용은 [Azure DevTest Labs 랩에 대 한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)를 참조 하세요. 

정책을 켤 때 모든 Vm이 포함 되는 autoshutdown과 달리 자동 시작 정책에는 명시적으로 VM을 선택 하 고이 일정을 옵트인 (opt in) 하는 랩 사용자가 필요 합니다. 이렇게 하면 원치 않는 Vm이 실수로 자동으로 시작 되 고 예기치 않은 비용을 발생 시키는 상황을 쉽게 실행할 수 없습니다.

이 문서에서는 랩에 대 한 자동 시작 정책을 구성 하는 방법을 보여 줍니다.

## <a name="configure-autostart-settings-for-a-lab"></a>랩에 대 한 자동 시작 설정 구성 
1. 랩의 홈 페이지로 이동 합니다. 
2. 왼쪽 메뉴에서 **구성 및 정책** 을 선택 합니다. 

    ![구성 및 정책 메뉴](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. **구성 및 정책** 페이지에서 다음 단계를 수행 합니다.
    
    1. 이 랩에서 자동 시작 기능을 사용 하도록 설정 하려면 **가상 컴퓨터의 자동 시작 예약 허용** 에 대해 **켜기** 를 선택 합니다. 
    2. **일정 시작** 필드의 시작 시간 (예: 8:00:00 AM)을 선택 합니다. 
    3. 사용할 **표준 시간대** 를 선택 합니다. 
    4. Vm을 자동으로 시작 해야 하는 **요일을** 선택 합니다. 
    5. 그런 다음 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 

        ![자동 시작 설정](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 이 정책은 랩의 가상 컴퓨터에 자동 시작을 자동으로 적용 하지 않습니다. 개별 가상 컴퓨터를 **옵트인** 하려면 가상 컴퓨터 페이지로 이동 하 여 해당 VM에 대 한 **자동 시작** 을 사용 하도록 설정 합니다.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>랩에서 VM에 대 한 자동 시작 사용
다음 절차에서는 랩의 자동 시작 정책으로 VM을 옵트인 하는 단계를 제공 합니다. 

1. 랩의 홈 페이지에 있는 **내 가상 컴퓨터** 목록에서 **VM** 을 선택 합니다. 

    ![구성 및 정책 메뉴](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. **가상 컴퓨터** 페이지의 왼쪽 메뉴 또는 **일정** 목록에서 **자동** 시작을 선택 합니다. 

    ![자동 시작 메뉴 선택](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. **자동 시작 페이지의 자동 시작을 위해이 가상 컴퓨터 예약 허용** **옵션을** **선택 합니다.**

    ![VM에 대 한 자동 시작 사용](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 그런 다음 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 


## <a name="next-steps"></a>다음 단계
랩에 대 한 구성 자동 종료 정책에 대해 알아보려면 [Azure DevTest Labs에서 랩에 대 한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md) 를 참조 하세요.
