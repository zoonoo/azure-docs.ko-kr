---
title: 기술 자료에 대한 공동 작업 - Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. 이 기능은 Azure 역할 기반 액세스 제어를 통해 제공됩니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: c7b970d9d906e9a703e396d6c9358d7dde733250
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220062"
---
# <a name="collaborate-on-your-knowledge-base"></a>기술 자료에 대한 공동 작업

QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. 이 기능은 Azure [역할 기반 액세스 제어](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 통해 제공됩니다. 

QnA Maker 서비스를 다른 사람과 공유하려면 다음 단계를 수행하세요.

1. Azure Portal에 로그인하고 QnA Maker 리소스로 이동합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. **추가**를 선택합니다.

    ![QnA Maker IAM 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. **소유자** 또는 **참가자** 역할을 선택합니다. 역할 기반 액세스 제어를 통해 읽기 전용 액세스를 부여할 수 없습니다. 소유자 및 기여자 역할에는 QnA Maker 서비스에 대한 읽기/쓰기 액세스 권한이 있습니다.

    ![QnA Maker IAM 역할 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. 공유할 메일을 입력하고 저장을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

이제 QnA Maker 서비스를 공유한 사용자가 [QnA Maker 포털](https://qnamaker.ai)에 로그인하면 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

특정 기술 자료는 QnA Maker 서비스에서 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 서비스에 기술 자료를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 테스트](./test-knowledge-base.md)
