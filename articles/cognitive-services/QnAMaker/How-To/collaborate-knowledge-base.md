---
title: 기술 자료에 대 한 공동 작업-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. 이 기능은 Azure 역할 기반 액세스 제어를 통해 제공됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75660752"
---
# <a name="collaborate-on-your-knowledge-base"></a>기술 자료에 대한 공동 작업

QnA Maker를 사용 하면 여러 사용자가 동일한 QnA Maker 리소스의 모든 기술 자료에 대해 공동 작업할 수 있습니다. 이 기능은 Azure [역할 기반 액세스 제어](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 통해 제공됩니다.

QnA Maker 서비스를 다른 사람과 공유하려면 다음 단계를 수행하세요.

1. Azure Portal에 로그인 하 고 QnA Maker 리소스로 이동 합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. **추가**를 선택합니다.

    ![QnA Maker IAM 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. **소유자** 또는 **참가자** 역할을 선택합니다. 역할 기반 액세스 제어를 통해 읽기 전용 액세스를 부여할 수 없습니다. 소유자 및 참여자 역할에는 QnA Maker 서비스에 대 한 읽기/쓰기 액세스 권한이 있습니다.

    ![QnA Maker IAM 역할 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 사용자의 전자 메일 주소를 입력 하 고 **저장**을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

QnA Maker 서비스를와 공유 하는 사람이 [QnA Maker 포털](https://qnamaker.ai) 에 로그인 하면 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

특정 기술 자료는 QnA Maker 서비스에서 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 서비스에 기술 자료를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 테스트](./test-knowledge-base.md)
