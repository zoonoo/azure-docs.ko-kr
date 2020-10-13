---
title: 팀에서 Azure Lab Services의 VM 풀 관리
description: 팀에서 Azure Lab Services의 VM 풀을 관리 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946760"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>팀의 랩 서비스에서 VM 풀 관리

VM (가상 머신) 만들기는 템플릿 VM이 처음 게시 되는 즉시 시작 됩니다. 랩 사용자 목록의 사용자 수가 배에 이르는 Vm이 생성 됩니다. Azure Lab Services에 대 한 첫 번째 로그인에 따라 학생에 게 자동으로 할당 됩니다. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>템플릿 게시 및 VM 풀 관리

템플릿을 게시 하려면 팀 랩 서비스 창으로 이동 하 고 **템플릿** 탭 **->**  ->  ...을 선택 합니다. **게시**.

템플릿 VM이 구성 되 고 교육자에서 템플릿을 게시 하도록 선택 하면 랩의 사용자 목록에 있는 사용자 수에 해당 하는 Vm 수가 생성 됩니다. 랩을 게시 하 고 Vm을 만든 후에는 사용자가 자동으로 랩에 등록 되며, 첫 번째 Azure Lab Services 로그인 시 사용자가 앱을 **Azure Lab Services** 있는 탭에 처음 액세스할 때 해당 사용자에 게 vm이 할당 됩니다. 

사용자 목록 동기화가 트리거되면 랩 용량 (랩의 Vm 수)은 팀 멤버 자격에 대 한 변경 내용에 따라 자동으로 업데이트 됩니다. 새 사용자가 추가 되 면 새 Vm이 만들어지고 팀에서 제거 된 사용자에 게 할당 된 Vm도 삭제 됩니다. 자세한 내용은 [팀 내에서 사용자를 관리 하는 방법을](how-to-manage-user-lists-within-teams.md)참조 하세요. 

교육자는 VM 풀 탭에서 학생 Vm에 직접 액세스할 수 있습니다. 그리고 교육자는 **가상 머신 풀** 탭에서 또는 화면의 위쪽/오른쪽 모서리에 있는 **내 Virtual Machines** 단추를 클릭 하 여 자신에 게 할당 된 vm에 액세스할 수 있습니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM 풀":::

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀에서 랩 서비스 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [팀에서 Lab Services 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [팀에서 Lab Services 일정 만들기](how-to-create-schedules-within-teams.md)
- [팀의 랩 서비스에서 VM (학생 보기)에 액세스](how-to-access-vm-for-students-within-teams.md)


