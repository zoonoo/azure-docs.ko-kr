---
title: Azure Spring Cloud의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878699"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud의 할당량 및 서비스 계획

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

모든 Azure 서비스에서는 리소스 및 기능에 대한 기본 한도와 할당량을 설정합니다.   Azure Spring Cloud는 기본 및 표준의 두 가지 가격 책정 계층을 제공합니다. 두 계층에 대한 제한은 이 문서에서 자세히 설명됩니다.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 스프링 클라우드 서비스 계층 및 제한

| 리소스 | 범위 | Basic | Standard
------- | ------- | -------
vCPU | 앱 인스턴스당 | 1 | 4
메모리 | 앱 인스턴스당 | 2GB | 8GB
Azure Spring Cloud 서비스 인스턴스 | 구독당 지역별 | 10 | 10
총 앱 인스턴스 | Azure Spring Cloud 서비스 인스턴스별 | 25 | 500
사용자 지정 도메인 | Azure Spring Cloud 서비스 인스턴스별 | 0 | 25 
영구적 볼륨 | Azure Spring Cloud 서비스 인스턴스별 | 1GB/앱 x 앱 10개 | 50GB/앱 x 앱 10개

> [!TIP]
> 서비스 인스턴스당 총 앱 인스턴스에 대해 나열된 제한은 중지됨 상태를 포함하여 모든 상태의 앱 및 배포에 적용됩니다. 사용하지 않는 앱이나 배포를 삭제하십시오.

## <a name="next-steps"></a>다음 단계

몇 가지 기본 제한은 늘릴 수 있습니다. 설정을 증량해야 할 경우 [지원 요청을 만듭니다](../azure-portal/supportability/how-to-create-azure-support-request.md).
