---
title: 비디오 인덱서 장애 조치 및 재해 복구
titleSuffix: Azure Media Services
description: 지역 데이터 센터 오류 또는 재해가 발생할 경우 보조 비디오 인덱서 계정으로 장애 조치(failover)하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499613"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>비디오 인덱서 장애 조치 및 재해 복구

Azure 미디어 서비스 비디오 인덱서에서는 지역 데이터 센터 가동 중단 또는 오류가 있는 경우 서비스의 즉각적인 장애 조치(failover)를 제공하지 않습니다. 이 문서에서는 장애 조치(failover)에 대한 환경을 구성하여 앱에 대한 최적의 가용성을 보장하고 재해가 발생할 경우 복구 시간을 최소화하는 방법을 설명합니다.

Azure의 격리 및 가용성 정책의 이점을 누리려면 지역 쌍 간에 비즈니스 연속성 재해 복구(BCDR)를 구성하는 것이 좋습니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독 아직 Azure 구독이 없는 경우 [Azure 무료 평가판에](https://azure.microsoft.com/free/)등록합니다.

## <a name="failover-to-a-secondary-account"></a>보조 계정으로 의 장애 조치(failover)

BCDR을 구현하려면 중복성을 처리하기 위해 두 개의 비디오 인덱서 계정이 있어야 합니다.

1. Azure에 연결된 두 개의 비디오 인덱서 [계정을 만듭니다(비디오 인덱서 계정 만들기](connect-to-azure.md)참조). 기본 영역에 대해 하나의 계정을 만들고 다른 계정을 페어링된 Azure 영역에 만듭니다.
1. 기본 지역에 오류가 있는 경우 보조 계정을 사용하여 인덱싱으로 전환합니다.

> [!TIP]
> 서비스 알림에 대한 활동 로그 알림 만들기에 따라 서비스 상태 알림에 대한 활동 로그 경고를 설정하여 [BCDR을 자동화할](../../service-health/alerts-activity-log-service-notifications.md)수 있습니다.

여러 테넌타 사용에 대한 자세한 내용은 [여러 테넌션 관리를](manage-multiple-tenants.md)참조하십시오. BCDR을 구현하려면 [테넌트당 비디오 인덱서 계정](manage-multiple-tenants.md#video-indexer-account-per-tenant) 또는 [테넌트당 Azure 구독의](manage-multiple-tenants.md#azure-subscription-per-tenant)두 가지 옵션 중 하나를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Azure에 연결된 비디오 인덱서 계정 관리.](manage-account-connected-to-azure.md)
