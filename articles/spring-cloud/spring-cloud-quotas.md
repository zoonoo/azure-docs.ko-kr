---
title: Azure 스프링 클라우드의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711878"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 할당량 및 서비스 계획

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

모든 Azure 서비스는 리소스 및 기능에 대 한 기본 제한 및 할당량을 설정 합니다.   Azure 스프링 클라우드는 기본 및 표준 이라는 두 가지 가격 책정 계층을 제공 합니다. 이 문서의 두 계층에 대 한 제한이 자세히 설명 됩니다.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 스프링 클라우드 서비스 계층 및 제한

| 리소스 | Scope | Basic | Standard
------- | ------- | -------
vCPU | 앱 인스턴스당 | 1 | 4
메모리 | 앱 인스턴스당 | 2GB | 8GB
Azure 스프링 클라우드 서비스 인스턴스 | 구독 당 지역별 | 10 | 10
총 앱 인스턴스 | Azure 스프링 클라우드 서비스 인스턴스 당 | 25 | 500
사용자 지정 도메인 | Azure 스프링 클라우드 서비스 인스턴스 당 | 0 | 25 
영구적 볼륨 | Azure 스프링 클라우드 서비스 인스턴스 당 | 1GB/앱 x 10 앱 | 50 g b/앱 x 10 앱

> [!TIP]
> 서비스 인스턴스당 총 앱 인스턴스에 대해 나열 된 제한은 중지 됨 상태를 포함 하 여 모든 상태의 앱 및 배포에 적용 됩니다. 사용 하지 않는 앱 또는 배포를 삭제 하세요.

## <a name="next-steps"></a>다음 단계

몇 가지 기본 제한을 늘릴 수 있습니다. 설정에 대 한 증가가 필요한 경우 [지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md).
