---
title: Azure 포털 설정 내보내기 또는 삭제
description: Azure 포털에서 사용자 설정, 개인 대시보드 및 사용자 지정 설정을 내보내거나 삭제하는 방법에 대해 알아봅니다.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900770"
---
# <a name="export-or-delete-user-settings"></a>사용자 설정 내보내기 또는 삭제

Azure 포털의 설정 및 기능을 사용하여 사용자 지정 환경을 만들 수 있습니다. 사용자 지정 설정에 대한 정보는 Azure에 저장됩니다. 다음 사용자 데이터를 내보내거나 삭제할 수 있습니다.

* Azure 포털의 개인 대시보드
* 즐겨찾기 구독 또는 디렉터리, 마지막으로 로그인한 디렉토리와 같은 사용자 설정
* 테마 및 기타 사용자 지정 포털 설정

설정을 삭제하기 전에 내보내고 검토하는 것이 좋습니다. 대시보드를 다시 빌드하거나 사용자 지정 설정을 다시 수행하는 데 시간이 많이 걸릴 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>포털 설정 내보내기 또는 삭제

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. 포털의 헤더에서 설정 ![아이콘](media/azure-portal-export-delete-settings/settings-icon.png) **을 선택합니다.**

1. **모든 설정 내보내기** 또는 **모든 설정 및 프라이빗 대시보드 삭제**를 선택합니다.

    ![Azure 포털 설정 및 설정 옵션](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      다음 표는 이러한 작업에 대해 설명합니다.

      | 작업 | 설명 |
      | --- | --- |
      | **모든 설정 내보내기** | 색상 테마, 즐겨찾기 및 개인 대시보드와 같은 사용자 설정이 포함된 *.json* 파일을 만듭니다.|
      | **모든 설정 및 프라이빗 대시보드 삭제** | 비공개 대시보드 및 포털에 대한 사용자 지정 설정에 대한 모든 링크를 삭제합니다. |

> [!NOTE]
> 사용자 설정의 동적 특성과 데이터 손상의 위험으로 인해 *.json* 파일에서 설정을 가져올 수 없습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [역할 기반 Access Control을 사용하여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)
* [즐겨찾기 추가, 제거 및 재정렬](azure-portal-add-remove-sort-favorites.md)
