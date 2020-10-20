---
title: 팀 내에서 시작 및 Azure Lab Services 랩 만들기
description: 팀 내에서 Azure Lab Services 랩을 시작 하 고 만드는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: e17b0be223a1630402fc4f32c692d7ecfe184783
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217085"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>팀 내에서 랩 서비스 랩 시작 및 만들기

이 문서에서는 팀에 **Azure Lab Services** 앱을 추가 하 고 MS 팀 환경 내에서 랩을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 팀에 대 한 가상 컴퓨터를 사용 하 여 랩을 설정 합니다. 랩 계정에서 랩을 설정 하려면 랩 계정에서 소유자, 랩 작성자 또는 참가자 역할 중 하나의 멤버 여야 합니다. 랩 계정을 만드는 데 사용된 계정은 소유자 역할에 자동으로 추가됩니다. 따라서 랩을 만들기 위해 랩 계정을 만드는 데 사용한 사용자 계정을 사용할 수 있습니다.

팀 내에서 Azure Lab Services를 사용 하는 일반적인 워크플로는 다음과 같습니다.

1. 사용자가 Azure Portal에서 [랩 계정을 만듭니다](tutorial-setup-lab-account.md#create-a-lab-account) .
1. [랩 계정 작성자는 다른 사용자](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) 를 **랩 작성자** 역할에 추가 합니다. 예를 들어 랩 계정 작성자/관리자는 **랩 작성자** 역할에 교육자를 추가하여 해당 클래스에 대한 랩을 만들 수 있습니다.
1. 그런 다음, 교육자는 랩을 만들고, 템플릿 VM을 미리 구성 하 고, 팀의 모든 사람에 게 VM을 프로 비전 하는 랩을 게시 합니다.
1. 랩을 게시 한 후에는 팀 (SSO) 내에서 **Azure Lab Services** 앱을 포함 하는 탭을 클릭 하거나 [labs 웹 사이트](https://labs.azure.com)에 액세스 하 여 Azure Lab Services에 대 한 첫 번째 로그인의 팀 멤버 자격 목록에 있는 모든 사용자에 게 VM이 할당 됩니다. 그러면 사용자가 VM을 사용 하 여 클래스 작업 및 과제를 수행할 수 있습니다.

> [!IMPORTANT]
> 랩 계정이 팀과 같은 테 넌 트에 생성 된 경우에만 팀 내에서 Azure Lab Services를 사용할 수 있습니다.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>팀에 탭으로 Azure Lab Services 앱 추가

팀 소유자는 팀 채널에서 직접 **Azure Lab Services** 앱을 추가할 수 있으며, 팀의 모든 사용자가 앱을 사용할 수 있습니다. 다음 세 단계를 수행 합니다.

1. 앱을 추가 하려는 팀 채널로 이동 하 고 **+** 탭을 추가 하도록 선택 합니다. 
1. 탭 옵션에서 **Azure Lab Services** 을 검색 하 고이 앱을 추가 합니다. 

    > [!NOTE]
    > 팀 **소유자** 만이 팀을 위한 랩을 만들 수 있습니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="탭 추가":::
1. 이 팀에서 교실 랩을 만드는 데 사용할 랩 서비스 계정을 선택 합니다. 

    Azure Lab Services [Azure Lab Services 웹 사이트](https://labs.azure.com) 에 Single Sign-On를 사용 하 고 액세스 권한이 있는 모든 랩 계정을 가져옵니다. 

    팀과 같은 테 넌 트에 있는 계정과 **소유자**, **참가자**또는 **작성자** 액세스 권한이 있는 계정입니다. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="탭 추가":::
1. **저장** 을 누르고 탭이 채널에 추가 됩니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="탭 추가":::

    이제 채널에서 **Azure Lab Services** 탭을 선택 하 고 다음 문서에 설명 된 대로 실습 관리를 시작할 수 있습니다.

랩 계정이 선택 되 면 팀 소유자는 팀을 위한 랩을 만들 수 있습니다. 전체 랩 생성 프로세스와 랩 수준의 모든 태스크는 팀 내에서 수행할 수 있습니다. 사용자는 동일한 팀 내에서 여러 랩을 만들 수 있으며 랩 계정 수준에서 적절 한 액세스 권한으로 팀 소유자는 특정 팀과 연결 된 랩을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

팀 내에서 랩을 만들면 랩 사용자 목록이 자동으로 채워지고 팀 멤버 자격과 동기화 됩니다. 소유자, 멤버 및 게스트를 포함 하 여 팀의 모든 사용자가 랩 사용자 목록에 자동으로 추가 됩니다. Azure lab Services는 팀 멤버 자격과의 동기화를 유지 하 고 자동 동기화는 24 시간 마다 트리거됩니다. 자세한 내용은 다음을 참조하세요.

[팀 내의 Lab Services 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>참고자료

다음 문서도 참조합니다.

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [팀 내에서 랩 일정 만들기 및 관리](how-to-create-schedules-within-teams.md)
- [팀 내에서 VM 액세스 – 학생 보기](how-to-access-vm-for-students-within-teams.md)
- [팀 내에서 랩 삭제](how-to-delete-lab-within-teams.md)
