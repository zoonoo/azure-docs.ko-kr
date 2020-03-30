---
title: 지원되는 Azure PaaS 리소스에 대한 Azure 보안 센터 기능입니다.
description: 이 페이지에는 지원되는 Azure PaaS 리소스에 대한 Azure 보안 센터 기능의 가용성이 표시됩니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209364"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Azure PaaS 서비스에 대한 기능 적용 범위<a name="paas-services"></a>

아래 표는 지원되는 Azure PaaS 리소스에 대한 Azure 보안 센터 기능의 가용성을 보여 주며 있습니다.

|서비스|권장 사항(무료)|보안 경고(표준)|취약성 평가(표준)|
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
|Azure Data Lake Storage|✔|-|-|
|MySQL용 Azure 데이터베이스*|✔|✔|-|
|포스트그레SQL용 Azure 데이터베이스*|✔|✔|-|
|Azure 이벤트 허브 네임스페이스|✔|-|-|
|Azure Functions 앱|✔|-|-|
|Azure Key Vault|✔|✔ *|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL Database|✔|✔|✔|
|Azure 서비스 버스 네임스페이스|✔|-|-|
|Azure 서비스 패브릭 계정|✔|-|-|
|Azure Storage 계정|✔|-|-|
|Azure Stream Analytics|✔|-|-|
|Azure 구독|✔ **|✔|-|
|Azure Virtual Network</br> (서브넷, NIC 및 네트워크 보안 그룹 포함)|✔|-|-|

\*이러한 기능은 현재 미리 보기에서 지원됩니다.

\*\*Azure Active Directory(Azure AD) 권장 사항은 표준 구독에만 사용할 수 있습니다.