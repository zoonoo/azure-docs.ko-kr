---
title: Azure 랩 서비스에서 강의실 랩 VM에 대한 암호 재설정 | 마이크로 소프트 문서
description: Azure Lab 서비스의 강의실 랩에서 가상 컴퓨터(VM)에 대한 암호를 재설정하는 방법을 알아봅니다.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583690"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>교실 랩(학생)의 가상 컴퓨터에 대한 암호 설정 또는 재설정
이 문서에서는 학생이 VM에 대한 암호를 설정/재설정하는 방법을 보여 줍니다. 

## <a name="enable-resetting-of-passwords"></a>암호 재설정 사용
랩을 만들 때 랩 소유자는 **모든 가상 컴퓨터에 대해 동일한 암호 사용을**활성화하거나 사용하지 않도록 설정할 수 있습니다. 이 옵션을 사용하도록 설정한 경우 학생은 암호를 재설정할 수 없습니다. 랩의 모든 VM에는 교수자가 설정한 것과 동일한 암호가 있습니다. 

이 옵션을 사용하지 않도록 설정하면 처음으로 VM에 연결하려고 할 때 암호를 설정해야 합니다. 학생은 이 문서의 마지막 섹션에 표시된 대로 나중에 언제든지 암호를 재설정할 수도 있습니다. 

## <a name="reset-password-for-the-first-time"></a>처음으로 암호 재설정
모든 **가상 시스템에 대해 동일한 암호 사용** 옵션을 사용하지 않도록 설정한 경우 사용자(학생)가 내 가상 **컴퓨터** 페이지에서 랩 타일의 **연결** 단추를 선택하면 사용자가 다음 대화 상자를 보고 VM의 암호를 설정합니다. 

![학생의 암호 재설정](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>나중에 암호 재설정
학생은 또한 랩 타일에서 오버플로**메뉴(세로 3점)를**클릭하고 **암호 재설정을**선택하여 암호를 설정할 수 있습니다. 

![나중에 암호 재설정](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용 옵션에 대해 알아보려면 다음 문서를 [참조하십시오.](how-to-configure-student-usage.md)
