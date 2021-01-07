---
title: 포함 파일
description: 포함 파일
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "82996247"
---
## <a name="create-a-new-cdn-profile"></a>새 CDN 프로필 만들기

CDN 프로필은 CDN 엔드포인트에 대한 컨테이너이며 가격 책정 계층을 지정합니다.

1. Azure Portal에서 **리소스 만들기**(왼쪽 상단)를 선택합니다. **새로 만들기** 창이 나타납니다.
   
1. **CDN**을 검색하여 선택한 다음, **만들기**를 선택합니다.
   
    ![CDN 리소스 선택](./media/cdn-create-profile/cdn-new-resource.png)

    **CDN 프로필** 창이 나타납니다.

1. 다음 값을 입력합니다.
   
    | 설정  | 값 |
    | -------- | ----- |
    | **이름** | 프로필 이름에 *cdn-profile-123*을 입력합니다. 이 이름은 전역적으로 고유해야 합니다. 이미 사용 중인 경우 다른 이름을 입력합니다. |
    | **구독** | 드롭다운 목록에서 Azure 구독을 선택합니다. |
    | **리소스 그룹** | **새로 만들기**를 선택하고 리소스 그룹 이름에 *CDNQuickstart-rg*를 입력하거나, 이미 그룹이 있는 경우 **기존 그룹 사용**을 선택하고 *CDNQuickstart-rg*를 선택합니다. | 
    | **리소스 그룹 위치** | 드롭다운 목록에서 가까운 위치를 선택합니다. |
    | **가격 책정 계층** | 드롭다운 목록에서 **표준 Akamai** 옵션을 선택합니다. (Akamai 계층의 배포 시간은 약 1분입니다. Microsoft 계층은 약 10분 정도 걸리며 Verizon 계층은 약 90분이 걸립니다.) |
    | **지금 새 CDN 엔드포인트 만들기** | 선택하지 않고 그대로 둡니다. |  
   
    ![새 CDN 프로필](./media/cdn-create-profile/cdn-new-profile.png)

1. **만들기**를 선택하여 프로필을 만듭니다.

