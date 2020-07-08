---
title: 장애 조치 (failover) 및 재해 복구 Video Indexer
titleSuffix: Azure Media Services
description: 지역 데이터 센터 오류 또는 재해가 발생 하는 경우 보조 Video Indexer 계정으로 장애 조치 (failover) 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79499613"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>장애 조치 (failover) 및 재해 복구 Video Indexer

Azure Media Services Video Indexer는 지역 데이터 센터 중단 또는 오류로 인 한 서비스의 즉각적인 장애 조치 (failover)를 제공 하지 않습니다. 이 문서에서는 장애 조치 (failover)를 위해 환경을 구성 하 여 앱에 대 한 최적의 가용성을 보장 하 고 재해가 발생 하는 경우 복구 시간을 최소화 하는 방법을 설명

Azure의 격리 및 가용성 정책을 활용 하려면 지역 쌍에서 BCDR (비즈니스 연속성 재해 복구)를 구성 하는 것이 좋습니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독 Azure 구독이 아직 없는 경우 [azure 무료 평가판](https://azure.microsoft.com/free/)에 등록 합니다.

## <a name="failover-to-a-secondary-account"></a>보조 계정으로 장애 조치 (Failover)

BCDR를 구현 하려면 중복성을 처리 하기 위해 두 개의 Video Indexer 계정이 있어야 합니다.

1. Azure에 연결 된 두 개의 Video Indexer 계정 ( [Video Indexer 계정 만들기](connect-to-azure.md)참조)을 만듭니다. 주 지역에 대 한 계정과 쌍을 이루는 azure 지역에 대 한 계정을 만듭니다.
1. 주 지역에 오류가 있는 경우 보조 계정을 사용 하 여 인덱싱으로 전환 합니다.

> [!TIP]
> [서비스 알림에 대 한 활동 로그 경고 만들기에](../../service-health/alerts-activity-log-service-notifications.md)따라 서비스 상태 알림에 대 한 활동 로그 경고를 설정 하 여 BCDR를 자동화할 수 있습니다.

다중 테 넌 트 사용에 대 한 자세한 내용은 [다중 테 넌 트 관리](manage-multiple-tenants.md)를 참조 하세요. BCDR를 구현 하려면 [테 넌 트 당 계정](manage-multiple-tenants.md#video-indexer-account-per-tenant) 또는 [테 넌 트 당 Azure 구독](manage-multiple-tenants.md#azure-subscription-per-tenant)의 두 Video Indexer 옵션 중 하나를 선택 합니다.

## <a name="next-steps"></a>다음 단계

[Azure에 연결 된 Video Indexer 계정 관리](manage-account-connected-to-azure.md)
