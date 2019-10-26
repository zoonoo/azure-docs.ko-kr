---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central를 사용 하 여 장치를 연결 하 고 정상 상태로 유지 하는 방법에 대 한 요약을 제공 합니다.
author: aaronbjork
ms.author: abjork
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 7ce5ba37763bc78740f5f07f860fdc4b620b474a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954657"
---
# <a name="stay-connected-with-azure-iot-central"></a>Azure IoT Central와 연결 된 상태로 유지

이 문서에서는 Azure IoT Central 장치를 연결 하 고 정상 상태로 유지 하는 방법에 대 한 개요를 제공 합니다.

규모에 맞게 작동 하도록 설계 된 모든 IoT 솔루션을 사용 하 여 장치 관리에 대 한 잘 구성 된 접근 방식을 고려해 야 합니다. 장치를 "연결" 하는 것 만으로는 충분 하지 않습니다. 솔루션의 진화, 성장 및 연령에 따라 장치를 연결 하 고 정상 상태로 유지 하는 방법이 필요 합니다. Azure IoT Central는 응용 프로그램 수명 주기 동안 IoT 솔루션의 장치가 잘 확인 개체만 수 있도록 하는 데 필요한 기능을 제공 합니다.

## <a name="dashboards"></a>대시보드 
기본 제공 [대시보드](howto-manage-devices.md#import-devices) 는 장치 상태 및 원격 분석을 모니터링 하기 위한 사용자 지정 가능한 노출 영역을 제공 합니다. [응용 프로그램 템플릿에서](howto-use-app-templates-pnp.md) 미리 작성 된 대시보드를 시작 하거나 응용 프로그램의 요구에 맞게 고유한 대시보드를 만듭니다. 대시보드는 응용 프로그램의 모든 사용자와 공유 하거나 비공개로 유지할 수 있습니다.

## <a name="rules-and-actions"></a>규칙 및 동작 
장치 상태 및 원격 분석을 기반으로 [사용자 지정 규칙](howto-create-event-rules.md) 을 빌드하여 주의가 필요한 장치를 신속 하 게 식별 합니다. 적절 한 시기에 정정 조치를 취할 수 있도록 적절 한 사용자에 게 알리는 [작업](howto-create-event-rules.md#configure-actions) 을 구성 합니다.

## <a name="jobs"></a>교육 
[작업](howto-run-a-job.md) 을 사용 하면 장치 속성, 설정 및 명령에 대 한 단일 또는 대량 업데이트를 수행할 수 있습니다. 

## <a name="user-roles-and-permissions"></a>사용자 역할 및 사용 권한
[역할 및 사용 권한은](howto-manage-users-roles-pnp.md) 각 사용자의 경험을 조정할 수 있는 기능을 제공 합니다. UI를 통해 직접 역할을 만들고 적절 한 사용 권한을 쉽게 할당 합니다. 




