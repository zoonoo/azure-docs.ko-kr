---
title: Azure Lab Services에서 교실 랩 Vm에 대 한 암호 다시 설정 Microsoft Docs
description: Azure Lab Services의 교실 랩에서 가상 머신 (Vm)에 대 한 암호를 다시 설정 하는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 26b0f710590496875521e0dd8577a35841fbd3dd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657009"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>교실 랩의 가상 컴퓨터에 대 한 암호 설정 또는 다시 설정 (학생용)
이 문서에서는 학생이 Vm에 대 한 암호를 설정/다시 설정 하는 방법을 보여 줍니다. 

## <a name="enable-resetting-of-passwords"></a>암호 재설정 사용
랩을 만들 때 랩 소유자는 **모든 가상 컴퓨터에 대해 동일한 암호를 사용**하거나 사용 하지 않도록 설정할 수 있습니다. 이 옵션을 사용 하도록 설정 하면 학생이 암호를 재설정할 수 없습니다. 랩의 모든 Vm은 강사가 설정한 것과 동일한 암호를 갖습니다. 

이 옵션을 사용 하지 않도록 설정 하면 사용자는 처음으로 VM에 연결 하려고 할 때 암호를 설정 해야 합니다. 이 문서의 마지막 섹션에 표시 된 대로 나중에 언제 든 지 암호를 다시 설정할 수도 있습니다. 

## <a name="reset-password-for-the-first-time"></a>처음으로 암호 다시 설정
**모든 가상 컴퓨터에 대해 동일한 암호 사용** 옵션을 사용 하지 않도록 설정한 경우 사용자 (학생) **내 가상 컴퓨터** 페이지의 랩 타일에서 **연결** 단추를 선택 하면 사용자가 VM에 대 한 암호를 설정 하는 다음 대화 상자를 볼 수 있습니다. 

![학생 암호 다시 설정](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>나중에 암호 다시 설정
또한 학생은 랩 타일에서 오버플로 메뉴 (**세로 3 점**)를 클릭 하 고 **암호 재설정**을 선택 하 여 암호를 설정할 수 있습니다. 

![나중에 암호 다시 설정](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용 옵션에 대해 알아보려면 다음 문서를 참조 하세요. [학생 사용을 구성](how-to-configure-student-usage.md)합니다.
