---
title: Visual Studio를 사용하여 Azure Stack에 템플릿 배포 | Microsoft Docs
description: Azure Stack에서 Visual Studio를 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 585f890b11ab71f9c10479ff65aff74922a30ed1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42147054"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Visual Studio를 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에 Azure Resource Manager 템플릿을 배포 하려면 Visual Studio를 사용할 수 있습니다.

## <a name="to-deploy-a-template"></a>템플릿을 배포 하려면

1. Visual Studio를 사용하여 Azure Stack에 [설치하고 연결합니다](azure-stack-install-visual-studio.md).
2. Visual Studio를 엽니다.
3. 선택 **파일**를 선택한 후 **새로 만들기**합니다. **새 프로젝트**를 선택 **Azure 리소스 그룹**합니다.
4. 입력을 **이름** 새 프로젝트 및 선택 **확인**합니다.
5. **Azure 템플릿 선택**, 선택 **Azure Stack 빠른 시작** 드롭 다운 목록에서.
6. 선택 **101-만들기-저장소 계정**를 선택한 후 **확인**합니다.
7. 새 프로젝트를 확장 합니다 **템플릿** 노드에서 **솔루션 탐색기** 사용 가능한 템플릿을 볼 수입니다.
8. **솔루션 탐색기**프로젝트의 이름을 선택한 다음 선택 **배포**합니다. 선택 **새 배포**합니다.
9. **리소스 그룹에 배포**를 사용 합니다 **구독** Microsoft Azure Stack 구독을 선택 하려면 드롭다운 목록입니다.
10. **리소스 그룹** 나열, 기존 리소스 그룹을 선택 하거나 새로 만듭니다.
11. **리소스 그룹 위치** 목록, 위치를 선택한 다음 선택 **배포**합니다.
12. **매개 변수 편집**, (템플릿에 따라 다름) 매개 변수에 대 한 값을 제공 합니다. 선택한 후 **저장**합니다.

## <a name="next-steps"></a>다음 단계

* [명령줄을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
* [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
