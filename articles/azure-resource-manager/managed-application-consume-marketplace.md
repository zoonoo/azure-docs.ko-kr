---
title: "Marketplace에서 Azure Managed Application 사용 | Microsoft Docs"
description: "Marketplace를 통해 사용할 수 있는 Azure Managed Application을 만드는 방법을 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Marketplace에서 Azure Managed Application 사용

[Managed Application 개요](managed-application-overview.md) 문서에서 설명한 대로 종단 간 환경에는 두 가지 시나리오가 있습니다. 하나는 관리되는 응용 프로그램을 만들어 고객이 사용할 수 있게 하려는 게시자 또는 공급업체입니다. 다른 하나는 관리되는 응용 프로그램의 최종 고객 또는 소비자입니다. 이 문서에서는 두 번째 시나리오에 대해 설명합니다. Microsoft Azure Marketplace의 관리되는 응용 프로그램을 배포하는 방법을 설명합니다.

## <a name="create-from-the-marketplace"></a>Marketplace에서 만들기

Marketplace에서 관리되는 응용 프로그램을 배포하는 것은 Marketplace에서 유형에 관계없이 리소스를 배포하는 것과 비슷합니다. 

포털에서 **+새로 만들기**를 선택하고 배포할 솔루션 유형을 검색합니다. 사용 가능한 옵션 중에서 필요한 옵션을 선택합니다.

![검색 솔루션](./media/managed-application-consume-marketplace/search-apps.png)

응용 프로그램의 요약을 검토하고 **만들기**를 선택합니다.

![관리되는 응용 프로그램 만들기](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

다른 솔루션과 마찬가지로 값을 제공할 필드가 표시됩니다. 이러한 필드는 생성하는 관리되는 응용 프로그램의 유형에 따라 달라집니다. 

## <a name="view-support-information"></a>지원 정보 보기

관리되는 응용 프로그램이 배포된 후에 응용 프로그램에 대한 지원 정보를 봅니다. 관리되는 응용 프로그램 블레이드에서 지원 정보가 나열됩니다.

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>게시자 사용 권한 보기

응용 프로그램을 관리하는 공급업체에는 리소스에 대한 액세스 권한이 부여됩니다. 이러한 사용 권한을 보려면 **권한 부여**를 선택합니다.

![권한 부여](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>다음 단계

* Marketplace에서 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure Managed Application](managed-application-author-marketplace.md)을 참조하세요.
* 조직의 사용자만 사용할 수 있는 관리되는 응용 프로그램을 게시하려면 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* 조직의 사용자만 사용할 수 있는 관리되는 응용 프로그램을 사용하려면 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.