---
title: Azure DevTest 랩에서 VM에 대한 자동 시작 설정 구성 | 마이크로 소프트 문서
description: 랩에서 VM에 대한 자동 시작 설정을 구성하는 방법에 대해 알아봅니다. 이 설정을 사용하면 랩의 VM을 일정에 따라 자동으로 시작할 수 있습니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807915"
---
# <a name="auto-startup-lab-virtual-machines"></a>자동 시작 랩 가상 머신  
Azure DevTest Labs를 사용하면 일정에 따라 랩의 가상 컴퓨터를 자동으로 시작하고 종료하도록 구성할 수 있습니다. 자동 종료 설정 구성에 대한 자세한 내용은 [Azure DevTest Labs의 랩에 대한 자동 종료 정책 관리를](devtest-lab-auto-shutdown.md)참조하십시오. 

정책이 켜져 있을 때 모든 VM이 포함된 자동 종료와 달리 자동 시작 정책에 따라 랩 사용자는 VM을 명시적으로 선택하고 이 일정을 옵트인해야 합니다. 이렇게 하면 원치 않는 VM이 실수로 자동 시작되어 예기치 않은 지출이 발생할 수 있는 상황이 쉽게 발생하지 않습니다.

이 문서에서는 랩에 대한 자동 시작 정책을 구성하는 방법을 보여 주며 이 문서에서는

## <a name="configure-autostart-settings-for-a-lab"></a>랩에 대한 자동 시작 설정 구성 
1. 랩의 홈 페이지로 이동합니다. 
2. 왼쪽 메뉴에서 **구성 및 정책을** 선택합니다. 

    ![구성 및 정책 메뉴](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 구성 **및 정책** 페이지에서 다음 단계를 수행합니다.
    
    1. 이 랩의 자동 시작 기능을 사용하도록 **설정하려면 가상 컴퓨터를 자동 시작하도록 예약할 수 있도록** **켜기를** 선택합니다. 
    2. **일정 시작** 필드의 시작 시간(예: 오전 8:00:00)을 선택합니다. 
    3. 사용할 **표준 시간대를** 선택합니다. 
    4. VM을 자동으로 시작해야 하는 **요일을** 선택합니다. 
    5. 그런 다음 도구 모음에 **저장을** 선택하여 설정을 저장합니다. 

        ![자동 시작 설정](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 이 정책은 랩의 모든 가상 컴퓨터에 자동 시작을 자동으로 적용하지 않습니다. 개별 가상 컴퓨터를 **선택하려면** 가상 컴퓨터 페이지로 이동하여 해당 VM에 대해 **자동 시작을** 사용하도록 설정합니다.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>랩에서 VM에 대해 자동 시작 을 사용하도록 설정
다음 절차에서는 VM을 랩의 자동 시작 정책에 옵트인하는 단계를 제공합니다. 

1. 랩의 홈 페이지에서 **내 가상 컴퓨터** 목록에서 **VM을** 선택합니다. 

    ![구성 및 정책 메뉴](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 가상 **컴퓨터** 페이지에서 왼쪽 메뉴 또는 일정 목록에서 **자동 시작을** **선택합니다.** 

    ![자동 시작 메뉴 선택](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 자동 **시작** 페이지에서 이 가상 시스템 자동 시작 옵션을 **예약할 수 있도록** **켜기를** 선택합니다.

    ![VM에 대해 자동 시작 을 사용하도록 설정](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 그런 다음 도구 모음에 **저장을** 선택하여 설정을 저장합니다. 


## <a name="next-steps"></a>다음 단계
랩의 구성 자동 종료 정책에 대해 자세히 알아보려면 [Azure DevTest Labs의 랩에 대한 자동 종료 정책 관리를 참조하세요.](devtest-lab-auto-shutdown.md)
