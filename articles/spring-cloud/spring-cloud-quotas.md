---
title: Azure 스프링 클라우드의 서비스 계획 및 할당량
description: Azure 스프링 클라우드의 서비스 할당량 및 서비스 계획에 대해 알아보기
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038783"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 스프링 클라우드의 할당량 및 서비스 계획

모든 Azure 서비스는 리소스 및 기능에 대 한 기본 제한 및 할당량을 설정 합니다.  Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계획만 제공 합니다.

이 문서에서는 현재 미리 보기 기간 중에 제공 되는 서비스 할당량에 대해 자세히 설명 합니다.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 스프링 클라우드 서비스 계층 및 할당량

Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계층만 제공 합니다.

리소스 | Amount
------- | -------
vCPU | 4
메모리 | 8gb
Azure 스프링 클라우드 구독 | 1
구독 당 지역별 Azure 스프링 클라우드 서비스 인스턴스 수 | 2
Azure 스프링 클라우드 서비스 인스턴스당 총 앱 인스턴스 수 | 50
스프링 응용 프로그램당 총 앱 인스턴스 수 | 20
영구적 볼륨 | 10 x 50 Gb

할당량에 도달 하면 다음을 읽는 400 오류가 표시 됩니다. "할당량 *은 Azure 스프링 클라우드 서비스를 만든 지역 지역* *에서 구독 구독에* 대 한 제한을 초과 합니다.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스를 증가 시켜야 하는 경우 azure-spring-cloud@service.microsoft.com으로 요청을 보냅니다.
