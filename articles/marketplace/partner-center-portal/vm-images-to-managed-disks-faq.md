---
title: Azure 마켓플레이스에서 관리되는 디스크 저장소로 가상 머신(VM) 이미지를 이동하고 있습니다.
description: 새로운 마켓플레이스 기능 및 기능에 대한 보다 빠르고 안정적인 스토리지 및 지원을 제공하기 위해 마켓플레이스 VM 이미지를 관리형 디스크 스토리지로 이전하고 있습니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285115"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Azure 마켓플레이스에서 가상 머신(VM) 이미지를 관리디스크 저장소로 이동하고 있습니다.

새로운 마켓플레이스 기능 및 기능에 대한 보다 빠르고 안정적인 스토리지 및 지원을 제공하기 위해 마켓플레이스 VM 이미지를 관리형 디스크 스토리지로 이전하고 있습니다.

2020년 1월 2일부터 VM 이미지를 단계적으로 관리되는 디스크 저장소로 이동합니다. 첫 번째 단계에서는 이전 90일 동안 새 배포나 실행 중인 VM이 없는 이미지만 이동합니다. 이미지를 이동하기 전에 게시자가 어떤 이미지가 이동하고 이동할 지 알려주는 이메일을 보냅니다.

게시자 나 소비자는 어떤 조치를 취할 필요가 없습니다, 그리고 사용자는 영향을받지 않습니다. 마켓플레이스 오퍼는 계속 사용할 수 있으며 고객은 이동 중 및 이동 후에도 이러한 이미지에서 관리되는 VM을 배포할 수 있습니다.

궁금한 점이 있으시면 [저희에게 연락하십시오.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>FAQ

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>내 VM 이미지 사용자가 중단을 경험할까요?

VM 이미지 사용자는 중단이 발생하지 않습니다. 

첫 번째 단계에서는 실행 중인 VM이 없는 VM 이미지만 이동합니다. 이러한 이미지에 대한 사용자가 없기 때문에 아무런 영향도 미치지 않습니다. 후속 단계의 경우 사용자에게도 영향을 미치지 않습니다.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>프로세스가 완료되는 데 얼마나 걸립니까?

마이그레이션을 완료하는 데 최대 24시간이 걸릴 수 있습니다.

### <a name="do-i-need-to-take-any-action"></a>어떤 조치를 취해야 하나요?

아니요. 게시자 나 소비자는 어떤 조치를 취할 필요가 없습니다.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>관리되는 디스크 저장소로 이동한 후 다른 방식으로 클라우드 포털 API를 호출하도록 시스템을 업데이트해야 합니까?

아니요. 기존 API 호출은 계속 작동합니다.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>모든 VM 이미지를 관리 디스크로 동시에 이동하시겠습니까?

우리는 같은 날에 모든 VM 이미지를 이동합니다. 이동이 완료되면 알려드리겠습니다.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>나중에 VM 이미지의 이동을 예약하도록 요청할 수 있습니까?

예정된 날짜에 이미지를 이동하는 것이 좋습니다. 그러나 문제가 있는 경우 당사에 연락하여 이동 일정을 조정하십시오.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>이동하는 동안 VM 이미지에 업데이트를 게시할 수 있습니까?

이동 하는 동안 VM 이미지에 대 한 업데이트를 만들 수 없습니다.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>VM 이미지를 관리디스크로 이동한 후 게시 프로세스가 변경됩니다.

아니요, 게시 프로세스는 동일하게 유지됩니다. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>게시자가 오퍼를 관리 디스크로 이동할 수 있습니까?

아니요, 게시자는 오퍼를 관리 디스크로 이동할 수 없습니다. 그들은 기다려야하고 이미지가 자동으로 이동됩니다. 변경하기 전에 게시자에게 알림을 보내드립니다.
