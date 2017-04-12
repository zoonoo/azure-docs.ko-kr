---
title: "웹앱 리소스를 다른 리소스 그룹으로 이동"
description: "하나의 리소스 그룹에서 다른 리소스 그룹으로 웹앱 및 앱 서비스를 이동할 수 있는 시나리오를 설명합니다."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 078833a21cd309087b842e21e3fc730695c254dd
ms.openlocfilehash: 89fdeca4ffc1a36e8aff829b00483f208fa03e0f
ms.lasthandoff: 12/21/2016


---
# <a name="supported-move-configurations"></a>지원되는 이동 구성
[Resource Manager Move Resources API](../azure-resource-manager/resource-group-move-resources.md)를 사용하여 Azure Web App 리소스를 이동할 수 있습니다.

Azure 웹앱은 현재 다음 이동 시나리오를 지원합니다.

* 리소스 그룹(웹앱, 앱 서비스 계획 및 인증서)의 전체 내용을 다른 리소스 그룹으로 이동합니다. 
   > [!Note]
   > 대상 리소스 그룹은 이 시나리오에서 모든 Microsoft.Web 리소스를 포함할 수 없습니다.

* 여전히 자신의 현재 앱 서비스 계획(앱 서비스 계획은 이전 리소스 그룹에 유지)에서 호스트하는 동안 다른 리소스 그룹으로 개별 웹앱을 이동



