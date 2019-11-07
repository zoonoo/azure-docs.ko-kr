---
title: Azure Lab Services에서 교실 랩에 대 한 대시보드 사용 Microsoft Docs
description: Azure Lab Services에서 교실 랩에 대 한 대시보드를 사용 하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d1e34a493b747383ce479bcad638098b41e59d2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588188"
---
# <a name="dashboard-for-classroom-labs"></a>교실 labs 대시보드
이 문서에서는 Azure Lab Services의 클래스 룸 랩에 대 한 대시보드 보기에 대해 설명 합니다. 

![대시보드](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>비용 및 청구 타일
이 타일은 다음과 같은 비용 예측 정보를 제공 합니다.

| 설정 | 값 | 
| ------- | ----- | 
| 할당량 시간 | 사용자가 예약 된 시간 외에 VM을 사용할 수 있는 최대 시간입니다. |
| 예약 된 시간 | 랩에서 설정 된 일정에 따라 발생 하는 시간입니다. 이 값은 모든 일정 이벤트에 대해 보낸 날짜/종료 날짜가 설정 된 경우에만 사용할 수 있습니다. |
| 시간/사용자 | 할당량 시간 및 예약 된 시간 합계입니다. |
| 최대 사용자 수 | 요청할 모든 가상 머신을 기반으로 하는 랩의 최대 사용자 수입니다. |
| 시간 x 사용자 | 시간/사용자에 사용자 수를 곱합니다. |
| 조정 됨 할당량 | 특정 사용자에 게 추가 된 할당량 시간의 합계입니다. |
| 총 시간 * $/시간 | 선택한 VM 크기에 따라 시간당 비용입니다. 일반 종 량 제 가격을 기준으로 합니다. |
| 총 예상 비용 | 현재 설정에 따라이 랩에 대 한 최대 가격입니다. |

## <a name="template-tile"></a>템플릿 타일
이 타일에 대 한 다음 정보가 표시 됩니다.

- 템플릿을 만든 날짜 
- 템플릿을 마지막으로 게시 한 날짜 

또한 클래스에 대 한 [템플릿 VM을 관리할](how-to-create-manage-template.md) 수 있는 **템플릿** 페이지로 이동 하는 링크가 있습니다. 

## <a name="virtual-machine-pool-tile"></a>가상 컴퓨터 풀 타일

이 타일에 대 한 다음 정보가 표시 됩니다.

- 학생에 게 할당 된 가상 머신 수 (사용자)
- 학생에 게 아직 할당 되지 않은 가상 머신 수

또한 랩에서 [가상 머신 풀을 관리할](how-to-set-virtual-machine-passwords.md) 수 있는 **가상 머신 풀** 페이지로 이동 하는 링크가 있습니다. 

## <a name="users-tile"></a>사용자 타일

이 타일에 대 한 다음 정보가 표시 됩니다.

- 클래스에 등록 된 사용자 수
- 랩에 추가 되었지만 클래스에 등록 되지 않은 사용자 수 

또한 랩에 대 한 [사용자를 관리할](how-to-configure-student-usage.md) 수 있는 **사용자** 페이지로 이동 하는 링크가 있습니다. 

## <a name="schedules-tile"></a>일정 타일
타일에 랩에 대 한 현재 예약 된 이벤트가 표시 됩니다. 또한 일정을 [만들고 관리할](how-to-create-schedules.md)수 있는 **일정** 페이지로 이동 하는 링크가 있습니다. 타일에는 두 개의 예약 된 이벤트에 대 한 세부 정보 및 랩에 대해 남아 있는 예약 된 이벤트의 수가 표시 됩니다. 

![예약된 이벤트](../media/use-dashboard/scheduled-events.png)

