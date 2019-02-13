---
title: Azure Stack에서 서비스를 제공 합니다. | Microsoft Docs
description: 클라우드 운영자로 서 사용자에 게 서비스를 제공할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 4deb72eae7dffac6eabb34b18a9e879ac1fd8113
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179963"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure Stack에서 서비스 제공 개요

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

[Microsoft Azure Stack](azure-stack-poc.md) 는 데이터 센터에서 서비스를 제공할 수 있는 하이브리드 클라우드 플랫폼입니다. 서비스 공급자, 테 넌 트에 서비스를 제공할 수 있습니다. 비즈니스 또는 정부 기관에서 직원에 게 온-프레미스 서비스를 제공할 수 있습니다. 

제공할 수 있습니다 [Infrastructure as a Service](https://azure.microsoft.com/overview/what-is-iaas/) 주문형 컴퓨팅 인프라를 구축할 수 있도록 하는 (IaaS) 서비스를 프로 비전 및 Azure Stack 사용자 포털에서 관리 합니다.

배포할 수도 있습니다 [Platform-as-a-service](https://azure.microsoft.com/overview/what-is-paas/) Microsoft 및 기타 타사 공급자 로부터 Azure Stack에 대 한 서비스 (PaaS)입니다. 배달할 수 있는 서비스 포함 되지만로 제한 되지 않습니다.

- [Azure Stack에 App Service 리소스 공급자 추가](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [Azure Stack에 SQL Server 리소스 공급자 추가](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Azure Stack에 MySQL Server 리소스 공급자 추가](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


서비스를 통합 하 고 다른 사용자에 대 한 복잡 한 솔루션을 구축할 통합할 수도 있습니다.

이러한 서비스 사용자에 게 전달할 만들어야 [계획, 제품 및 할당량](azure-stack-plan-offer-quota-overview.md)합니다. 그런 다음 사용자에 게 서비스를 사용 하 여 제품을 구독할 수 있습니다.

## <a name="plan-your-service-offers"></a>서비스 제품 계획

제품을 계획할 때는 다음 사항을 염두에서에 둡니다.

**평가판**: 그런 다음 추가 서비스를 업그레이드할 수 있는 새 사용자를 유치에 평가판을 사용할 수 있습니다. 평가판 제품을 만들려면 작은 [기본 계획](azure-stack-plan-offer-quota-overview.md#base-plan) 큰 선택적 추가 기능 계획 합니다.

**용량 계획**: 사용자 가져오기 대용량 리소스 및 모든 사용자를 위해 시스템을 막 히기 때문에 대 한 우려 수 있습니다. 성능을 위해 수 있습니다 [계획에 할당량을 구성할](azure-stack-plan-offer-quota-overview.md#plans) cap 사용 합니다.

**공급자 위임**: 사용자 환경에서 만드는 기능을 제공 하는 다른 사용자가 부여할 수 있습니다. 예를 들어, 서비스 공급자 인 경우 있습니다 [대리자](azure-stack-delegated-provider.md) 여 재판매인이 기능입니다. 또는 조직 라면 다른 부서/자회사에 위임할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack에서 제품 만들기](azure-stack-create-offer.md)
