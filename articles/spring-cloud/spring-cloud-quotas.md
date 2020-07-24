---
title: Azure 스프링 클라우드의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089467"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 할당량 및 서비스 계획

모든 Azure 서비스는 리소스 및 기능에 대 한 기본 제한 및 할당량을 설정 합니다.   Azure 스프링 클라우드는 기본 및 표준 이라는 두 가지 가격 책정 계층을 제공 합니다. 이 문서의 두 계층에 대 한 제한이 자세히 설명 됩니다.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 스프링 클라우드 서비스 계층 및 제한

| 리소스 | Basic | Standard
------- | ------- | -------
vCPU | 서비스 인스턴스당 1 개 | 서비스 인스턴스당 4 개
메모리 | 서비스 인스턴스당 2gb | 서비스 인스턴스당 8gb
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10 | 10
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 25 | 500
영구적 볼륨 | 1GB/앱 x 10 앱 | 50 g b/앱 x 10 앱


Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계층만 제공 합니다. 한도에 도달 하면 다음과 같은 오류 400 메시지가 표시 됩니다. "할당량은 *Azure 스프링 클라우드 서비스가 생성 된 지역 지역* *에서 구독 구독에* 대 한 제한을 초과 합니다.

## <a name="next-steps"></a>다음 단계

몇 가지 기본 제한을 늘릴 수 있습니다. 설정에 대 한 증가가 필요한 경우 [지원 요청을 만듭니다](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
