---
title: "Azure 스택에서 테스트 VM 만들기 | Microsoft Docs"
description: "테스트 클라우드 연산자로 스택 Azure에서에서 VM을 프로 비전 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Azure 스택에서 테스트 가상 컴퓨터 만들기

*적용 대상: Azure 스택 개발 키트*

Azure 스택 연산자로 유효성을 검사 하는 테스트 가상 컴퓨터를 만들 수 있습니다 프로그램 [Azure 스택](azure-stack-poc.md) 개발자 키트 배포 합니다.

> [!NOTE]
> 가상 컴퓨터를 프로 비전 수 전에 해야 [Azure 스택 시장에 Windows Server 2016 Evaluation 이미지를 추가](azure-stack-add-default-image.md)합니다.
> 
> 

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
1. Azure 스택 개발 키트 호스트에서 [로그인](azure-stack-connect-azure-stack.md) 관리자 포털에 (`https://adminportal.local.azurestack.external`)를 클릭 하 고 **새로** > **계산**  >  **Windows Server 2016 데이터 센터 Eval** > **만들**합니다.  
2. 에 **기본 사항** 블레이드에서 입력 **이름**, **사용자 이름**, 및 **암호**합니다. **구독**을 선택합니다. 만들기는 **리소스 그룹**, 또는 기존 키를 선택한 다음 클릭 **확인**합니다.  
3. 에 **크기를 선택** 블레이드에서 클릭 **표준 A1**, 클릭 하 고 **선택**합니다.  
4. 에 **설정** 블레이드에서 기본값을 적용 하 고 클릭 **확인**
5. **요약** 블레이드에서 클릭 **확인** 가상 컴퓨터를 만듭니다.  
6. 새 가상 컴퓨터를 보려면 클릭 **모든 리소스**, 다음 가상 컴퓨터를 검색 하 고 해당 이름을 클릭 합니다.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>다음 단계
[관리자 및 사용자 포털을 사용 하 여 Azure 스택](azure-stack-manage-portals.md)
