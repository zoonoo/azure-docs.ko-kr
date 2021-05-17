---
title: Azure Data Share에서 수신자 추가
description: Azure Data Share의 기존 데이터 공유에 수신자를 추가하는 방법에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680630"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>공유에 수신자 추가하는 방법

새 공유 또는 기존 공유를 만들 때 수신자를 추가할 수 있습니다. Azure Data Share UI에서 사용자의 Azure 로그인 이메일을 사용하여 수신자를 추가할 수 있습니다.  API에서 사용자/서비스 주체와 테넌트 ID의 조합을 사용할 수 있습니다. 테넌트 ID를 지정하면 이 테넌트에만 초대를 수락할 수 있습니다. 또한 API에서 수신자에게 이메일을 보내지 않고 초대를 만들 수 있습니다. 

## <a name="add-recipient-to-an-existing-share"></a>기존 공유에 수신자 추가

Azure Data Share 보낸 공유로 이동하여 **초대** 탭을 선택합니다. 이 데이터 공유에 대한 초대를 받는 모든 수신자가 여기에 나열되어 있습니다. 새 항목을 추가하려면 **수신자 추가** 를 선택합니다.

![스크린샷은 선택된 수신자 추가를 보여줍니다.](./media/how-to/how-to-add-recipients/add-recipient.png)

페이지의 오른쪽에 패널이 표시됩니다. **수신자 추가** 를 클릭하고 새 수신자의 이메일을 빈 줄에 입력합니다. 수신자의 Azure 로그인 이메일을 사용해야 합니다(이메일 별칭을 사용하면 작동하지 않음). 

![스크린샷은 초대를 추가하고 보낼 수 있는 수신자 추가 창을 보여줍니다.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

**초대 추가 및 보내기** 를 클릭합니다. 이 공유에 대한 초대 이메일이 새로운 수신자에게 전송됩니다.

## <a name="next-steps"></a>다음 단계
[공유 초대를 삭제](how-to-delete-invitation.md)하는 방법에 대해 자세히 알아보세요.
