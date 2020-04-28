---
title: Azure 스프링 클라우드의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278884"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 할당량 및 서비스 계획

모든 Azure 서비스는 리소스 및 기능에 대 한 기본 제한 및 할당량을 설정 합니다.  Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계획만 제공 합니다.

이 문서에서는 현재 미리 보기 기간 중에 제공 되는 서비스 할당량에 대해 자세히 설명 합니다.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 스프링 클라우드 서비스 계층 및 할당량

Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계층만 제공 합니다.

리소스 | Amount
------- | -------
vCPU | 서비스 인스턴스당 4 개
메모리 | 서비스 인스턴스당 8gb
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 500
스프링 응용 프로그램당 총 앱 인스턴스 수 | 20
영구적 볼륨 | 10x50GB

할당량에 도달 하면 다음과 같은 오류 400 메시지가 표시 됩니다. "할당량은 *Azure 스프링 클라우드 서비스가 생성 된 지역 지역* *에서 구독 구독에* 대 한 제한을 초과 합니다.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스를 증가 시켜야 하는 경우 [지원 요청을 만듭니다](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
