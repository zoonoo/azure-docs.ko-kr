---
title: Azure Lab Services의 용량 한도
description: Azure Lab Services의 용량 한도(가상 머신 한도)에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 344ca5950e38d3c4850accdbfb5062a011059919
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294511"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Azure Lab Services의 용량 한도
Azure Lab Services에는 Azure Compute 할당량 한도를 준수하고 사기를 줄이기 위해 Azure 구독에 대한 기본 용량 한도가 있습니다. 모든 Azure 구독에는 초기 용량 한도가 있으며 이는 구독 유형, 표준 컴퓨팅 코어 수, Azure Lab Services 내에서 사용할 수 있는 GPU 코어에 따라 달라질 수 있습니다. 한도 증가를 요청하기 전에 랩 내에서 만들 수 있는 가상 머신 수를 제한합니다.  

구독의 가상 머신 코어 한도에 가깝거나 도달한 경우 추가 가상 머신을 만드는 작업을 수행하려고 할 때 Azure Lab Services 메시지가 표시됩니다. 예를 들면 다음과 같습니다. 

- 랩 만들기
- 랩 게시
- 랩 용량을 조정하여 기존 랩에 더 많은 가상 머신을 추가

이러한 작업은 이미 코어 한도에 도달한 경우에도 사용하지 않도록 설정할 수 있습니다. 

![코어 한도 - 경고 메시지](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>기본 한도가 0 코어인 구독
사기에 더 일반적으로 사용되는 일부 드문 구독 유형은 기본 한도가 0 표준 코어 및 0 GPU 코어입니다. 이러한 구독 유형 중 하나를 사용하는 경우 랩 계정을 만드는 관리자는 Azure Lab Services를 사용하기 전에 한도 증가를 요청해야 합니다. 

관리자는 다음 단계에 따라 한도 증가를 요청할 수 있습니다.  

1.  구독에서 [랩 계정을 만듭니다](tutorial-setup-lab-account.md).
2.  랩 계정의 **개요** 페이지에서 위쪽의 **한도 증가 요청** 단추를 클릭합니다. 
3.  양식의 단계를 따라 지원 요청을 제출하여 한도를 늘립니다.

## <a name="request-a-limit-increase"></a>한도 증가 요청
코어 한도에 도달하면 Azure Lab Services를 계속 사용하기 위해 한도 증가를 요청할 수 있습니다. 요청 프로세스는 사기 행위 또는 의도하지 않은 갑작스러운 대규모 배포의 경우 구독이 관여하지 않도록 하기 위한 검사점입니다.

Azure Lab Services 포털의 가상 머신 코어 한도에 대한 메시지에는 한도 증가를 요청하는 링크가 포함되어 있습니다. 링크를 누르면 새 지원 요청을 만들 수 있는 새 브라우저 탭이 열립니다. 다음 이미지에 표시된 것처럼 문제 유형, 구독, 할당량 유형 정보는 자동으로 입력됩니다. 

![새 지원 요청](./media/capacity-limits/new-support-request.png)


그런 다음, 한도 증가에 대한 추가 정보를 제공하라는 메시지가 표시됩니다. **설명** 필드에 다음 세부 정보를 입력합니다.

- 수행하려는 작업(예: 컴퓨터 과학 수업을 가르치기 위한 랩 만들기, 해커톤 실행 등)
- 해당 랩에 사용하는 가상 머신 크기
- 필요한 가상 머신 수

지원 요청을 제출하면 요청을 검토합니다. 필요한 경우 추가 정보를 얻기 위해 연락드리겠습니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [관리자 가이드 - VM 크기 조정](administrator-guide.md#vm-sizing)
- [질문과 대답](classroom-labs-faq.yml)