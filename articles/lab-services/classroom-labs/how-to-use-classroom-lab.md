---
title: Azure Lab Services의 클래스룸 랩에 액세스하는 방법 | Microsoft Docs
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
ms.date: 10/17/2018
ms.author: spelluru
ms.openlocfilehash: 4b137396dd6a8ff924c9380aeb87a81b95f91414
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466225"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services의 클래스룸 랩에 액세스하는 방법
이 문서에서는 클래스룸 랩에 액세스하고, 랩의 VM에 연결하고 VM을 중지하는 방법을 설명합니다. 

## <a name="view-all-the-classroom-labs"></a>모든 클래스룸 랩 보기

1. 교수/강사로부터 받은 **등록 URL**로 이동합니다. 
2. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 
3. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 

    ![모든 랩 보기](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>클래스룸 랩에서 가상 머신에 연결

1. 액세스하려는 랩의 가상 머신을 나타내는 타일에서 **연결**을 선택합니다.

    ![모든 랩 보기](../media/how-to-use-classroom-lab/connect-button.png)
2. 가상 머신을 준비하는 데 몇 분 정도 걸립니다.

    ![가상 머신 준비하기](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. RDP 파일을 하드 디스크에 저장하고 엽니다. 
    
    ![RDP 파일 저장](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. 강사/교수로부터 받은 **사용자 이름**과 **암호**를 사용하여 컴퓨터에 로그인합니다. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>클래스룸 랩의 가상 머신 중지

클래스룸 랩을 나타내는 타일에서 **중지**를 선택합니다. VM이 중지되면 타일의 **시작** 단추를 사용할 수 있게 됩니다. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](how-to-manage-classroom-labs.md)
- [랩 설정](../tutorial-create-custom-lab.md)

 