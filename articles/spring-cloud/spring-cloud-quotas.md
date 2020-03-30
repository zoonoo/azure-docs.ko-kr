---
title: Azure 스프링 클라우드에 대한 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 자세히 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278884"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드에 대한 할당량 및 서비스 계획

모든 Azure 서비스는 리소스 및 기능에 대한 기본 제한 및 할당량을 설정합니다.  미리 보기 기간 동안 Azure Spring Cloud는 하나의 서비스 요금제만 제공합니다.

이 문서에서는 현재 미리 보기 기간 동안 제공되는 서비스 할당량에 대해 자세히 설명합니다.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 스프링 클라우드 서비스 계층 및 할당량

미리 보기 기간 동안 Azure Spring Cloud는 하나의 서비스 계층만 제공합니다.

리소스 | Amount
------- | -------
vCPU | 서비스 인스턴스당 4개
메모리 | 서비스 인스턴스당 8G바이트
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 500
스프링 응용 프로그램당 총 앱 인스턴스 수 | 20
영구적 볼륨 | 10x50GB

할당량에 도달하면 "할당량이 Azure Spring Cloud 서비스가 생성된 리전 *리전에서* *구독에* 대한 제한을 초과합니다.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스에 증가가 필요한 경우 [지원 요청을 만듭니다.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
