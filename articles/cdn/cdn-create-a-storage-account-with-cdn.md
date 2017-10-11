---
title: "Azure CDN과 Azure Storage 계정 통합 | Microsoft Docs"
description: "Azure Storage에서 Blob을 캐시하여 고대역폭 콘텐츠를 배달하기 위해 Azure CDN(콘텐츠 배달 네트워크)을 사용하는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure CDN과 Azure Storage 계정 통합
CDN을 사용하도록 설정하여 Azure 저장소의 콘텐츠를 캐시할 수 있습니다. CDN은 미국, 유럽, 아시아, 오스트레일리아 및 남아메리카의 물리적 노드에서 계산 인스턴스의 Blob 및 정적 콘텐츠를 캐시하여 고대역폭 콘텐츠를 제공하기 위한 글로벌 솔루션을 개발자에게 제공합니다.

## <a name="step-1-create-a-storage-account"></a>1 단계: 저장소 계정 만들기
다음 절차에 따라 Azure 구독에 대한 새 저장소 계정을 만듭니다. 저장소 계정을 통해 Azure 저장소 서비스에 액세스할 수 있습니다. 저장소 계정은 Blob 서비스, 큐 서비스, 테이블 서비스 등 각 Azure 저장소 서비스 구성 요소에 액세스하는 데 필요한 가장 높은 수준의 네임스페이스를 나타냅니다. 자세한 내용은 [Microsoft Azure 저장소 소개](../storage/common/storage-introduction.md)를 참조하세요.

저장소 계정을 만들려면 관련 구독에 대한 서비스 관리자 또는 공동 관리자여야 합니다.

> [!NOTE]
> Azure 포털 및 Powershell을 포함하여 저장소 계정을 만드는 데 사용할 수 있는 여러 가지 방법이 있습니다.  이 자습서에서는 Azure 포털을 사용합니다.  
> 
> 

**Azure 구독에 대한 저장소 계정을 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위에서 **새로 만들기**를 선택합니다. **새로 만들기** 대화 상자에서 **데이터 + 저장소**를 선택한 다음 **저장소 계정**을 클릭합니다.
    
    **저장소 계정 만들기** 블레이드가 나타납니다.   

    ![저장소 계정 만들기][create-new-storage-account]  

3. **이름** 필드에 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다.
   
    이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다. Blob 서비스에 있는 컨테이너 리소스의 주소를 지정하려면 다음 형식의 URI를 사용하며, 여기서 *&lt;StorageAccountLabel&gt;*은 **URL 입력**에 입력한 값을 가리킵니다.
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **중요:** URL 레이블은 저장소 계정 URI의 하위 도메인을 구성하며, Azure에서 호스팅된 모든 서비스에서 고유해야 합니다.
   
    이 값은 프로그래밍 방식으로 이 계정에 액세스할 때 또는 포털에서 이 저장소 계정의 이름으로도 사용됩니다.
4. **배포 모델**, **계정 종류**, **성능** 및 **복제**에 대한 기본값을 그대로 둡니다. 
5. 저장소 계정을 사용할 **구독** 을 선택합니다.
6. **리소스 그룹**을 선택하거나 만듭니다.  리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
7. 저장소 계정의 위치를 선택합니다.
8. **만들기**를 클릭합니다. 저장소 계정을 만드는 프로세스를 완료하려면 몇 분 정도 걸릴 수 있습니다.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>2단계: 저장소 계정에 대해 CDN 사용

이제 최신 통합을 사용하여 저장소 포털 확장을 종료하지 않고도 저장소 계정에 대한 CDN을 사용하도록 설정할 수 있습니다. 

1. 저장소 계정을 선택하고, "CDN"을 검색하거나 왼쪽 탐색 메뉴에서 아래로 스크롤한 다음 "Azure CDN"을 클릭합니다.
    
    **Azure CDN** 블레이드가 나타납니다.

    ![cdn 사용 탐색][cdn-enable-navigation]
    
2. 필요한 정보를 입력하여 새 끝점을 만듭니다.
    - **CDN 프로필**: 새 프로필을 만들거나 기존 프로필을 사용할 수 있습니다.
    - **가격 책정 계층**: 새 CDN 프로필을 만드는 경우 가격 책정 계층만 선택하면 됩니다.
    - **CDN 끝점 이름**: 원하는 끝점 이름을 입력합니다.

    > [!TIP]
    > 만든 CDN 끝점은 기본적으로 저장소 계정의 호스트 이름을 토대로 이름이 지정됩니다.

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. 만들어진 새 끝점은 위의 끝점 목록에 표시됩니다.

    ![CDN 저장소 새 끝점][cdn-storage-new-endpoint]

> [!NOTE]
> Azure CDN 확장으로 이동하여 CDN을 사용하도록 설정할 수도 있습니다. [자습서](#Tutorial-cdn-create-profile)
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>3단계: 추가 CDN 기능 사용

저장소 계정의 "Azure CDN" 블레이드에서 목록에 있는 CDN 끝점을 클릭하여 CDN 구성 블레이드를 엽니다. 전송에 대해 압축, 쿼리 문자열, 지역 필터링 등과 같은 추가 CDN 기능을 사용하도록 설정할 수 있습니다. CDN 끝점에 사용자 지정 도메인 매핑을 추가하고 사용자 지정 도메인 HTTPS를 사용하도록 설정할 수도 있습니다.
    
![CDN 저장소 CDN 구성][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>4단계: CDN 콘텐츠 액세스
CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 캐시된 Blob의 주소는 다음과 유사합니다.

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 저장소 계정에 대한 CDN 액세스를 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에 적합합니다. 현재 CDN에 캐시된 개체를 수정하는 경우 캐시된 콘텐츠 TTL(Time-to-Live) 기간이 만료될 때 CDN에서 해당 콘텐츠를 새로 고쳐야 CDN을 통해 새 콘텐츠를 사용할 수 있습니다.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>5단계: CDN에서 콘텐츠 제거
더 이상 Azure CDN(콘텐츠 배달 네트워크)에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

* 컨테이너를 공용 대신 전용으로 설정할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명읽기 권한 관리](../storage/blobs/storage-manage-access-to-resources.md) 를 참조하세요.
* 관리 포털을 사용하여 CDN 끝점을 사용하지 않도록 설정하거나 삭제할 수 있습니다.
* 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정할 수 있습니다.

CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 끝점이 삭제될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 CDN에서 CDN 끝점이 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

## <a name="additional-resources"></a>추가 리소스
* [CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](cdn-map-content-to-custom-domain.md)
* [사용자 지정 도메인에 대해 HTTPS 사용](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
