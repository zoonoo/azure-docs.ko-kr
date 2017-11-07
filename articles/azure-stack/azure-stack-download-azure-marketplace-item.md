---
title: "Azure에서 마켓플레이스 항목을 다운로드 | Microsoft Docs"
description: "내 Azure 스택 배포에서 Azure 마켓플레이스 항목을 다운로드할 수 있습니다."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 marketplace에 포함할 콘텐츠를 결정 하는 대로 Azure 마켓플레이스에서 사용할 수 있는 콘텐츠를 고려해 야 합니다. Azure 스택에서 실행 되도록 검증을 거친 되었던 Azure 마켓플레이스 항목의 큐 레이트 목록에서 다운로드할 수 있습니다. 이 목록에 새 항목 자리 자주, 수 있으므로 새 내용에 대 한 다시 확인 해야 합니다.

마켓플레이스 항목을 다운로드 하려면 먼저 [Azure를 사용한 Azure 스택 등록](azure-stack-register.md)합니다. 

## <a name="download"></a>다운로드
1. Azure 스택 관리자 포털 (https://portal.local.azurestack.external)에 로그인 합니다.
2. 마켓플레이스 항목 일부는 매우 클 수 있습니다.  확인을 클릭 하 여 시스템에 있는 충분 한 공간이 있는지 확인 하십시오 **리소스 공급자** > **저장소**합니다.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. 클릭 **더 많은 서비스** > **마켓플레이스 관리**합니다.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 클릭 **Azure에서 추가** 다운로드할 수 있는 항목의 목록을 볼 수 있습니다. 다운로드 크기 및 설명을 보려면 목록에서 각 항목을 클릭할 수 있습니다.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 클릭 한 다음 목록에서 원하는 항목을 선택 **다운로드**합니다. 선택한 항목에 대 한 VM 이미지를 다운로드 시작 됩니다. 다운로드 시간 달라 집니다.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 다운로드가 완료 되는 Azure 스택 연산자 또는 사용자로 프로그램 새 마켓플레이스 항목을 배포할 수 있습니다. 클릭 **+ 새로 만들기**새 마켓플레이스 항목에 대 한 범주를 검색 하 고 다음 항목을 선택 합니다.
7. 클릭 **만들기** 새로 다운로드 한 항목에 대 한 생성 환경을 엽니다. 에 항목을 배포 하려면 단계별 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

[Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)
