---
title: 예약된 유지 관리 - Azure Database for MySQL – 유연한 서버
description: 이 문서에서는 Azure Database for MySQL - 유연한 서버의 예약된 유지 관리 기능을 설명합니다.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a2e99440a7c8f33eee9d3c9fe2276ac3868ff4b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91331763"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL에서 예약된 유지 관리 – 유동 서버

Azure Database for MySQL - 유연한 서버는 관리형 데이터베이스를 안전하고 안정적으로, 또 최신 상태로 유지하기 위해 정기적으로 유지 관리를 수행합니다. 유지 관리 중에 서버는 새로운 기능, 업데이트, 패치를 가져옵니다.

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 미리 보기로 제공됩니다.

## <a name="select-a-maintenance-window"></a>유지 관리 기간 선택

특정 요일 및 해당일의 시간대에서 유지 관리를 예약할 수 있습니다. 또는 시스템이 자동으로 요일과 시간을 선택하도록 할 수도 있습니다. 어느 쪽이든 시스템은 유지 관리 실행 5일 전에 알림을 보냅니다. 또한 시스템은 유지 관리가 시작되고 완료된 시점을 알립니다.

다음을 통해 임박한 예약된 유지 관리에 대한 알림을 받을 수 있습니다.

* 특정 주소로 메일 전송
* Azure Resource Manager 역할로 메일 전송
* 모바일 디바이스로 SMS(문자 메시지) 전송
* Azure 앱에 알림으로 푸시
* 음성 메시지로 전달

유지 관리 일정에 대한 기본 설정을 지정하는 경우 요일 및 기간을 선택할 수 있습니다. 지정하지 않으면 시스템이 서버의 지역 시간에서 오후 11시에서 오전 7시 사이의 시간을 선택합니다. Azure 구독의 유연한 서버마다 다른 일정을 정의할 수 있습니다.

> [!IMPORTANT]
> 일반적으로 서버에 대해 성공적으로 예약된 유지 관리 이벤트 사이의 간격은 30일 이상입니다.
>
> 그러나 심각한 취약성과 같은 중요한 응급 업데이트의 경우 알림 기간은 5일이 안 될 수 있습니다. 최근 30일 동안 예약된 유지 관리가 성공적으로 수행된 경우에도 중요 업데이트가 서버에 적용될 수 있습니다.

일정 설정은 언제든지 업데이트할 수 있습니다. 유연한 서버의 유지 관리가 예약되어 있고 일정 기본 설정을 업데이트하는 경우 현재 이벤트는 예약된 대로 진행되고 일정 설정 변경은 성공적으로 완료될 때 적용됩니다.

드문 경우지만 시스템에서 유지 관리 이벤트를 취소하거나 성공적으로 완료하지 못할 수도 있습니다. 이 경우 시스템은 취소 또는 실패한 유지 관리 이벤트에 대한 알림을 각각 만듭니다. 현재 일정 설정을 기준으로 다음 유지 관리가 예약되고 5일 전에 알림을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

* [유지 관리 일정을 변경](how-to-maintenance-portal.md)하는 방법을 알아봅니다.
* Azure Service Health를 사용하여 [예정된 유지 관리에 대한 알림을 받는](../../service-health/service-notifications.md) 방법을 알아봅니다.
* [임박한 예약된 유지 관리 이벤트에 대한 경고를 설정](../../service-health/resource-health-alert-monitor-guide.md)하는 방법을 알아봅니다.
