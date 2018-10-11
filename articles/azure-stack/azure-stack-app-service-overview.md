---
title: 'App Service 개요: Azure Stack | Microsoft Docs'
description: Azure Stack의 App Service의 개요
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079160"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 개요에 대한 App Service

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack의 azure App Service는 Azure Stack에 사용할 수 있는 Microsoft Azure의 플랫폼-as a service (PaaS) 제품입니다. 서비스를 사용 하면 내부 또는 외부 고객-있습니다-웹, API 및 Azure Functions 만들기는 모든 플랫폼 또는 장치에 대 한 응용 프로그램입니다. 온-프레미스 응용 프로그램을 사용 하 여 앱을 통합할 수 있으며 해당 비즈니스 프로세스를 자동화. Azure Stack 클라우드 운영자는 선택한 공유 VM 리소스 또는 전용된 Vm을 사용 하 여 완전히 관리 되는 virtual machines (Vm)에서 고객 앱을 실행할 수 있습니다.

Azure App Service를 사용 하면 비즈니스 프로세스 및 호스팅 클라우드 Api 자동화할 수 있습니다. 단일 통합 서비스로 Azure App Service 결합할 수-웹 사이트, RESTful Api 및 비즈니스 프로세스-다양 한 구성 요소를 단일 솔루션입니다.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Azure Stack에서 Azure App Service를 제공 하는 이유

다음은 App Service의 몇 가지 주요 기능입니다.

- **여러 언어 및 프레임 워크**: App Service에 ASP.NET, Node.js, Java, PHP 및 Python에 대 한 최고 수준의 지원 합니다. 또한 App Service Vm에서 Windows PowerShell 및 기타 스크립트 또는 실행 파일을 실행할 수 있습니다.
- **DevOps 최적화**: 지속적인 통합 및 로컬 Git, GitHub, BitBucket을 사용 하 여 배포를 설정 합니다. 테스트 및 스테이징 환경을 통해 업데이트를 승격 합니다. Azure PowerShell 또는 플랫폼 간 명령줄 인터페이스 (CLI)를 사용 하 여 App Service에서 앱을 관리 합니다.
- **Visual Studio 통합**: Visual Studio의 전용된 도구를 만들고 응용 프로그램 배포 작업을 간소화 합니다.

## <a name="app-types-in-app-service"></a>App Service의 앱 유형

App Service는 각각 특정 워크 로드를 호스트 하기 위한 여러 가지 앱 유형을 제공 합니다.

- [웹 앱](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) 웹 사이트와 웹 응용 프로그램을 호스트 합니다.
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) RESTful Api 호스팅에 대 한 합니다.
- 이벤트 기반의 서버 리스 워크 로드 호스팅을 위한 azure 함수입니다.

여기 word 앱 워크 로드 실행을 전담 하는 호스팅 리소스를 가리킵니다. 예를 들어 "웹앱"의 경우 함께 브라우저에 대한 기능을 제공하는 계산 리소스와 응용 프로그램 코드를 모두 웹앱으로 생각하는 것이 익숙할 것입니다. 하지만 App Service에서 웹 앱은 Azure Stack 응용 프로그램 코드를 호스팅하기 위해 제공 하는 계산 리소스입니다.

다른 종류의 여러 App Service 앱의 응용 프로그램을 구성할 수 있습니다. 예를 들어, 응용 프로그램은 웹 프런트 엔드를 구성 하 고는 RESTful API 백 엔드를 수행할 수 있습니다.

- 프런트 엔드와 api를 단일 웹앱에 배포합니다.
- 프런트 엔드 코드를 웹앱에 배포하고 백 엔드 코드를 API 앱에 배포합니다.

   ![모니터링 데이터를 사용 하 여 app Service 개요](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>App Service 계획의 정의

App Service 리소스 공급자는 Azure App Service를 사용 하는 동일한 코드를 사용 합니다. 결과적으로, 몇 가지 일반적인 개념은 설명 하는 것이 좋습니다. App Service에서 응용 프로그램에 대 한 가격 책정 컨테이너 App Service 계획을 이라고 합니다. 앱을 포함 하는 데 사용 되는 전용된 가상 컴퓨터의 집합을 나타냅니다. 지정된 된 구독 내에서 여러 App Service 계획 할 수 있습니다.

Azure에서 공유 및 전용 작업 자가 있습니다. 공유 작업자 고밀도 다중 테 넌 트 앱 호스팅, 지원 하며 공유 작업자 집합을 하나만 있습니다. 전용된 서버는 하나의 테 넌 트에서 사용 및 세 가지 크기: 소형, 중형 및 대형입니다. 온-프레미스 고객의 요구는 항상 해당 용어를 사용 하 여 설명할 수 없습니다. 리소스 공급자 관리자는 Azure Stack에서 App Service에서 사용할 수 있는 작업을 수행 하려는 작업자 계층을 정의할 수 있습니다. 고유한 호스팅 요구에 따라 여러 집합이 공유 작업자 또는 다른 전용된 작업자 집합을 정의할 수 있습니다. 해당 작업자 계층 정의 사용 하 여 정의할 수 있습니다 다음 자신의 가격 책정 Sku입니다.

## <a name="portal-features"></a>포털 기능

Azure App Service를 사용 하 고 동일한 UI를 사용 하는 Azure Stack에서 app Service, 백 엔드를 사용 하 여 마찬가지입니다. 그러나 일부 기능이 비활성화 되 고 Azure Stack에서 작동 되지 않습니다. 이러한 기능을 필요로 하는 서비스나 Azure 관련 요구 사항에는 Azure Stack에서 현재 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 App Service를 사용 하 여 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)
- [App Service 리소스 공급자를 설치 합니다.](azure-stack-app-service-deploy.md)

도 사용해 볼 수 있습니다 다른 [서비스 (PaaS) 플랫폼](azure-stack-tools-paas-services.md)와 같은 합니다 [SQL Server 리소스 공급자](azure-stack-sql-resource-provider-deploy.md) 하며 [MySQL 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md).
