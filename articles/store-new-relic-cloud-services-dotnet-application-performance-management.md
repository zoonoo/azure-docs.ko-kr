---
title: "Azure에서 New Relic 사용 | Microsoft Docs"
description: "New Relic 서비스를 사용하여 Azure 응용 프로그램을 관리 및 모니터링하는 방법에 대해 알아봅니다."
services: 
documentationcenter: .net
author: nickfloyd
manager: timlt
editor: 
ms.assetid: b01011be-c344-4e33-987d-c93dac1971fb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2016
ms.author: nickfloyd@newrelic.com
ms.openlocfilehash: f4f13c909a6ff640d403f5264004176c087925dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="new-relic-application-performance-management-on-azure"></a>Azure에서 New Relic 응용 프로그램 성능 관리
Azure 호스티드 응용 프로그램에 세계 최고 수준의 New Relic 성능 모니터링을 추가할 수 있습니다. Azure 앱의 모니터링, 문제 해결 및 튜닝하는 포괄적인 기능과 함께 Azure를 사용하여 New Relic 제품을 할인된 가격으로 사용할 수 있습니다.

## <a name="what-is-new-relic"></a>New Relic 정의
[New Relic 제품](https://newrelic.com/products)을 사용하여 앱 오류를 해결하고 잠재적인 문제에 앞서 전체 환경의 성능을 확인할 수 있습니다. 성능 문제를 확인하고 진단할 때 시간을 절약할 수 있도록 설계되었으며, 성능 문제를 해결하는 데 필요한 정보를 즉시 제공합니다.

New Relic은 서버 및 사용자 브라우저의 웹 트랜잭션에 대한 부하 시간 및 처리량을 추적합니다. 그리고 데이터베이스 사용 시간을 보여 주고, 느린 쿼리 및 웹 요청을 분석하고, 가동 시간 모니터링 및 경고를 제공하고, 응용 프로그램 예외를 추적하는 등의 다양한 기능을 제공합니다. 

## <a name="special-pricing"></a>특별가
New Relic Standard는 Azure 사용자에게 무료입니다. New Relic Pro는 Azure Cloud Services의 인스턴스 크기에 따라 제공됩니다. 가격 책정 정보는 Azure Marketplace에서 [New Relic 페이지](https://azure.microsoft.com/marketplace/partners/newrelic/newrelic/)를 참조하고 엔터프라이즈 수준의 가격은 New Relic(sales@newrelic.com)에 문의하세요.

Azure 고객은 New Relic 에이전트를 배포할 때 New Relic Pro에 대해 2주의 평가판 구독을 받습니다.

## <a name="sign-up-for-new-relic-using-the-azure-store"></a>Azure 스토어를 사용하여 New Relic에 등록
New Relic은 Azure 웹 역할 및 작업자 역할과 원활하게 통합됩니다. 쉽고 빠르게 Azure Store에서 직접 New Relic에 등록할 수 있습니다. 자세한 내용은 New Relic에서 [Azure Store 등록 지침](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services#signup) 을 참조하세요.

## <a name="view-your-data"></a>데이터 보기
일단 등록하면 New Relic의 훌륭한 응용 프로그램 모니터링 및 데이터 중심 분석을 활용할 수 있습니다. New Relic에서 앱의 성능을 확인하려면:

1. Azure Portal에서 관리를 선택합니다.
2. New Relic 계정 전자 메일 및 암호로 로그인합니다.
3. 응용 프로그램 인덱스에서 앱을 선택하여 [APM 개요 페이지](https://docs.newrelic.com/docs/apm/applications-menu/monitoring/apm-overview-page)에서 모든 앱의 데이터를 봅니다.

## <a name="using-new-relic-with-azure"></a>Azure에서 New Relic 사용
New Relic 및 Azure를 사용하는 방법에 대한 자세한 내용은 다음을 비롯한 [New Relic 설명서 사이트](https://docs.newrelic.com/docs/agents/net-agent/azure-installation)를 참조하세요. 

* [.NET용 New Relic](https://docs.newrelic.com/docs/agents/net-agent/getting-started/new-relic-net)
* [Azure 클라우드 서비스에 대한 .NET 작업자 역할 계측](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/instrument-net-worker-role-azure-cloud-service)
* [Microsoft Azure 클라우드 플랫폼용 New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-cloud-services)
* [Microsoft Azure App Services용 New Relic](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-portal)
* [New Relic/Azure 문제 해결](https://docs.newrelic.com/docs/agents/net-agent/azure-troubleshooting)

