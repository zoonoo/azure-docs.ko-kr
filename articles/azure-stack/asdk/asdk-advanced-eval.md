---
title: Azure Stack 평가 작업을 고급 | Microsoft Docs
description: 이 문서에서는 고급 Azure Stack 평가 작업을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: d00eaf0ab24fbd754ba5ad2c79e9630d69d28eb7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200516"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>고급 Azure Stack 개발 키트 평가 작업
기본 Azure Stack 개발 키트 (ASDK) 서비스 기능 및 기능에 알고 나면 있습니다 수 파악해 보세요 추가 Azure Stack의 고급 시나리오를 테스트 하 여 합니다. 이러한 고급 평가 작업은 Azure Stack 연산자 설명서에서 완벽 하 게 설명 되어 있습니다.

> [!NOTE]
> ASDK 및 다중 노드 Azure Stack 배포의 경우 프로덕션 환경에 대 한 다양 한 연산자 작업 지원 되지만 ASDK 배포에 대 한 모든 사용 시나리오는 지원 됩니다. 참조 [ASDK 및 다중 노드 Azure Stack 차이점](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) 자세한 내용은 합니다.

## <a name="delegate-offers-in-azure-stack"></a>Azure Stack에서 제안 위임
Azure Stack 운영자로 제품을 만들고 사용자를 등록 해야 다른 사용자를 배치 하려는 경우가 많습니다. 예를 들어, 서비스 공급자 인 경우 재판매인 고객을 등록 하 고 사용자를 대신해 관리에 좋습니다. 또는 기업에서 중앙 IT 그룹에 속하지 인 경우에 사용자 개입 없이 사용자를 등록 하는 자회사를 할 수 있습니다.

[Azure Stack에서 제품 위임](../azure-stack-delegated-provider.md) 연결 하 고 직접 수 있는 것 보다 더 많은 사용자를 관리 하는 데 수 있도록 하 여 이러한 작업을 사용 하 여 도움이 됩니다.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Azure Stack 사용자에 게 SQL database를 사용할 수 있도록
Azure Stack 운영자로 서 사용자에 게는 제품을 만들 수 있습니다 (테 넌 트)는 클라우드 네이티브 앱, 웹사이트 및 워크 로드를 사용 하 여 사용할 수 있는 SQL database를 만듭니다. 이러한 사용자 지정, 주문형, 클라우드 기반 데이터베이스를 사용자에 게 제공 하 여 저장할 수 시간 및 리소스입니다.

SQL Server 리소스 공급자 어댑터를 사용 하 여 [Azure Stack 사용자에 게 SQL database를 사용할 수 있도록](../azure-stack-tutorial-sql-server.md) 서비스로 Azure Stack의 합니다. 리소스 공급자를 설치한 후 하나 이상의 SQL Server 인스턴스에 연결 합니다.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>웹 및 API apps를 Azure Stack 사용자에 게 사용할 수 있도록
Azure Stack 운영자로 서 사용자에 게는 제품을 만들 수 있습니다 (테 넌 트) Azure Functions 및 웹 API 응용 프로그램을 만듭니다. 이러한 주문형, 클라우드 기반 앱에 대 한 액세스를 사용자에 게 제공 하 여 저장할 수 시간 및 리소스입니다.

App Service 리소스 공급자를 배포 [웹 및 API apps를 Azure Stack 사용자에 게 사용할 수 있도록](../azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>다음 단계

[Azure Stack 통합 시스템을 사용 하 여 서비스를 제공 하는 방법에 대 한 자세한 정보](../azure-stack-offer-services-overview.md)
