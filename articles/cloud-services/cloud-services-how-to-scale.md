---
title: "클래식 포털에서 클라우드 서비스 크기 자동 조정 | Microsoft Docs"
description: "(클래식) 클래식 포털을 사용하여 Azure에서 클라우드 서비스 웹 역할 또는 작업자 역할에 대한 자동 크기 조정 규칙을 구성하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: c7fb1c796556baf09a34c4bc6ed71964071d5874
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017

---

# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>클래식 포털에서 클라우드 서비스 크기 자동 조정을 구성하는 방법
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-scale-portal.md)
> * [Azure 클래식 포털](cloud-services-how-to-scale.md)

Azure 클래식 포털의 크기 조정 페이지에서 웹 역할 또는 작업자 역할에 대한 자동 크기 조정 설정을 구성할 수 있습니다. 또는 규칙 기반 자동 크기 조정 대신 수동 크기 조정을 구성할 수 있습니다.

> [!NOTE]
> 이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 중점을 둡니다. 가상 컴퓨터(클래식)를 직접 만든 경우 이 가상 컴퓨터는 클라우드 서비스에서 호스트됩니다. 이 정보 중 일부는 이러한 유형의 가상 컴퓨터에 적용됩니다. 가상 컴퓨터 가용성 집합의 크기 조정은 사용자가 구성한 크기 조정 규칙에 따라 설정 및 해제됩니다. 가상 컴퓨터와 가용성 집합에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

응용 프로그램의 크기 조정을 구성하기 전에 다음 내용을 고려해야 합니다.

* 크기 조정은 코어 사용량의 영향을 받습니다.

    역할 인스턴스가 클수록 더 많은 코어를 사용합니다. 응용 프로그램의 크기는 구독에 대한 코어 제한 내에서만 조정할 수 있습니다. 예를 들어 구독의 코어 제한이 20이라고 해보겠습니다. 중간 크기의 클라우드 서비스 두 대에서 응용 프로그램을 실행할 경우(총 코어 수 4개), 구독에 있는 다른 클라우드 서비스 배포를 나머지 16코어까지만 확장할 수 있습니다. 크기에 대한 자세한 내용은 [클라우드 서비스 크기](cloud-services-sizes-specs.md)를 참조하세요.

* 메시지 임계값을 기반으로 응용 프로그램의 크기를 조정하려면 큐를 만든 후 역할에 연결해야 합니다. 자세한 내용은 [큐 저장소 서비스를 사용하는 방법](../storage/storage-dotnet-how-to-use-queues.md)(영문)을 참조하세요.

* 클라우드 서비스에 연결되는 리소스의 크기를 조정할 수 있습니다. 리소스를 연결하는 방법에 대한 자세한 내용은 [방법: 클라우드 서비스에 리소스 연결](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service)을 참조하세요.

* 응용 프로그램의 가용성을 높이려면 응용 프로그램이 두 개 이상의 역할 인스턴스와 함께 배포되는지 확인해야 합니다. 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## <a name="schedule-scaling"></a>크기 조정 예약
기본적으로 모든 역할은 특정 일정을 따르지 않습니다. 따라서 변경된 모든 설정은 연중 모든 날짜 및 모든 시간에 적용됩니다. 필요한 경우 다음 모드 중 하나에 대해 수동 또는 자동 크기 조정을 설정할 수 있습니다.

* 평일
* 주말
* 주중 야간
* 주중 오전
* 특정 날짜
* 특정 날짜 범위

이 일정 설정은 [Azure 클래식 포털](https://manage.windowsazure.com/)(  
**클라우드 서비스** > **\[클라우드 서비스\]** > **크기** > **\[프로덕션 또는 스테이징\]** 페이지)에서 구성됩니다.

변경하려는 각 역할에 대해 **예약 시간 설정** 단추를 클릭합니다.

![일정에 따라 클라우드 서비스 자동 크기 조정][scale_schedules]

## <a name="manual-scale"></a>수동 크기 조정
**크기 조정** 페이지에서 클라우드 서비스에서 실행 중인 인스턴스의 수를 수동으로 늘리거나 줄일 수 있습니다. 이 설정은 사용자가 만든 각 일정에 대해 구성되거나 일정을 만들지 않은 경우 모든 시간으로 구성됩니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.
   
   > [!TIP]
   > 클라우드 서비스가 보이지 않는 경우 **프로덕션**에서 **준비**로 변경하거나 그 반대로 변경해야 할 수 있습니다.

