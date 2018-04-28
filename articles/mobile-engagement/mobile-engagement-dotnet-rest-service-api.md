---
title: REST API를 사용하여 Azure Mobile Engagement 서비스 API에 액세스
description: Mobile Engagement REST API를 사용하여 Azure Mobile Engagement 서비스 API에 액세스하는 방법 설명
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>REST를 사용하여 Azure Mobile Engagement 서비스 API에 액세스
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

Azure Mobile Engagement는 장치, 도달률/푸시 캠페인 등을 관리할 수 있는 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)를 제공합니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/)를 참조하세요.

REST API를 직접 사용하지 않으려는 분들을 위해, 기본 설정 언어에 대한 SDK를 생성하는 도구와 함께 사용할 수 있는 [Swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)을 제공해 드립니다. Swagger 파일에서 SDK를 생성하는 [AutoRest](https://github.com/Azure/AutoRest) 도구를 사용하는 것이 좋습니다. C# 래퍼를 사용하여 이러한 API와 상호 작용할 수 있는 유사한 방식으로 .NET SDK를 만들었으며 인증 토큰 협상을 수행하거나 새로 고칠 필요가 없습니다. API에 대한 .net SDK를 사용하는 방법을 알아보려면 [서비스 API .NET SDK 샘플](mobile-engagement-dotnet-sdk-service-api.md)을 참조하세요.
