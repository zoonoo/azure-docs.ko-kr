---
title: Azure Lab Services의 교실 랩-FAQ | Microsoft Docs
description: Azure Lab Services에서 교실 labs에 대 한 일반적인 질문에 대 한 답변을 찾습니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: spelluru
ms.openlocfilehash: 261f337ac72a28bc2e1c74e23731242cc3bd0075
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68572031"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Azure Lab Services의 교실 랩-질문과 대답 (FAQ)
Azure Lab Services에서 교실 labs에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 받으세요. 

## <a name="quotas"></a>할당량

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>사용자 당 또는 주당 할당량 또는 랩의 전체 기간에 대 한 할당량 입니까? 
랩에 대해 설정 하는 할당량은 랩의 전체 기간에 대 한 각 학생에 대해 설정 됩니다. 그리고 [vm의 예약 된 실행 시간은](how-to-create-schedules.md) 사용자에 게 할당 된 할당량에 계산 되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다.  할당량에 대 한 자세한 내용은 [사용자에 대 한 할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)을 참조 하세요.

## <a name="schedules"></a>일정

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>일정이 설정 되 면 랩의 모든 Vm이 자동으로 시작 됩니까? 
아니요. 모든 Vm이 아닙니다. 일정에 따라 사용자에 게 할당 된 Vm만 사용자에 게 할당 되지 않은 Vm이 자동으로 시작 되지 않습니다. 이것은 의도 된 것입니다. 

## <a name="lab-accounts"></a>랩 계정

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>주소 범위를 사용할 수 없기 때문에 랩을 만들 수 없는 이유는 무엇입니까? 
교실 랩에서는 Azure Portal에서 랩 계정을 만들 때 지정 하는 IP 주소 범위 내에서 랩 Vm을 만들 수 있습니다. 주소 범위가 제공 되 면 랩 Vm에 대해 512 IP 주소를 할당 한 후 생성 된 각 랩입니다. 랩 계정에 대 한 주소 범위는 랩 계정에서 만들려는 랩을 모두 수용할 수 있을 만큼 커야 합니다. 

예를 들어/19-10.0.0.0/19 블록이 있는 경우이 주소 범위는 8192 IP 주소 및 16 개 랩 (8192/512 = 16 labs)을 수용 합니다. 이 경우 17 일 lab 만들기에서 랩 만들기가 실패 합니다.

## <a name="blog-post"></a>블로그 게시물
[Azure Lab Services 블로그](https://azure.microsoft.com/blog/tag/azure-lab-services/)를 구독 합니다.

## <a name="update-notifications"></a>업데이트 알림
[Lab Services 업데이트](https://azure.microsoft.com/updates/?product=lab-services) 를 구독 하 여 DevTest Labs의 새로운 기능에 대 한 최신 정보를 받을 수 있습니다.

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문이 여기에 나열 되지 않은 경우 알려 주시기 바랍니다. 답변을 찾을 수 있습니다.

- 이 FAQ의 끝에 질문을 게시합니다. 
- 더 광범위한 대상에 도달하기 위해 [Azure DevTest Labs MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)에 질문을 게시하세요. 
- 기능 요청의 경우 요청 내용과 아이디어를 [Azure DevTest Labs 사용자 의견](https://feedback.azure.com/forums/320373-azure-devtest-labs)으로 제출해 주세요.

