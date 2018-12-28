---
title: IoT Edge 모듈 제안 만들기 | Microsoft Docs
description: Marketplace의 새 IoT Edge 모듈을 게시하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: bf7d639c682e443f29b31b3c6d7438e89c406fde
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838636"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Cloud 파트너 포털에서 새 IoT Edge 모듈을 게시하는 방법

이 문서에서는 새 IoT Edge 모듈 제안을 게시하는 단계를 설명합니다.

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소는 Azure Marketplace용 IoT Edge 모듈 게시에 적용됩니다.

-   [CPP(Cloud 파트너 포털)](https://cloudpartner.azure.com/#alloffers)에 액세스합니다. 자세한 내용은 [게시 지침](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)을 참조하세요.

-   Azure Container Registry에 IoT Edge 모듈을 호스트합니다.

-   다음과 같은 IoT Edge 모듈 메타데이터를 준비합니다(아래 목록에는 일부 메타데이터만 나와 있음).

    -   제목

    -   설명(기본 HTML 형식)

    -   png 이미지 형식 및 다음 크기의 로고: 40픽셀 x 40픽셀, 90픽셀 x 90픽셀, 115픽셀 x 115픽셀, 255 픽셀x 115픽셀입니다.

    -   사용 약관 및 개인정보처리방침

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>CPP에서 IoT Edge 모듈 목록 준비
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>IoT Edge 모듈 형식의 새 제안 만들기 

다음 단계에 따라 IoT Edge 모듈 목록을 준비합니다.

-   [CPP 계정](https://cloudpartner.azure.com/)에 로그인합니다.

>[!Note]
>Cloud 파트너 포털에 대한 일반적인 정보는 [학습 설명서](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-getting-started-with-the-cloud-partner-portal)를 참조하세요.

-   **새 제안**을 선택한 후 **IoT Edge 모듈**을 선택합니다.

>[!NOTE]
>IoT Edge 모듈은 IoT Edge에서 실행되도록 특수하게 고안된 컨테이너입니다. IoT Edge 모듈로 처리되는 시나리오는 IoT Edge 컨텍스트에서 타당해야 합니다. 또한 IoT Edge 디바이스에 간편하게 배포할 수 있도록 하는 기본 구성 설정을 포함합니다. 이 컨테이너는 Edge 허브 및 IoT Hub와 통신할 수 있도록 하는 IoT Edge 모듈 SDK를 포함할 수도 있습니다.

### <a name="define-your-offer-settings"></a>제안 설정 정의

제안 설정 탭에서 제안 정보를 입력합니다.

![제안 ID](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   **제안 ID**는 CPP에서 제안을 고유하게 식별하고 고객 관련 URL에서 사용할 수 있습니다.

-   **이름**은 CPP에서 이 제안을 참조하는 데 사용되며 사용자에게만 표시됩니다.

### <a name="create-one-or-more-skus"></a>하나 이상의 SKU 만들기

각 SKU는 컨테이너 이미지에 해당합니다. 적어도 하나의 SKU가 있어야 하며 2개 이상을 추가할 수 있습니다. SKU에는 다음 두 부분이 있습니다.

-   SKU 메타데이터

-   컨테이너 메타데이터

**SKU를 만들려면**

**SKU** 탭을 선택한 후 **새 SKU**를 선택합니다.

![새 SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

SKU 메타데이터는 다음 필수 필드를 포함합니다.
- SKU ID - 고유 식별자입니다.
- 제목 - SKU 제목으로 50자 이내입니다.
- 요약 - 짧은 설명으로 100자 이내입니다.
- 설명 - 긴 설명입니다.
- 이 SKU 숨기기 - 기본값은 **아니요**입니다.
   
![SKU 세부 정보](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge 모듈 메타데이터 및 컨테이너 레지스트리

IoT Edge 모듈의 메타데이터에는 ACR(Azure Container Registry)에 저장된 이미지 참조 정보가 있습니다. Azure Marketplace는 공용 마켓플레이스 레지스트리에 이미지를 복사하며, 이 이미지는 인증 후에 고객이 사용할 수 있습니다. IoT Edge 모듈 이미지를 사용하기 위한 모든 사용자 요청은 Marketplace Container Registry에서 제공됩니다.

![컨테이너 이미지](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**컨테이너 이미지**

이미지 리포지토리 세부 정보는 다음 필수 필드를 포함합니다.

-   **구독 ID** ACR 레지스트리가 있는 Azure 구독 ID입니다.

-   **리소스 그룹 이름** - ACR 레지스트리의 리소스 그룹 이름입니다.

-   **레지스트리 이름** – ACR 레지스트리 이름입니다.

-   **리포지토리 이름** - 리포지토리 이름입니다. 일단 설정되면 나중에 이 값을 변경할 수 없습니다. 계정에 같은 이름의 다른 제안이 없도록 이름을 고유하게 유지해야 합니다.

-   **사용자 이름** – ACR(관리자 사용자 이름)과 연결된 사용자 이름입니다.

-   **암호** – ACR과 연결된 암호입니다.

    >[!Note]
    >파트너가 게시 프로세스에 설명된 ACR에 대해 액세스 권한을 가지려면 사용자 이름 및 암호가 필요합니다.

IoT Edge 모듈 이미지에 게시하는 경우 하나 이상의 이미지 태그를 제공할 수 있습니다. 테스트 중에 모듈을 쉽게 식별할 수 있도록 모듈에 ‘latest’ 태그(기본값)를 추가해야 합니다.

또한 다음 IoT Edge 모듈 사양을 지정할 수 있습니다.

-   **createOptions** - 이 IoT Edge 모듈을 즉시 시작할 수 있도록 전달할 기본 createOptions입니다.

-   **twin:** - IoT Module SDK를 사용하는 경우 이 IoT Edge 모듈을 즉시 시작할 수 있도록 전달할 기본 twin입니다.

-   **twin:** - IoT Module SDK를 사용하는 경우 이 IoT Edge 모듈을 즉시 시작할 수 있도록 전달할 기본 routes입니다.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>고객을 위해 IoT Edge 모듈 설명

Marketplace 탭에서 마케팅 관련 콘텐츠를 추가합니다. 이 정보는 Azure Marketplace에서 공개되고 나열될 항목입니다.

![IoT Edge 모듈 개요](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **제목** - 공개될 IoT Edge 모듈의 제목입니다.

-   **요약** - 탐색 페이지와 같은 대부분의 페이지에서 공개되는 IoT Edge 모듈 요약입니다.

-   **긴 요약** - 모듈 추천 시 공개되는 IoT Edge 모듈 요약입니다. 

-   **설명** - 제품 정보 페이지에서 공개될 IoT Edge 모듈에 대한 설명입니다. (기본 HTML 태그를 사용하여 설명에 서식을 지정할 수 있습니다.)

-   **마케팅 식별자** - 제품 URL을 만드는 데 사용되는 고유 식별자입니다. 이 URL은 *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier* 형식을 갖습니다.

-   **미리 보기 구독 ID** - 이러한 구독의 액세스 권한이 있는 사용자는 인증 단계 후, Live 계정을 만들기 전에 IoT Edge 모듈을 볼 수 있습니다.

-   **유용한 링크** - 제품 세부 정보 페이지에 표시되는 최대 10개의 링크를 추가할 수 있습니다.

-   **제안된 범주** - 최대 5개의 범주를 선택합니다. 이러한 범주는 제품 세부 정보 페이지에 표시됩니다. 현재, 모든 IoT Edge 모듈이 찾아보기 페이지의 *사물 인터넷 \> IoT Edge 모듈* 범주에 표시됩니다.

-   **로고** - IoT Edge 모듈 로고 이미지를 PNG 형식으로 업로드합니다. 정확하게 다음 크기로 사용: 40픽셀 x 40픽셀, 90픽셀 x 90픽셀, 115픽셀 x 115픽셀, 255 픽셀x 115픽셀입니다.

-   **스크린샷** - 스크린샷은 제품 세부 정보 페이지에 표시됩니다. IoT Edge 모듈의 의미 및 작동 방식을 시각적으로 알려주는 좋은 방법입니다. 아키텍처 다이어그램이나 사용 사례 그림을 예제로 표시할 수 있습니다.

-   **비디오** - 비디오는 제품 세부 정보 페이지에 표시됩니다. IoT Edge 모듈의 의미 및 작동 방식을 시각적으로 알려주는 좋은 방법입니다.

-   **잠재 고객 관리** - 제품에 대해 관심을 나타내는 모든 잠재 고객을 수집하는 시스템을 선택할 수 있습니다.

-   **개인 정보 보호** - 개인 정보 취급 방침을 가리키는 URL이 있어야 합니다.

-   **사용 약관** - 사용 약관이 있어야 합니다. HTML 태그를 사용하여 이 페이지의 서식을 지정하거나 다른 페이지 중 하나를 가리킬 수 있습니다.

### <a name="enter-your-support-contact-information"></a>지원 연락처 정보 입력

지원 탭에서 **엔지니어링 연락처** 및 **고객 지원** 정보를 제공합니다.

![지원 연락처](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>IoT Edge 모듈 인증

필요한 모든 정보를 제공한 후 **게시**를 선택하여 인증을 위해 IoT Edge 모듈을 보냅니다. 인증 프로세스에서 단계를 표시하는 타임라인이 표시됩니다.

**모듈 확인**

모듈은 인증 팀에서 확인합니다. 모듈이 인증되면 모듈을 테스트하기 위한 개인 링크가 제공됩니다. 테스트 후 변경해야 할 경우 제안의 메타데이터를 편집하고 인증 팀에 모듈을 다시 제출합니다. 

## <a name="publish-your-iot-edge-module"></a>IoT Edge 모듈 게시

테스트를 마치고 게시 준비가 완료되면 **Live 계정 만들기**를 선택하여 IoT Edge 모듈을 게시합니다.

>[!Important]
>Ignite 이벤트에서 IoT Edge 모듈을 발표하고 싶은 경우 2018년 9월 23일까지 모듈을 공개해야 합니다.
