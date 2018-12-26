---
title: Azure App Service - 동기화 네트워크 구성 | Microsoft Docs
description: 이 아티클에서는 Azure App Service 호스팅 계획에 대한 네트워크 구성을 동기화하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163173"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Azure App Service 호스팅 계획에 대한 네트워킹 구성 동기화

[Azure Virtual Network와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)했더라도 발생할 수 있습니다. Managed Instance에 연결을 설정할 수 없습니다. 시도할 수 있는 방법은 서비스 계획에 대한 네트워킹 구성을 새로 고치는 것입니다. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>App Service 호스팅 계획에 대한 네트워킹 구성 동기화

이렇게 하려면 다음 단계를 수행하세요.  

1. 웹앱 App Service 계획으로 이동합니다.
 
   ![App Service 계획](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. **네트워킹**을 클릭한 다음, **관리하려면 여기 클릭**을 클릭합니다.
 
   ![서비스 계획 관리](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. **VNet**을 선택하고 **네트워크 동기화**를 클릭합니다. 
 
   ![네트워크 동기화](./media/sql-database-managed-instance-sync-networking/sync.png)

4. 동기화가 완료될 때까지 대기합니다.
  
   ![동기화 완료](./media/sql-database-managed-instance-sync-networking/sync-done.png)

이제 Managed Instance에 연결을 다시 설정할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- Managed Instance에 VNet을 구성하는 방법에 대한 정보는 [Managed Instance VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.
