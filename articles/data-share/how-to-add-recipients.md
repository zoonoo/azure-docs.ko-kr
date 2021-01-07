---
title: Azure 데이터 공유에서 받는 사람 추가
description: Azure 데이터 공유의 기존 데이터 공유에 받는 사람을 추가 하는 방법에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680630"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>공유에 받는 사람을 추가 하는 방법

새 공유 또는 기존 공유를 만들 때 수신자를 추가할 수 있습니다. Azure 데이터 공유 UI에서 사용자의 Azure 로그인 전자 메일을 사용 하 여 받는 사람을 추가할 수 있습니다.  API에서 사용자/서비스 주체와 테 넌 트 ID의 조합을 사용할 수 있습니다. 테 넌 트 ID를 지정 하면이 테 넌 트에만 초대를 수락할 수 있습니다. 또한 API에서 받는 사람에 게 전자 메일을 보내지 않고 초대를 만들 수 있습니다. 

## <a name="add-recipient-to-an-existing-share"></a>기존 공유에 받는 사람 추가

Azure 데이터 공유에서 보낸 공유로 이동 하 고 **초대** 탭을 선택 합니다. 여기에 나열 된 것은이 데이터 공유에 대 한 초대의 모든 받는 사람입니다. 새 항목을 추가 하려면 **받는 사람 추가** 를 클릭 합니다.

![선택한 받는 사람 추가가 스크린샷에 표시 됩니다.](./media/how-to/how-to-add-recipients/add-recipient.png)

페이지의 오른쪽에 패널이 표시됩니다. **받는 사람 추가** 를 클릭 한 다음 새 받는 사람의 전자 메일을 빈 줄에 입력 합니다. 수신자의 Azure 로그인 전자 메일을 사용 해야 합니다 (전자 메일 별칭을 사용 하 여 작동 하지 않음). 

![초대를 추가 하 고 보낼 수 있는 받는 사람 추가 창이 스크린샷에 표시 됩니다.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

**추가 및 초대 보내기를** 클릭 합니다. 새 수신자가이 공유에 초대 전자 메일을 보냅니다.

## <a name="next-steps"></a>다음 단계
[공유에](how-to-delete-invitation.md)대 한 초대를 삭제 하는 방법에 대해 자세히 알아보세요.