2. **크기 조정**을 클릭합니다.
3. 크기 조정 옵션을 변경할 일정을 선택합니다. 정의된 일정이 없는 경우 *예약된 시간 없음*이 기본값입니다.
4. **메트릭별 크기 조정** 섹션을 찾아서 **없음**을 선택합니다. 이 설정은 모든 역할의 기본 설정입니다.
5. 클라우드 서비스의 각 역할에는 사용할 인스턴스 수를 변경하는 데 사용되는 슬라이더가 있습니다.
   
    ![클라우드 서비스 역할 크기 수동 조정][manual_scale]
   
    더 많은 인스턴스가 필요한 경우 [클라우드 서비스 가상 컴퓨터 크기](cloud-services-sizes-specs.md)를 변경해야 할 수 있습니다.
6. **Save**를 클릭합니다.  
   선택 사항을 기반으로 역할 인스턴스가 추가되거나 제거됩니다.

> [!TIP]
> ![][tip_icon]가 표시될 때마다 여기로 마우스를 이동하면 특정 설정의 기능에 대한 도움말을 확인할 수 있습니다.

## <a name="automatic-scale---cpu"></a>자동 크기 조정 - CPU
이 모드는 평균 CPU 사용률이 지정된 임계값을 초과하거나 임계값 아래로 떨어질 경우 크기를 조정합니다. 이 경우 역할 인스턴스가 만들어지거나 삭제됩니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.
   
   > [!TIP]
   > 클라우드 서비스가 보이지 않는 경우 **프로덕션**에서 **준비**로 변경하거나 그 반대로 변경해야 할 수 있습니다.

2. **크기 조정**을 클릭합니다.
3. 크기 조정 옵션을 변경할 일정을 선택합니다. 정의된 일정이 없는 경우 *예약된 시간 없음*이 기본값입니다.
4. **메트릭별 크기 조정** 섹션을 찾아서 **CPU**를 선택합니다.
5. 이제 역할 인스턴스의 최소 및 최대 범위, 대상 CPU 사용량(확장 트리거), 확장 및 축소할 인스턴스 수를 구성할 수 있습니다.

![CPU 로드에 따라 클라우드 서비스 역할 크기 조정][cpu_scale]

> [!TIP]
> ![][tip_icon]가 표시될 때마다 여기로 마우스를 이동하면 특정 설정의 기능에 대한 도움말을 확인할 수 있습니다.

## <a name="automatic-scale---queue"></a>자동 크기 조정 - 큐
이 모드는 큐의 메시지 수가 지정된 임계값을 초과하거나 임계값 아래로 떨어질 경우 자동으로 크기를 조정합니다. 이 경우 역할 인스턴스가 만들어지거나 삭제됩니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.
   
   > [!TIP]
   > 클라우드 서비스가 보이지 않는 경우 **프로덕션**에서 **준비**로 변경하거나 그 반대로 변경해야 할 수 있습니다.

2. **크기 조정**을 클릭합니다.
3. **메트릭별 크기 조정** 섹션을 찾아서 **큐**를 선택합니다.
4. 이제 역할 인스턴스의 최소 및 최대 범위, 각 인스턴스에 대해 처리할 큐 및 큐 메시지 수, 확장 및 축소할 인스턴스 수를 구성할 수 있습니다.

![메시지 큐에 따라 클라우드 서비스 역할 크기 조정][queue_scale]

> [!TIP]
> ![][tip_icon]가 표시될 때마다 여기로 마우스를 이동하면 특정 설정의 기능에 대한 도움말을 확인할 수 있습니다.

## <a name="scale-linked-resources"></a>연결된 리소스 크기 조정
역할의 크기를 조정할 때 응용 프로그램에 사용 중인 데이터베이스의 크기도 조정하는 것이 좋습니다. 데이터베이스를 클라우드 서비스에 연결한 경우 적절한 링크를 클릭하여 해당 리소스에 대한 크기 조정 설정에 액세스할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하여 대시보드를 엽니다.
   
   > [!TIP]
   > 클라우드 서비스가 보이지 않는 경우 **프로덕션**에서 **준비**로 변경하거나 그 반대로 변경해야 할 수 있습니다.

2. **크기 조정**을 클릭합니다.
3. **연결된 리소스** 섹션을 찾아서 **이 데이터베이스에 대한 크기 조정 관리**를 클릭합니다.
   
   > [!NOTE]
   > **연결된 리소스** 섹션이 보이지 않는 경우 연결된 리소스가 없는 것일 수 있습니다.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png

