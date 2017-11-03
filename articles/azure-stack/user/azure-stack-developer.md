---
title: "Azure 스택에 대 한 앱을 개발 | Microsoft Docs"
description: "Azure 스택 프로토타입 응용 프로그램의 개발 고려 사항에 알아봅니다"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>Azure Stack에 대한 개발
시작할 수 있는 현재 응용 프로그램을 개발 Azure 스택 환경에 액세스할 수 없는 경우에 합니다. Azure 스택 데이터 센터에서 실행 되는 Microsoft Azure 서비스를 제공, 때문에 Azure와 마찬가지로 Azure 스택에 대해 개발 하기 비슷한 도구와 프로세스를 사용할 수 있습니다.  준비 및 다음 항목의 지침의 비트를 Azure 스택 환경 에뮬레이션 하기 위해 Azure를 사용할 수 있습니다.

* Azure에도 Azure 스택에 배포 될 수 있는 Azure 리소스 관리자 템플릿을 만들 수 있습니다.  참조 [템플릿 고려 사항](azure-stack-develop-templates.md) 이동성을 보장 하 여 템플릿을 개발에 대 한 지침입니다.
* 서비스의 가용성과 Azure 및 Azure 스택 간에 서비스 버전 관리에 델타가 있습니다. 사용할 수는 [Azure 스택 정책 모듈](azure-stack-policy-module.md) 를 Azure 스택에서 사용할 수 있는 Azure 서비스 가용성 및 리소스 유형을 제한 합니다. 사용 가능한 서비스를 제한 하면 Azure 스택을 사용할 수 있는 서비스에 의존 하는 응용 프로그램 데 도움이 됩니다.
* [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates) Azure 스택와 Azure에 배포 될 수 있도록 서식 파일을 개발 하는 방법의 일반적인 시나리오 예입니다.


