---
title: Azure Stack의 storage 계정 | Microsoft Docs
description: Azure Stack 저장소 계정을 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: mabrigg
ms.openlocfilehash: ae6539900e201f0559d998ad2d9be24c39d42e3b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713496"
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack의 Storage 계정
Storage 계정에는 Blob 및 Table service, 그리고 저장소 데이터 개체에 대한 고유한 네임스페이스가 있습니다. 기본적으로 계정에 대한 데이터는 저장소 계정 소유자에만 사용할 수 있습니다.

1. Azure Stack POC 컴퓨터에 로그인 `https://adminportal.local.azurestack.external` 으로 [관리자로](azure-stack-connect-azure-stack.md)를 클릭 하 고 **+ 리소스 만들기** > **데이터 + 저장소**  >  **저장소 계정**합니다.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 에 **저장소 계정 만들기** 블레이드에서 저장소 계정의 이름 입력 합니다. 새 **리소스 그룹**, 또는 기존 항목을 선택한 다음 클릭 **만들기** storage 계정을 만들려면.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 새 저장소 계정이 표시 하려면 클릭 **모든 리소스**, 저장소 계정에 대 한 다음 검색 하 고 해당 이름을 클릭 합니다.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>다음 단계
[Azure 리소스 관리자 템플릿 사용](user/azure-stack-arm-templates.md)

[Azure storage 계정에 알아봅니다](../storage/common/storage-create-storage-account.md)

[Azure Stack Azure consistent Storage 유효성 검사 가이드 다운로드](http://aka.ms/azurestacktp1doc)
