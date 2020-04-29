---
title: Azure Lab Services에서 Vm에 액세스 하는 강사
description: 이 문서에서는 강사가 강사 보기에서 학생 Vm에 액세스 하는 방법을 보여 줍니다. 예를 들어, 학습 도우미는 한 클래스에 대 한 강사 이지만 다른 클래스에 대 한 학생이 될 수 있습니다.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641941"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>강사 보기에서 학생으로 가상 머신 액세스
이 문서에서는 강사가 학생으로 참석 하는 클래스에 대 한 Vm에 액세스 하는 방법을 보여 줍니다. 

이 기능이 도움이 되는 시나리오는 다음과 같습니다. 학습 도우미는 한 클래스에 대 한 강사 이지만 다른 클래스의 학생입니다. 또한 교육 강사는 자신이 소유한 랩을 보여 주는 강사 보기에서 학생 Vm을 보고 액세스 하려고 합니다. 

## <a name="access-vms-from-instructor-view"></a>강사 보기에서 Vm 액세스

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)에 로그인 합니다. 소유 하 고 있는 랩을 볼 수 있습니다. 이러한 랩은 사용자가 직접 만든 랩 또는 관리자가 소유자로 할당 된 랩을 사용할 수 있습니다. 자세한 내용은 [기존 랩에 소유자를 추가 하는 방법](how-to-add-user-lab-owner.md) 을 참조 하세요.
2. 학생으로 참석 하는 클래스에 대 한 Vm에 액세스 하려면 오른쪽 위 모서리에서 컴퓨터 아이콘을 선택 합니다. 학생으로 액세스할 수 있는 Vm이 표시 되는지 확인 합니다. 다음 예제에서 사용자는 Python 랩에 대 한 학습 도우미가 며 Java 랩의 학생입니다. 따라서 사용자는 드롭다운 목록에서 Java 랩의 VM을 볼 것입니다. 사용자가 VM을 시작 하 고 연결할 수 있습니다. 
    
    ![학생 Vm 액세스](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [VM에 연결](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-rdp.md)
- [Linux 가상 머신에 대 한 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)
