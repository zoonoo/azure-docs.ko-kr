---
title: Azure Stack 용 앱 개발 | Microsoft Docs
description: Azure Stack에서 프로토타입 응용 프로그램에 대 한 개발 고려 사항
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: a6f89f9a7e5960e4749c14fc9a4adb648f6781f4
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139422"
---
# <a name="develop-for-azure-stack"></a>Azure Stack에 대한 개발

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

있습니다 수 응용 프로그램 개발 시작 현재 Azure Stack 환경에 액세스할 수 없는 경우에 합니다. 데이터 센터에서 실행 되는 Microsoft Azure 서비스를 제공 하는 Azure Stack, 때문에 Azure와 마찬가지로 Azure Stack에 대해 개발에 유사한 도구 및 프로세스를 사용할 수 있습니다. 일부 준비 및 지침을 사용 하 여 다음 항목에서 사용 하 여 Azure Stack 환경을 에뮬레이트하는 데 Azure를 사용할 수 있습니다.

* Azure에서 Azure Stack에 배포 가능한 Azure Resource Manager 템플릿을 만들 수 있습니다. 참조 [템플릿 고려 사항](azure-stack-develop-templates.md) 이식성을 보장 하는 템플릿 개발에 대 한 지침에 대 한 합니다.
* 서비스 가용성 및 서비스 버전 관리 Azure와 Azure Stack 간에 차이가 있습니다. 사용할 수는 [Azure Stack 정책 모듈](azure-stack-policy-module.md) Azure Stack에서 사용 가능한 Azure 서비스 가용성 및 리소스 유형을 제한 합니다. Azure Stack에 사용할 수 있는 서비스 응용 프로그램 사용 확인 서비스를 제한 합니다.
* 합니다 [Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) 은 Azure Stack 및 Azure에 배포할 수 있는 템플릿을 개발 하는 방법을 보여 주는 일반 시나리오 예입니다.
