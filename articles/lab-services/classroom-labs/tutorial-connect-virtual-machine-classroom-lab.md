---
title: Azure Lab Services의 클래스룸 랩에 액세스 | Microsoft Docs
description: 이 자습서에서는 교수가 설정한 클래스룸 랩의 가상 머신에 액세스합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3ec3abffc7962051f4cfc02d5369581ca193d70e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775580"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>자습서: Azure Lab Services의 클래스룸 랩에 액세스
이 자습서에서는 사용자가 학생이 되어 클래스룸 랩의 VM(가상 머신)에 연결합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 등록 링크 사용 
> * 가상 머신에 연결

## <a name="use-the-registration-link"></a>등록 링크 사용

1. 교수/강사로부터 받은 **등록 URL**로 이동합니다. 등록을 완료한 후에는 등록 URL을 사용할 필요가 없습니다. 대신 [https://labs.azure.com](https://labs.azure.com) URL을 사용합니다. 
1. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 
2. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 
3. 가상 머신이 준비될 때까지 기다렸다가 VM을 **시작**합니다. 이 프로세스는 다소 시간이 걸립니다.  

    ![VM 시작](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

1. 액세스하려는 랩의 가상 머신 타일에서 **연결**을 선택합니다. 

    ![VM에 연결](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 다음 단계 중 하나를 수행합니다. 
    1. **Windows** 가상 머신의 경우 **RDP** 파일을 하드 디스크에 저장합니다. RDP 파일을 열어 가상 머신에 연결합니다. 강사/교수로부터 받은 **사용자 이름** 및 **암호**를 사용하여 머신에 로그인합니다. 
    3. **Linux** 가상 머신의 경우 **가상 머신에 연결** 대화 상자에서 SSH 연결 문자열을 복사하고 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 문자열을 사용하여 가상 머신에 연결합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 강사/교수로부터 받은 등록 링크를 사용하여 클래스룸 랩에 액세스했습니다.

랩 소유자는 랩에 누가 등록되었는지 확인하고 VM 사용량을 추적하고 싶을 것입니다. 랩 사용량을 추적하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [랩 사용량 추적](tutorial-track-usage.md) 