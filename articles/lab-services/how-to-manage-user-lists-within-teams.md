---
title: 팀에서 Azure Lab Services 사용자 목록 관리
description: 팀에서 Azure Lab Services 사용자 목록을 관리 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946712"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>팀에서 Lab Services 사용자 목록 관리

팀 내에서 랩을 만들 때 ( [팀에서 시작 및 랩 서비스 랩 만들기](how-to-get-started-create-lab-within-teams.md)참조) 랩 사용자 목록은 자동으로 채워지고 팀 멤버 자격과 동기화 됩니다. 소유자, 멤버 및 게스트를 비롯 한 팀의 모든 사용자가 랩 사용자 목록에 자동으로 추가 됩니다. Azure lab Services는 팀 멤버 자격과의 동기화를 유지 하며 자동 동기화는 24 시간 마다 트리거됩니다. 

## <a name="sync-users"></a>사용자 동기화

교육자는 팀 멤버 자격이 업데이트 된 후 **동기화** 단추를 사용 하 여 수동 동기화를 트리거할 수 있습니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="사용자 동기화":::

자동 또는 수동 동기화가 완료 되 면 랩이 게시 되었는지 여부에 따라 다음이 적용 됩니다.

* 랩이 최소한 한 번 게시 되지 않은 경우:
    * 팀 멤버 자격에 대 한 변경 내용에 따라 랩 사용자 목록에서 사용자가 추가 되거나 삭제 됩니다. 
* 랩을 한 번 이상 게시 한 경우 사용자를 추가 하거나 삭제 하는 것 외에도 랩 용량이 자동으로 업데이트 됩니다.
    * 팀에 새 항목이 추가 되 면 새 Vm이 만들어집니다.
    * 팀에서 사용자가 삭제 된 경우 연결 된 VM도 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

템플릿 VM이 구성 되 고 교육자에서 템플릿을 게시 하도록 선택 하면 랩의 사용자 목록에 있는 사용자 수에 해당 하는 Vm 수가 생성 됩니다. 랩을 게시 하 고 Vm을 만든 후에는 사용자가 자동으로 랩에 등록 되며, 첫 번째 Azure Lab Services 로그인 시 사용자가 앱을 **Azure Lab Services** 있는 탭에 처음 액세스할 때 해당 사용자에 게 vm이 할당 됩니다. 

템플릿 VM을 게시 하려면 팀 랩 서비스 창으로 이동 하 여 **템플릿** 탭 **->**  ->  ...을 선택 합니다. **게시**.

VM 풀을 관리 하려면 [팀에서 랩 서비스의 vm 풀 관리](how-to-manage-vm-pool-within-teams.md)를 참조 하세요.

### <a name="also-review"></a>또한 검토

다음 문서를 참조하세요.

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀에서 랩 서비스 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [팀에서 Lab Services 일정 만들기](how-to-create-schedules-within-teams.md)
- [팀의 랩 서비스에서 VM (학생 보기)에 액세스](how-to-access-vm-for-students-within-teams.md)

