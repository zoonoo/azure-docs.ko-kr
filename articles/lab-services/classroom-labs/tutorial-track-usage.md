---
title: Azure Lab Services에서 랩 사용량 추적 | Microsoft Docs
description: 이 자습서에서는 랩 작성자/소유자로서 랩 사용량을 추적합니다.
services: lab-services
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
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 710d157dcf4c6d060e59bcfbb69455e2ddc91bdd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450133"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>자습서: Azure Lab Services에서 랩 사용량 추적
이 자습서에서는 랩 작성자/소유자가 랩 사용량을 추적하는 방법을 보여줍니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩에 등록된 사용자 보기
> * 랩의 VM 사용량 보기
> * 학생 VM 관리 


## <a name="view-users-registered-with-the-lab"></a>랩에 등록된 사용자 보기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다.
3. **내 랩** 페이지에서, 사용량을 추적하려는 랩을 선택합니다. 
4. **사용자** 탭을 선택합니다. 랩에 등록한 학생이 표시됩니다. **등록 링크**를 선택하고, 링크를 복사하여 아직 랩에 등록하지 않은 새로운 학생에게 보냅니다. 

    ![등록된 사용자](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>랩의 VM 사용량 보기 

1. 왼쪽 메뉴에서 **가상 머신**을 선택합니다. 
2. VM 상태 및 VM 실행 시간이 보이는지 확인합니다. 학생 VM에서 소비한 시간은 마지막 열에 표시된 사용량 시간에 포함되지 않습니다. 

    ![VM 사용량](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>학생 VM 관리 
가상 머신 목록의 행을 마우스로 가리키면 다음 작업을 수행하는 컨트롤이 표시됩니다. 

- VM에 연결
- VM 시작
- VM 중지
- VM 삭제

![VM 컨트롤](../media/tutorial-track-usage/vm-controls.png) 



## <a name="next-steps"></a>다음 단계
이 자습서에서는 랩에 등록한 사용자를 찾고, 랩에서 VM 사용량을 추적하고, 랩에서 VM을 관리하는 방법을 알아보았습니다.

클래스룸 랩에 대한 자세한 내용은 [방법 가이드](how-to-manage-lab-accounts.md)의 토픽을 참조하세요.
