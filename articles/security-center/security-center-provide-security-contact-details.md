---
title: Azure Security Center의 보안 연락처 세부 정보 제공 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 연락처 세부 정보를 제공하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91446021"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>보안 경고에 대한 이메일 알림 설정 

조직의 올바른 사람이 사용자 환경에서 보안 경고에 대한 알림을 받을 수 있도록 하려면 **이메일 알림** 설정 페이지에서 이메일 주소를 입력합니다.

알림을 설정할 때 특정 개인 또는 구독에 대한 특정 Azure 역할을 가진 사용자에게 이메일을 보내도록 구성할 수 있습니다. 

경고 피로를 방지하기 위해 Security Center는 보내는 메일의 볼륨을 제한합니다. 각 구독에 대해 Security Center는 다음을 보냅니다.

- **높은 심각도** 경고에 대해 하루 최대 **4개**의 이메일
- **중간 심각도** 경고에 대해 하루 최대 **2개**의 이메일
- **낮은 심각도** 경고에 대해 하루 최대 **1개**의 이메일


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="보안 경고에 대한 이메일을 받을 연락처의 세부 정보 구성" :::

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|Free|
|필요한 역할 및 권한:|**보안 관리자**<br>**구독 소유자** |
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 미국 정부(부분)<br>![아니요](./media/icons/no-icon.png) 중국 정부, 기타 정부|
|||


## <a name="set-up-email-notifications-for-alerts"></a>경고에 대한 이메일 알림 설정 <a name="email"></a>

개인 또는 특정 Azure 역할을 가진 모든 사용자에게 이메일 알림을 보낼 수 있습니다.

1. Security Center의 **가격 책정 및 설정** 영역, 관련 구독에서 **이메일 알림**을 선택합니다.

1. 알림의 수신자를 정의합니다.

    - 드롭다운 목록에서 사용 가능한 역할 중 하나를 선택합니다.
    - 또는 특정 이메일 주소를 쉼표로 구분하여 입력합니다. 입력할 수 있는 이메일 주소 수에는 제한이 없습니다.

1. 보안 연락처 정보를 구독에 적용하려면 **저장**을 선택합니다.


## <a name="see-also"></a>참고 항목
보안 알림에 대한 자세한 내용은 다음을 참조하세요.

* [보안 경고 - 참조 가이드](alerts-reference.md) - Azure Security Center의 위협 방지 모듈에서 볼 수 있는 보안 경고에 대해 알아보기
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
* [워크플로 자동화](workflow-automation.md) - 사용자 지정 알림 논리를 사용하여 경고에 대한 응답 자동화