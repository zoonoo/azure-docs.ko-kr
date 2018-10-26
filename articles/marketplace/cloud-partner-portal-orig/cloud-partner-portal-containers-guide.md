---
title: 컨테이너 | Microsoft Docs
description: 컨테이너 이미지를 게시하는 단계를 설명합니다.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807985"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>클라우드 파트너 포털에서 컨테이너 이미지 게시
========================================================

이 문서에서는 클라우드 파트너 포털에서 새 컨테이너 이미지를 게시하는 방법을 설명합니다.

새 컨테이너 이미지를 게시하려면 다음 단계를 수행합니다.

1. **새 제품**을 선택한 후 **컨테이너**를 선택합니다.

    ![새 제품에 대해 컨테이너 옵션 선택](media/cpp-containers-guide/azure-container-offer.png)

2. 제품 설정 탭의 제품 ID 아래에 **제품 ID**, **게시자 ID** 및 **이름**을 입력합니다.

    ![제품 ID](media/cpp-containers-guide/containers-offer-settings.png)

3. SKU 탭에서 **새 SKU**를 선택합니다.
    >[!NOTE]
    >SKU는 여러 개 추가할 수 있습니다.

    ![새 SKU 프롬프트](media/cpp-containers-guide/containers-sku-settings.png)

4. SKU 및 컨테이너 정보를 입력합니다. 각 SKU는 컨테이너 이미지에 해당합니다. SKU에는 다음의 두 부분이 있습니다.

    -   SKU 메타데이터
    -   컨테이너 메타데이터

    SKU 메타데이터는 컨테이너 이미지의 표시 정보를 포함합니다.

    ![SKU 메타데이터](media/cpp-containers-guide/containers-sku-details.png)

    컨테이너 메타데이터에는 ACR(Azure Container Registry) 내의 이미지 리포지토리 세부 정보에 대한 참조 정보가 포함되어 있습니다. Azure Marketplace는 공용 마켓플레이스 레지스트리에 이미지를 복사하며, 이 이미지는 인증 후에 고객이 사용할 수 있습니다. 컨테이너 이미지를 사용하기 위한 Azure 사용자의 모든 요청은 Marketplace Container Registry에서 처리됩니다.

   ![컨테이너 메타데이터](media/cpp-containers-guide/containers-image-repository.png)

    위 화면 캡처에 나와 있는 이미지 리포지토리 세부 정보에는 다음 필드가 포함되어 있습니다.

    -   **구독 ID** - ACR 레지스트리가 있는 Azure 구독 ID입니다.
    -   **리소스 그룹 이름** - ACR 레지스트리의 리소스 그룹 이름입니다.
    -   **레지스트리 이름** – ACR 레지스트리 이름입니다.
    -   **리포지토리 이름** - 리포지토리 이름입니다. 이 값은 설정하고 나면 나중에 변경할 수 없습니다. 계정에 같은 이름의 다른 제품이 없도록 고유한 이름을 지정해야 합니다.
    -   **사용자 이름** – ACR(관리자 사용자 이름)과 연결된 사용자 이름입니다.
    -   **암호** – ACR과 연결된 암호입니다.

    >[!NOTE]
    >사용자 이름과 암호가 있어야 파트너가 게시 프로세스에 설명된 ACR에 액세스할 수 있습니다.

    컨테이너 이미지를 게시할 때는 하나 이상의 이미지 태그를 입력할 수도 있습니다. 파트너는 이미지 태그 외에 SHA 다이제스트를 제공할 수도 있습니다. 테스트 중에 이미지를 식별할 수 있도록 이미지에 **테스트 태그**를 추가하세요.

5. Marketplace 탭에서 마케팅 관련 콘텐츠를 추가합니다.

    ![Marketplace 정보](media/cpp-containers-guide/containers-marketplace-tab.png)

6. 지원 탭에서 엔지니어링 연락처 및 고객 지원 정보를 입력합니다.

   ![지원 정보](media/cpp-containers-guide/containers-support-tab.png)

7. **게시**를 선택하여 제품을 게시합니다. 게시를 선택하면 컨테이너 이미지 게시를 위해 진행되는 단계를 보여 주는 타임라인이 표시됩니다.
