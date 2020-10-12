---
title: Azure Maps Creator 관리
description: 이 문서에서는 Azure Maps Creator를 관리하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83677961"
---
# <a name="manage-azure-maps-creator"></a>Azure Maps Creator 관리

Azure Maps Creator를 이용하면 프라이빗 실내 지도 데이터를 만들 수 있습니다. Azure Maps API 및 실내 맵 모듈을 사용하여 대화형 및 동적 실내 지도 웹 애플리케이션을 개발할 수 있습니다. 현재 Creator는 S1 가격 책정 계층을 사용하는 미국에서만 사용할 수 있습니다.

이 문서에서는 Azure Maps 계정에서 Creator 리소스를 만들고 삭제하는 단계를 안내합니다.

## <a name="create-creator-resource"></a>Creator 리소스 만들기

1. [Azure 포털](https://portal.azure.com)

2. Azure Maps 계정을 선택합니다. **최근 리소스**에 Azure Maps 계정이 표시되지 않으면 Azure Portal 메뉴로 이동합니다. **모든 리소스**를 선택합니다. Azure Maps 계정을 찾아 선택합니다.

    ![Azure Maps 포털 홈페이지](./media/how-to-manage-creator/select-maps-account.png)

3. Azure Maps 계정 페이지에서 **Creator** 아래의 **개요** 옵션으로 이동합니다. **만들기**를 클릭하여 Azure Maps Creator 리소스를 만듭니다.

    ![Azure Maps Creator 만들기 페이지](./media/how-to-manage-creator/creator-blade-settings.png)

4. Creator 리소스에서 이름 및 위치를 입력합니다. 현재 Creator는 미국에서만 지원됩니다. **검토 + 만들기**를 클릭합니다.

   ![Creator 계정 정보 페이지 입력](./media/how-to-manage-creator/creator-creation-dialog.png)

5. 설정을 검토하고 **만들기**를 클릭합니다.

    ![Creator 계정 설정 페이지 확인](./media/how-to-manage-creator/creator-create-dialog.png)

6. 배포가 완료되면 성공 또는 실패 메시지가 포함된 페이지가 표시됩니다.

   ![리소스 배포 상태 페이지](./media/how-to-manage-creator/creator-resource-created.png)

7. **리소스로 이동**을 클릭합니다. Creator 리소스 보기 페이지에는 Creator 리소스의 상태와 선택된 인구 통계 지역이 표시됩니다.

    ![Creator 상태 페이지](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Creator 리소스 페이지에서 Azure Maps 계정을 클릭하여 리소스가 포함된 Azure Maps 계정으로 다시 이동할 수 있습니다.

## <a name="delete-creator-resource"></a>Creator 리소스 삭제

Creator 리소스를 삭제하려면 Azure Maps 계정으로 이동합니다. **Creator**에서 **개요**를 선택합니다. **삭제** 단추를 클릭합니다.

>[!WARNING]
>Azure Maps 계정의 Creator 리소스를 삭제하는 경우 Creator 서비스를 사용하여 만든 데이터 세트, 타일 세트 및 기능 상태 세트도 삭제됩니다.

![삭제 단추가 있는 Creator 페이지](./media/how-to-manage-creator/creator-delete.png)

**삭제** 단추를 클릭하고 작성자 이름을 입력하여 삭제를 확인합니다. 리소스가 삭제되면 아래 이미지와 같이 확인 페이지가 표시됩니다.

![삭제 확인이 있는 Creator 페이지](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>인증

Creator는 IAM(Azure Maps Access Control) 설정을 상속합니다. 데이터 액세스에 대한 모든 API 호출은 인증 및 권한 부여 규칙과 함께 전송되어야 합니다.

Creator 사용량 현황 데이터는 Azure Maps 사용량 차트 및 활동 로그에 통합됩니다.  자세한 내용은 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

## <a name="access-to-creator-services"></a>Creator 서비스에 대한 액세스

Creator 서비스는 생성 중에 선택한 위치에서만 액세스할 수 있습니다. 선택한 위치 외부에서 Creator 서비스를 호출하면 사용자 오류 메시지가 반환됩니다. 선택한 위치 외부에서 호출하려면 선택한 지리적 위치를 나타내는 접두사가 서비스 URL에 포함되어 있어야 합니다. 예를 들어, 미국에서 Creator를 만든 경우 변환 서비스에 대한 모든 호출을 `us.atlas.microsoft.com/conversion/convert`로 전송해야 합니다.

또한 Creator로 가져온 모든 데이터는 Creator 리소스와 동일한 지리적 위치에 업로드해야 합니다. 예를 들어 Creator가 미국에서 프로비저닝되는 경우 모든 원시 데이터를 `us.atlas.microsoft.com/mapData/upload`를 통해 업로드해야 합니다.

## <a name="next-steps"></a>다음 단계

실내 매핑 Creator 소개:

> [!div class="nextstepaction"]
> [데이터 업로드](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 변환](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 세트](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [타일 세트](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [기능 상태 집합](creator-indoor-maps.md#feature-statesets)

Creator를 사용하여 애플리케이션에서 실내 지도를 렌더링하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps Creator 리소스](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [실내 맵 동적 스타일 지정](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Indoor Maps 모듈 사용](how-to-use-indoor-module.md)