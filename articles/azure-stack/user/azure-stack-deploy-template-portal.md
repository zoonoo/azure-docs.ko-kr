---
title: Azure Stack에서 포털을 사용 하 여 템플릿 배포 | Microsoft Docs
description: Azure Stack 포털을 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 931c3d8beb9f2ed12228c74f09f84bbdee1798b8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139631"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack 포털을 사용하여 템플릿 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에 Azure Resource Manager 템플릿을 배포 하려면 포털을 사용할 수 있습니다.

## <a name="to-deploy-a-template"></a>템플릿을 배포 하려면

1. 포털에 로그인 **새로 만들기**를 선택한 후 **사용자 지정**합니다.
2. 선택 **템플릿 배포**합니다.
3. 선택 **템플릿 편집**, JSON 템플릿 코드의 코드 창에 붙여넣습니다. **저장**을 선택합니다.
4. 선택 **매개 변수 편집**에 표시 되 고 선택한 매개 변수의 값을 제공할 **확인**합니다.
5. 선택 **구독**합니다. 를 사용 하 고 선택 하려는 구독을 선택 **확인**합니다.
6. 선택 **리소스 그룹**합니다. 기존 리소스 그룹을 선택 하거나 새 대시보드를 만들고, 선택한 **확인**합니다.
7. **만들기**를 선택합니다. 대시보드의 새 타일은 템플릿 배포의 진행 상태를 추적합니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
