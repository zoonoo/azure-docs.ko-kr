---
title: Azure App Service에 대 한 네트워크 구성 동기화
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure App Service 호스팅 계획에 대 한 네트워크 구성을 Azure SQL Managed Instance와 동기화 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695456"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance를 사용 하 여 Azure App Service 호스팅 계획에 대 한 네트워킹 구성 동기화
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[앱을 Azure Virtual Network와 통합](../../app-service/web-sites-integrate-with-vnet.md)한 경우에도 SQL Managed Instance에 대 한 연결을 설정할 수 없습니다. 서비스 계획에 대 한 네트워킹 구성을 새로 고치거 나 동기화 하면이 문제를 해결할 수 있습니다. 

## <a name="sync-network-configuration"></a>네트워크 구성 동기화 

이렇게 하려면 다음 단계를 수행하세요.  

1. 웹앱 App Service 계획으로 이동합니다.

   ![App Service 계획의 스크린샷](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. **네트워킹** 을 선택 하 고 **관리 하려면 여기를 클릭**하세요 .를 선택 합니다.

   ![서비스 계획 관리 스크린샷](./media/azure-app-sync-network-configuration/manage-plan.png)

3. **VNet**을 선택하고 **네트워크 동기화**를 클릭합니다.

   ![동기화 네트워크의 스크린샷](./media/azure-app-sync-network-configuration/sync.png)

4. 동기화가 완료될 때까지 대기합니다.
  
   ![동기화 완료 스크린샷](./media/azure-app-sync-network-configuration/sync-done.png)

이제 SQL Managed Instance에 대 한 연결을 다시 설정 하려고 할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance 용 VNet을 구성 하는 방법에 대 한 자세한 내용은 [sql Managed Instance vnet 아키텍처](connectivity-architecture-overview.md) 및 [기존 vnet을 구성 하는 방법](vnet-existing-add-subnet.md)을 참조 하세요.
