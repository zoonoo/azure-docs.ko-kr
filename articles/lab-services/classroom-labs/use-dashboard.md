---
title: Azure 랩 서비스의 강의실 랩에 대시보드 사용 | 마이크로 소프트 문서
description: Azure Lab 서비스의 강의실 랩에 대시보드를 사용하는 방법을 알아봅니다.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538943"
---
# <a name="dashboard-for-classroom-labs"></a>교실 실습을 위한 대시보드
이 문서에서는 Azure Lab Services의 강의실 랩의 대시보드 보기에 대해 설명합니다. 

![대시보드](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>비용 및 청구 타일
이 타일은 다음과 같은 비용 추정 세부 정보를 제공합니다.

| 설정 | 값 | 
| ------- | ----- | 
| 할당량 시간 | 사용자가 예약된 시간 이외에 VM을 사용할 수 있는 최대 시간입니다. |
| 예정된 시간 | 랩에 설정된 일정에 따라 발생하는 시간입니다. 이 값은 모든 일정 이벤트에 시작/날짜 집합이 있는 경우에만 사용할 수 있습니다. |
| 시간/사용자 | 할당량 시간 및 예약된 시간의 합계입니다. |
| 최대 사용자 | 모든 가상 시스템을 기반으로 하는 랩의 최대 사용자 수입니다. |
| 시간 x 사용자 | 시간/사용자에게 사용자 수를 곱한 값입니다. |
| 조정된 할당량 | 특정 사용자에게 추가된 할당량 시간의 합계입니다. |
| 총 시간 * 시간 당 | 선택한 VM 크기를 기준으로 시간당 비용입니다. 이것은 당신이 가격을 갈 때 일반 급여를 기반으로합니다. |
| 총 예상 비용 | 현재 설정을 기반으로 이 랩의 최대 가격입니다. |

## <a name="template-tile"></a>템플릿 타일
이 타일에는 다음 정보가 표시됩니다.

- 템플릿이 만들어진 날짜 
- 템플릿이 마지막으로 게시된 날짜 

또한 클래스에 대한 [템플릿 VM을 관리할](how-to-create-manage-template.md) 수 있는 **템플릿** 페이지로 이동하는 링크도 있습니다. 

## <a name="virtual-machine-pool-tile"></a>가상 시스템 풀 타일

이 타일에는 다음 정보가 표시됩니다.

- 학생(사용자)에게 할당된 가상 컴퓨터 수
- 아직 학생에게 할당되지 않은 가상 시스템 수

또한 랩에서 가상 컴퓨터 **풀을** [관리할](how-to-set-virtual-machine-passwords.md) 수 있는 가상 컴퓨터 풀 페이지로 이동하는 링크도 있습니다. 

## <a name="users-tile"></a>사용자 타일

이 타일에는 다음 정보가 표시됩니다.

- 클래스에 등록된 사용자 수
- 랩에 추가되었지만 클래스에 등록되지 않은 사용자 수 

또한 랩의 [사용자를 관리할](how-to-configure-student-usage.md) 수 있는 **사용자** 페이지로 이동하는 링크도 있습니다. 

## <a name="schedules-tile"></a>일정 타일
타일에 랩의 현재 예약된 이벤트가 표시됩니다. 또한 일정을 만들고 관리할 수 있는 **일정** 페이지로 이동하는 링크도 [있습니다.](how-to-create-schedules.md) 타일에는 예약된 두 이벤트에 대한 세부 정보와 랩의 나머지 예약된 이벤트 수가 표시됩니다. 

![예약된 이벤트](../media/use-dashboard/scheduled-events.png)

