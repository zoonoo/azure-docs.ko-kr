---
title: Azure App Service에 대한 네트워킹 구성 동기화
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure App Service 호스팅 계획에 대한 네트워크 구성을 Azure SQL Managed Instance와 동기화하는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/13/2018
ms.openlocfilehash: 69ff59812245d1765b4824c0fa0ce156f1a58d20
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697408"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure App Service 호스팅 계획에 대한 네트워킹 구성을 Azure SQL Managed Instance와 동기화
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure Virtual Network와 앱을 통합](../../app-service/web-sites-integrate-with-vnet.md)했더라도 SQL Managed Instance에 연결을 설정할 수 없는 문제가 발생할 수 있습니다. 서비스 계획에 대한 네트워킹 구성을 새로 고치거나 동기화하면 이 문제를 해결할 수 있습니다. 

## <a name="sync-network-configuration"></a>네트워크 구성 동기화 

이렇게 하려면 다음 단계를 수행하세요.  

1. 웹앱 App Service 계획으로 이동합니다.

   ![App Service 계획 스크린샷](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. **네트워킹** 선택한 다음, **여기를 클릭하여 관리** 를 선택합니다.

   ![서비스 계획 관리 스크린샷](./media/azure-app-sync-network-configuration/manage-plan.png)

3. **VNet** 을 선택하고 **네트워크 동기화** 를 클릭합니다.

   ![동기화 네트워크 스크린샷](./media/azure-app-sync-network-configuration/sync.png)

4. 동기화가 완료될 때까지 대기합니다.
  
   ![동기화 완료 스크린샷](./media/azure-app-sync-network-configuration/sync-done.png)

이제 SQL Managed Instance에 연결을 다시 설정할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 VNet을 구성하는 방법에 대한 자세한 내용은 [SQL Managed Instance VNet 아키텍처](connectivity-architecture-overview.md) 및 [기존 VNet을 구성하는 방법](vnet-existing-add-subnet.md)을 참조하세요.
