---
title: 데모 Conversation Learner 모델, 암호 재설정 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 데모 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 15aa3a8346087908cf77f1f68db916cc2c184448
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389868"
---
# <a name="demo-password-reset"></a>데모: 암호 재설정
이 자습서에서는 Conversation Learner에서 제공하는 암호 재설정을 사용하여 유용할 수 있는 간단한 기술 지원 봇을 보여줍니다. 봇의 모델은 도메인 외부 클래스를 포함하여 특수한 대화 흐름, 다중 턴 시퀀스를 학습할 수 있습니다. 코드 또는 엔터티 없이 작업을 수행할 수 있습니다.

## <a name="video"></a>비디오

[![데모 암호 미리 보기](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 암호 재설정 봇이 실행 중이어야 합니다.

    npm run demo-password

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 자습서 데모 암호 재설정을 클릭합니다. 

### <a name="actions"></a>작업

모델에는 사용자가 일반적인 암호 문제를 해결하는 데 유용한 작업 세트가 포함되어 있습니다.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>학습 대화 상자

또한 모델에는 도메인 클래스 학습 범위를 벗어나 설명하는 일부 항목을 비롯한 몇 가지 학습 대화 상자가 포함되어 있습니다. 주행 방향을 요청하는 사용자를 예로 들 수 있습니다. 샘플 봇은 설명 목적에 대한 몇 가지 항목을 학습되었으므로 간단히 “해결할 수 없습니다”라고 응답합니다. 기존 학습 대화 상자 목록은 왼쪽 패널의 “학습 대화 상자”에 있습니다.

![](../media/tutorial_pw_reset_entities.PNG)

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. “메시지를 입력하세요...”가 표시된 채팅 패널에 “암호를 잃어버렸습니다”를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. ‘로컬 계정 또는 Microsoft 계정 중 무엇인가요?’ 응답을 선택합니다.
5. “메시지를 입력하세요...”가 표시된 채팅 패널에 “로컬 계정이요”를 입력합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. ‘사용 중인 Windows 버전은 무엇인가요?’ 응답을 선택합니다.
8. “메시지를 입력하세요...”가 표시된 채팅 패널에 “windows xp”를 입력합니다.
9. “작업 점수 매기기” 단추를 클릭합니다.
10. “+작업” 단추를 클릭합니다.
11. “봇의 응답...” 필드에 “해결 방법: Windows XP에서 암호를 재설정하는 방법”을 입력합니다.
12. “만들기” 단추를 클릭합니다.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>도메인 외 시나리오에 대한 학습 대화 상자

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, 기존의 “장난감 매장” 학습 대화 상자를 클릭합니다.
2. 채팅 패널에서 “장난감 매장” 발화를 클릭합니다.
3. “대체 입력 추가...” 필드에 “웹 검색”을 입력하고 Enter 키를 누릅니다.
4. “대체 입력 추가...” 필드에 “항공편 예약”을 입력하고 Enter 키를 누릅니다.
5. “변경 내용 저장” 단추를 클릭합니다.
6. “편집 저장” 단추를 클릭합니다.
7. 왼쪽 패널에서 “로그 대화 상자”를 클릭한 다음, “새 로그 대화 상자” 단추를 클릭합니다.
8. “메시지를 입력하세요...”가 표시된 채팅 패널에 “암호를 찾을 수 없습니다”를 입력합니다.
9. “메시지를 입력하세요...”가 표시된 채팅 패널에 “Microsoft 계정”을 입력합니다.
10. “메시지를 입력하세요...”가 표시된 채팅 패널에 “고맙습니다”를 입력합니다.
11. “테스트 완료” 단추를 클릭합니다.
12. 그리드 보기에서 “암호를 찾을 수 없습니다” 로그 대화 상자를 클릭합니다.
13. 채팅 패널에서 잘못 렌더링된 “해결 방법: Microsoft 계정 암호를 재설정하는 방법” 응답을 클릭합니다.
14. “+작업” 단추를 클릭합니다.
15. “봇의 응답...” 필드에 “천만에요”를 입력합니다.
16. “만들기” 단추를 클릭합니다.
17. “학습 대화로 저장” 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데모 - 피자 주문](./demo-pizza-order.md)
