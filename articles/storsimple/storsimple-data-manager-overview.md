---
title: "Microsoft Azure StorSimple 데이터 관리자 개요 | Microsoft Docs"
description: "StorSimple 데이터 관리자 서비스 개요 제공(비공개 미리 보기)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73

---

# <a name="storsimple-data-manager-overview-private-preview"></a>StorSimple 데이터 관리자 개요(비공개 미리 보기)

## <a name="overview"></a>개요

Microsoft Azure StorSimple은 일반적으로 파일 공유와 연결된 구조화되지 않은 데이터의 복잡성을 해결하는 하이브리드 클라우드 저장소 솔루션입니다. StorSimple은 온-프레미스 솔루션의 확장으로 클라우드 저장소를 사용하여 자동으로 온-프레미스 솔루션 및 클라우드 저장소에서 데이터를 계층화합니다. 로컬 및 클라우드 스냅숏과 함께 통합 데이터 보호 기능은 제멋대로 늘어나는 저장소 인프라의 필요성을 없애줍니다. 보관 및 재해 복구는 오프 사이트 위치의 역할을 하는 클라우드와 원활하게 작동합니다.

이 문서에서 도입한 데이터 변환 서비스를 사용하면 클라우드에서 StorSimple 데이터에 원활하게 액세스할 수 있습니다. 이 서비스는 API를 제공하여 StorSimple에서 데이터를 추출하고 해당 데이터를 쉽게 사용할 수 없는 형식으로 다른 Azure 서비스에 제공합니다. 이 미리 보기에서 지원되는 형식은 Azure Blob 및 Azure Media Services 자산입니다. 이 변환을 사용하면 Azure Media Services, Azure HDInsight, Azure Machine Learning 및 Azure Search와 같은 서비스를 쉽게 연결하여 StorSimple 8000 시리즈 온-프레미스 장치에서 데이터를 운영할 수 있습니다.

이를 보여 주는 높은 수준의 블록 다이어그램은 아래와 같습니다.

![높은 수준의 다이어그램](./media//storsimple-data-manager-overview/high-level-diagram.png)

이 문서에서는 이 서비스의 비공개 미리 보기에 등록할 수 있는 방법을 설명합니다. 또한 이 서비스를 사용하여 클라우드에서 StorSimple 데이터 및 기타 Azure 서비스를 사용하는 응용 프로그램을 작성하는 방법을 설명합니다.

## <a name="sign-up-for-data-manager-preview"></a>데이터 관리자 미리 보기에 등록
데이터 관리자 서비스에 등록하기 전에 다음 필수 구성 요소를 검토합니다.

### <a name="prerequisites"></a>필수 조건

이 연습에서는 사용자에게 다음 항목이 있다고 가정합니다.
* 활성 Azure 구독
* 등록된 StorSimple 8000 시리즈 장치에 대한 액세스
* StorSimple 8000 시리즈 장치와 연결된 모든 키

### <a name="sign-up"></a>등록

StorSimple 데이터 관리자는 비공개 미리 보기 상태입니다. 비공개 미리 보기인 이 서비스에 등록하려면 다음 단계를 수행합니다.

1.  [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)에서 StorSimple 데이터 관리자 확장을 사용하여 Azure Portal에 로그인합니다. Azure 자격 증명을 사용하여 로그인합니다.

2.  **+** 아이콘을 클릭하여 서비스를 만듭니다. **저장소**를 클릭한 다음 열린 블레이드에서 **모두 보기**를 클릭합니다.

    ![StorSimple 데이터 관리자 아이콘 검색](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. StorSimple 데이터 관리자 아이콘이 표시됩니다.

    ![StorSimple 데이터 관리자 아이콘 선택](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. StorSimple 데이터 관리자 아이콘을 클릭한 다음 **만들기**를 클릭합니다. 비공개 미리 보기에 사용하려는 구독을 선택한 다음 **등록하기**를 클릭합니다.

    ![등록하기](./media/storsimple-data-manager-overview/sign-me-up.png)

5. 요청을 보내서 사용자를 등록합니다. 최대한 빨리 등록하겠습니다. 구독을 사용하도록 설정한 후에 StorSimple 데이터 관리자 서비스를 만들 수 있습니다.

6. StorSimple 데이터 관리자 서비스에 쉽게 액세스하려면 별표 아이콘을 클릭하여 즐겨찾기에 고정합니다.

    ![StorSimple 데이터 관리자에 액세스](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).


<!--HONumber=Nov16_HO4-->


