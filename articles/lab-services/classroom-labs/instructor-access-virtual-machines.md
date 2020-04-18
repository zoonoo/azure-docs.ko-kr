---
title: Azure 랩 서비스에서 VM에 액세스하는 강사
description: 이 문서에서는 교수자가 교수자 보기에서 학생 VM에 액세스하는 방법을 보여 주었습니다. 예를 들어 조교는 한 수업의 교수자가 될 수 있지만 다른 수업의 학생은 될 수 있습니다.
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
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641941"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>교수자 보기에서 학생으로 가상 머신에 액세스
이 문서에서는 교수자가 학생으로 참석하는 수업에 대해 VM에 액세스하는 방법을 보여 주었습니다. 

다음은 이 기능이 도움이 되는 시나리오입니다. 조교는 한 수업의 강사이지만 다른 수업의 학생입니다. 또한 교수자는 자신이 소유한 랩을 보여 주는 교수자 보기에서 학생 VM을 보고 액세스하려고 합니다. 

## <a name="access-vms-from-instructor-view"></a>강사 보기에서 VM 액세스

1. Azure 랩 [서비스 웹 사이트에](https://labs.azure.com)로그인합니다. 소유한 랩이 표시됩니다. 이러한 랩은 자신이 만든 랩또는 관리자가 소유자로 지정한 랩일 수 있습니다. 자세한 내용은 [기존 랩에 소유자를 추가하는 방법을 참조하세요.](how-to-add-user-lab-owner.md)
2. 학생으로 참석하는 수업의 VM에 액세스하려면 오른쪽 상단 모서리에 있는 컴퓨터 아이콘을 선택합니다. 학생으로 액세스할 수 있는 VM이 표시되는지 확인합니다. 다음 예제에서 사용자는 Python 랩의 조교이지만 Java 랩의 학생입니다. 따라서 사용자는 드롭다운 목록에서 Java 랩의 VM을 봅니다. 사용자는 VM을 시작하고 연결할 수 있습니다. 
    
    ![학생 VM 액세스](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [VM에 연결](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-rdp.md)
- [리눅스 가상 머신에 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)
