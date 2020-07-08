---
title: Azure Lab Services의 용량 제한
description: Azure Lab Services의 용량 제한 (가상 머신 제한)에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 92bdc714d70b3d73ca2cbc76b1f5dc5366582cbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444100"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Azure Lab Services의 용량 제한
Azure에는 azure 계산 할당량 제한을 준수 하 고 사기를 완화 하기 위해 Azure 구독에 대 한 기본 용량 제한이 Azure Lab Services 있습니다. 모든 Azure 구독에는 초기 용량 한도가 있으며,이는 구독 유형, 표준 계산 코어 수, Azure Lab Services 내에서 사용 가능한 GPU 코어에 따라 달라질 수 있습니다. 제한 증가를 요청 하기 전에 랩 내에서 만들 수 있는 가상 컴퓨터 수를 제한 합니다.  

구독의 가상 컴퓨터 코어 한도에 도달 하거나이에 도달 하면 추가 가상 컴퓨터를 만드는 작업을 수행 하려고 할 때 Azure Lab Services의 메시지가 표시 됩니다. 예를 들어: 

- 랩 만들기
- 랩 게시
- 랩 용량을 조정 하 여 기존 랩에 가상 컴퓨터를 더 추가 합니다.

이러한 작업은 이미 코어 제한에 도달한 경우에도 사용 하지 않도록 설정할 수 있습니다. 

![코어 제한-경고 메시지](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>기본 제한이 0 코어 인 구독
사기에 일반적으로 사용 되는 일부 드문 구독 유형은 기본 제한인 0 표준 코어와 0 개의 GPU 코어를 사용할 수 있습니다. 이러한 구독 유형 중 하나를 사용 하는 경우 랩 계정을 만드는 관리자는 Azure Lab Services를 사용 하기 전에 한도 증가를 요청 해야 합니다. 

관리자는 다음 단계에 따라 제한 증가를 요청할 수 있습니다.  

1.  구독에서 [랩 계정을 만듭니다](tutorial-setup-lab-account.md).
2.  랩 계정의 **개요** 페이지에서 위쪽의 **요청 제한 증가** 단추를 클릭 합니다. 
3.  양식의 단계를 따라 지원 요청을 제출 하 여 제한을 늘립니다.

## <a name="request-a-limit-increase"></a>제한 증가 요청
코어 한도에 도달 하면 Azure Lab Services를 계속 사용 하도록 제한 증가를 요청할 수 있습니다. 요청 프로세스는 사기 행위 또는 의도 하지 않은 갑작스러운 대규모 배포의 경우 구독이 관여 하지 않도록 하기 위한 검사점입니다.

Azure Lab Services 포털의 가상 컴퓨터 코어 제한에 대 한 메시지에는 한도 증가를 요청 하는 링크가 포함 되어 있습니다. 링크를 누르면 새 지원 요청을 만들 수 있는 새 브라우저 탭이 열립니다. 다음 그림에 표시 된 것 처럼 문제 유형, 구독 및 할당량 유형 정보는 자동으로 입력 됩니다. 

![새 지원 요청](./media/capacity-limits/new-support-request.png)


그런 다음 제한 증가에 대 한 추가 정보를 제공 하 라는 메시지가 표시 됩니다. **설명** 필드에 다음 세부 정보를 제공 합니다.

- 수행 하려는 작업 (예: 컴퓨터 과학 클래스를 학습 하 고 해커 톤를 실행 하는 랩 만들기)
- 이 랩에 사용 하는 가상 머신 크기
- 필요한 가상 컴퓨터 수

지원 요청을 제출 하면 요청을 검토 하 게 됩니다. 필요한 경우 추가 정보를 얻기 위해 연락 드리겠습니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조 하세요.
- [질문과 대답](classroom-labs-faq.md)