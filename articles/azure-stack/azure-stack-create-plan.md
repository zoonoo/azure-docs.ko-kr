---
title: "Azure 스택에서 계획 만들기 | Microsoft Docs"
description: "클라우드 관리자는 가상 컴퓨터를 프로 비전 하는 구독자 수 있는 계획을 만듭니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5eefca3541ae9f73514f80b0f8df9e5027600f87
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack에서 계획 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

[계획](azure-stack-key-features.md)은 하나 이상의 서비스에 대한 그룹화입니다. 공급자 사용자에 게 제공 하는 계획을 만들 수 있습니다. 차례로 사용자가 구독할 계획 및 여기에 포함 하는 서비스를 사용 하 여 제공 합니다. 이 예에서는 계산, 네트워크 및 저장소 리소스 공급자를 포함 하는 계획을 만드는 방법을 보여 줍니다. 이 계획 구독자가 가상 컴퓨터를 프로 비전 하는 기능을 제공 합니다.

1. Azure 스택 관리자 포털 (https://adminportal.local.azurestack.external)에 로그인 합니다. 5 단계 중에 만든 계정에 대 한 자격 증명을 입력에서 [PowerShell 스크립트를 실행](azure-stack-run-powershell-script.md) 섹션.

2. 계획 및 사용자가 구독할 수 있는 제공 서비스를 만들려면 **새로** > **테 넌 트 제공 + 계획** > **계획**합니다.

   ![](media/azure-stack-create-plan/image01.png)
3. 에 **새 계획** 블레이드에서 입력 **표시 이름** 및 **리소스 이름**합니다. 표시 이름은는 사용자에 게 표시 하는 계획의 이름입니다. 관리자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 계획 작업을 하는 데 사용하는 이름입니다.

   ![](media/azure-stack-create-plan/image02.png)
4. 새 **리소스 그룹**, 또는 계획에 대 한 컨테이너로 기존 템플릿을 선택 합니다.

   ![](media/azure-stack-create-plan/image02a.png)
5. 클릭 **서비스**선택, **Microsoft.Compute**, **Microsoft.Network**, 및 **Microsoft.Storage**, 클릭하고**선택**합니다.

   ![](media/azure-stack-create-plan/image03.png)
6. 클릭 **할당량**, 클릭 **Microsoft.Storage (로컬)**, 기본 할당량을 선택 하거나 클릭 **새 할당량 만들기** 할당량을 사용자 지정할 수 있습니다.

   ![](media/azure-stack-create-plan/image04.png)
7. 새 할당량을 만드는 경우 할당량에 대 한 이름을 입력 > 할당량 값을 설정 > 클릭 **확인** > 새 할당량의 이름을 클릭 합니다.

   ![](media/azure-stack-create-plan/image06.png)
8. 클릭 **Microsoft.Network (로컬)**, 기본 할당량을 선택 하거나 클릭 **새 할당량 만들기** 할당량을 사용자 지정할 수 있습니다.

    ![](media/azure-stack-create-plan/image07.png)
9. 새 할당량을 만드는 경우 할당량에 대 한 이름을 입력 > 할당량 값을 설정 > 클릭 **확인** > 새 할당량의 이름을 클릭 합니다.

    ![](media/azure-stack-create-plan/image08.png)
10. 클릭 **Microsoft.Compute (로컬)**, 기본 할당량을 선택 하거나 클릭 **새 할당량 만들기** 할당량을 사용자 지정할 수 있습니다.

    ![](media/azure-stack-create-plan/image09.png)
11. 새 할당량을 만드는 경우 할당량에 대 한 이름을 입력 > 할당량 값을 설정 > 클릭 **확인** > 새 할당량의 이름을 클릭 합니다.

    ![](media/azure-stack-create-plan/image10.png)
12. 에 **할당량** 블레이드에서 클릭 **확인**, 한 다음는 **새 계획** 블레이드에서 클릭 **만들기** 계획을 만들 합니다.

    ![](media/azure-stack-create-plan/image11.png)
13. 새 계획을 보려면 클릭 **모든 리소스**계획에 대 한 다음 검색 하 고 해당 이름을 클릭 합니다.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>다음 단계
[제품 만들기](azure-stack-create-offer.md)
