---
title: 팀 내에서 Azure Lab Services 일정 만들기
description: 팀 내에서 Lab Services 일정을 만드는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042340"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>팀 내에서 Lab Services 일정 만들기 및 관리

일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 다음 절차에서는 Classroom(강의실) 랩의 일정을 만들고 관리하는 단계를 제공합니다. 

다음은 랩 가상 컴퓨터에 영향을 주는 일정입니다. 

- 템플릿 가상 머신은 일정에 포함 되지 않습니다. 
- 할당 된 가상 머신만 시작 됩니다. 즉, 학생 (최종 사용자)이 컴퓨터를 요청 하지 않은 경우에는 예약 된 시간에 컴퓨터가 시작 되지 않습니다. 
- 모든 가상 컴퓨터 (사용자의 요구 여부에 상관 없음)는 랩 일정에 따라 중지 됩니다. 

> [!IMPORTANT]
> VM의 예약된 실행 시간은 사용자에게 할당된 할당량에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다. 

사용자는 [labs 웹 사이트](https://labs.azure.com)에서와 마찬가지로 팀 내에서 랩 일정을 만들고, 편집 하 고, 삭제할 수 있습니다. [일정 만들기 및 관리](how-to-create-schedules-within-teams.md)에 대 한 문서를 참조 하세요.

## <a name="automatic-shutdown-and-disconnect-settings"></a>자동 종료 및 연결 끊기 설정

여러 자동 종료 비용 제어 기능을 사용 하 여 가상 머신이 적극적으로 사용 되지 않을 때 추가 비용을 사전에 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능의 조합은 사용자가 실수로 가상 컴퓨터를 실행 하는 대부분의 경우를 포착 합니다.
 
- OS가 유휴 상태로 하다 고 판단 가상 머신에서 사용자를 자동으로 연결을 끊습니다.
- 사용자가 연결을 끊으면 자동으로 가상 컴퓨터를 종료 합니다.
- 시작 되었지만 사용자가 연결 하지 않는 가상 컴퓨터를 자동으로 종료 합니다.

자세한 내용은 [랩에 대 한 자동 종료 설정 구성](how-to-enable-shutdown-disconnect.md)문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀 내에서 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [팀 내의 랩 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [팀 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [팀 내에서 VM 액세스 – 학생 보기](how-to-access-vm-for-students-within-teams.md)
