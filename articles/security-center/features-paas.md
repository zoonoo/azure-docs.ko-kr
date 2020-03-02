---
title: 지원 되는 Azure PaaS 리소스에 대 한 기능을 Azure Security Center 합니다.
description: 이 페이지에는 지원 되는 Azure PaaS 리소스에 대 한 Azure Security Center 기능의 가용성이 표시 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 85460724071347a768a924f34290a75d2383b4c0
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209364"
---
# Azure PaaS 서비스에 대 한 기능 검사<a name="paas-services"></a>

다음 표에서는 지원 되는 Azure PaaS 리소스에 대 한 Azure Security Center 기능의 가용성을 보여 줍니다.

|서비스|권장 사항 (무료)|보안 경고 (표준)|취약성 평가 (표준)|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure Automation 계정|✔|-|-|
|Azure Batch 계정|✔|-|-|
|Azure Blob Storage|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure Cloud Services|✔|-|-|
|Azure Cognitive Search|✔|-|-|
|Azure Container Registry|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Azure 데이터 레이크 분석|✔|-|-|
|Azure Data Lake 스토리지|✔|-|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Event Hubs 네임 스페이스|✔|-|-|
|Azure Functions 앱|✔|-|-|
|Azure Key Vault|✔|✔ *|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL 데이터베이스|✔|✔|✔|
|Azure Service Bus 네임 스페이스|✔|-|-|
|Azure Service Fabric 계정|✔|-|-|
|Azure Storage 계정|✔|-|-|
|Azure Stream Analytics|✔|-|-|
|Azure 구독|✔ **|✔|-|
|Azure Virtual Network</br> (포함, Nic 및 네트워크 보안 그룹)|✔|-|-|

\* 이러한 기능은 현재 미리 보기에서 지원 됩니다.

Azure AD (\*\* Azure Active Directory 권장 사항은 표준 구독에 대해서만 사용할 수 있습니다.