---
title: Azure API Management에서 Power Platform으로 API 내보내기 | Microsoft Docs
description: API Management에서 Power Platform으로 API를 내보내는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 5814ff3909aa36ccfd9e14033f200fb4d3b8d32d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252860"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Azure API Management에서 Power Platform으로 API 내보내기 

Microsoft [Power Platform](https://powerplatform.microsoft.com)을 사용하는 시민 개발자는 전문 개발자가 개발하고 Azure에 배포하는 비즈니스 기능에 도달해야 하는 경우가 많습니다. 전문 개발자는 [Azure API Management](https://aka.ms/apimrocks)를 통해 백 엔드 서비스를 API로 게시하고, 이러한 API를 시민 개발자가 사용할 수 있도록 사용자 지정 커넥터로 Power Platform(Power Apps 및 Power Automate)으로 쉽게 내보낼 수 있습니다. 

이 문서에서는 API Management에서 Power Platform으로 API를 내보내는 단계를 안내합니다. 

## <a name="prerequisites"></a>사전 요구 사항

+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ API Management 인스턴스에 Power Platform으로 내보내려고 하는 API가 있는지 확인합니다.
+ Power Apps 또는 Power Automate [환경](/powerapps/powerapps-overview#power-apps-for-admins)을 보유하고 있는지 확인합니다. 

## <a name="export-an-api"></a>API 내보내기

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API**를 선택합니다.
2. 내보내려는 API 옆에 있는 세 개의 점을 클릭합니다. 
3. **내보내기**를 선택합니다.
4. **Power Apps 및 Power Automate**를 선택합니다.
5. API를 내보낼 환경을 선택합니다. 
6. 사용자 지정 커넥터의 이름으로 사용될 표시 이름을 제공합니다.  
7. 선택 사항으로 API가 OAuth 2.0 서버에 의해 보호되는 경우 `Client ID`, `Client secret`, `Authorization URL`, `Token URL` 및 `Refresh URL` 등의 추가 정보도 제공해야 합니다.  
8. **내보내기**를 선택합니다. 

내보내기가 완료되면 Power App 또는 Power Automate 환경으로 이동합니다. API는 사용자 지정 커넥터로 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Power Platform에 대한 자세한 정보](https://powerplatform.microsoft.com/)
* [자습서에 따라 API Management에서 일반적인 작업 알아보기](./import-and-publish.md)
