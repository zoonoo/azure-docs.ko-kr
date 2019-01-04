---
title: Azure Maps 계정과 키를 관리하는 방법 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Maps 계정을 관리하고 액세스 키를 관리할 수 있습니다.
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 351bc26d6c5b3dcfa9479138f2bd523f64a2912a
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335815"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Azure Maps 계정과 키를 관리하는 방법

Azure Portal을 통해 Azure Maps 계정 및 키를 관리할 수 있습니다. 계정과 키가 있으면 웹 사이트 또는 모바일 응용 프로그램에서 API를 구현할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-new-account"></a>새 계정 만들기

1. [Azure Portal](http://portal.azure.com)에 로그인합니다.

2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

3. **Maps**를 검색하고 선택한 다음 **만들기**를 클릭합니다.

4. 새로운 계정에 대한 정보를 입력합니다.

![포털에서 계정 정보 입력](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>계정 페이지에서 키 관리

계정을 만들고 나면 무작위로 생성된 두 개의 키를 받게 됩니다. 지도 데이터를 검색하거나 새 JavaScript 맵 인스턴스를 만들 때 키를 사용하여 Maps API에 대해 인증합니다.

Azure Portal에서 키를 찾을 수 있습니다. 계정으로 이동한 다음 메뉴에서 **Keys**를 선택합니다.

![포털에서 계정 키 관리](./media/how-to-manage-account-keys/account-keys-portal.png)

이 페이지에서 키를 복사하거나 새 키를 생성할 수 있습니다.

## <a name="delete-an-account"></a>계정 삭제

Azure Portal에서 계정을 삭제할 수 있습니다. 계정 개요 페이지로 이동하여 **삭제**를 선택합니다.

![포털에서 계정 삭제](./media/how-to-manage-account-keys/account-delete-portal.png)

그러면 확인 페이지가 표시됩니다. 계정 이름을 입력하여 계정 삭제를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Maps 계정의 가격 책정 계층을 관리하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [가격 책정 계층 관리](./how-to-manage-pricing-tier.md)

Azure Maps 계정에 대한 API 사용량 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](./how-to-view-api-usage.md)
