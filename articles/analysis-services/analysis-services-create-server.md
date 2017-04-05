---
title: "Azure에서 Analysis Services 서버 만들기 | Microsoft Docs"
description: "Azure에서 Analysis Services 서버 인스턴스를 만드는 방법을 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/16/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 466595773663d43ad8e25fa9ec0ec0163a3f5962
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-analysis-services-server"></a>Analysis Services 서버 만들기
이 문서에서는 Azure 구독에서 Analysis Services 서버 리소스를 만드는 과정을 안내합니다.

## <a name="before-you-begin"></a>시작하기 전에
시작하려면 다음이 필요합니다.

* **Azure 구독**: [Azure 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/)으로 이동하여 계정을 만들 수 있습니다.
* **Azure Active Directory**: 구독이 Azure Active Directory와 연결되어야 합니다. 또한 해당 Azure Active Directory의 계정으로 Azure에 로그인해야 합니다. Microsoft 계정은 지원되지 않습니다. 자세한 내용은 [사용자 인증](analysis-services-overview.md#secure)을 참조하세요.
* **리소스 그룹**: 기존 리소스 그룹을 사용하거나 [새 리소스 그룹을 만듭니다](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Analysis Services 서버를 만들면 새 청구 가능한 서비스가 발생할 수 있습니다. 자세한 내용은 [Analysis Services 가격 책정](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.
> 
> 

## <a name="create-an-analysis-services-server"></a>Analysis Services 서버 만들기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **+새로 만들기** > **인텔리전스 + 분석** > **Analysis Services**를 클릭합니다.
3. **Analysis Services** 블레이드에서 필수 필드를 입력한 다음 **만들기**를 누릅니다.
   
    ![서버 만들기](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **서버 이름**: 서버를 참조하는 데 사용되는 고유한 이름을 입력합니다.
   * **구독**: 이 서버를 청구할 구독을 선택합니다.
   * **리소스 그룹**: Azure 리소스 컬렉션을 관리할 수 있도록 디자인된 컨테이너입니다. 자세한 내용은 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 참조하세요.
   * **위치**: 이 Azure 데이터 센터 위치는 서버를 호스팅합니다. 가장 큰 사용자 기반에 가장 가까운 위치를 선택합니다.
   * **가격 책정 계층**: 가격 책정 계층을 선택합니다. 최대 100GB의 테이블 형식 모델이 지원됩니다. 자세한 내용은 [Azure Analysis Services 가격 책정](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.
4. **만들기**를 클릭합니다.

만드는 데 1분 이내, 대개 몇 초가 걸립니다. **포털에 추가**를 선택한 경우 새 서버를 보려면 포털로 이동합니다. 또는 **더 많은 서비스** > **Analysis Services**로 이동하여 서버가 준비되었는지 확인합니다.

 ![대시보드](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>서버 만들기 자동화
Azure Resource Manager 템플릿 파일을 사용하여 즉석에서 서버 프로비저닝을 자동화할 수 있습니다. 자세한 내용은 이 유용한 동영상을 시청하세요.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>다음 단계
일단 서버를 만들면 SSMS 또는 SSDT를 사용하여 [모델을 배포](analysis-services-deploy.md)할 수 있습니다.

서버에 배포하는 모델이 온-프레미스 데이터 원본에 연결된 경우 네트워크의 컴퓨터에서 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다.


