---
title: 고객 청구 및 차지 백 Azure Stack의 | Microsoft Docs
description: Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f055837711b52fc32cb387fb86c623d3502f47ab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090142"
---
# <a name="usage-and-billing-in-azure-stack"></a>사용량 및 Azure Stack에서 결제

이 문서에서는 Azure Stack 사용자는 리소스 사용량에 대해 어떻게 청구 되는지 설명 합니다. 분석 및 요금이 다시 청구 정보를 액세스 하는 방법을 배울 수 있습니다.

Azure Stack 수집 하 고 사용 되는 리소스에 대 한 사용 현황 데이터를 그룹화 합니다. 그런 다음 Azure Stack Azure 상거래에이 데이터를 전달 합니다. Azure 상거래는 요금을 청구 Azure Stack 사용에 대 한 동일한 방식으로 Azure 사용량에 대 한 청구는 해당 합니다.

사용 현황 데이터 및 내보내기 고유한 청구 또는 차지 청구 어댑터를 사용 하 여 시스템을 백업 또는 Microsoft Power BI와 같은 비즈니스 인텔리전스 도구를 내보내고 가져올 수 있습니다.


## <a name="usage-pipeline"></a>파이프라인 사용

Azure Stack에서 각 리소스 공급자 리소스 사용량에 따른 사용 현황 데이터를 게시합니다. 사용 서비스를 정기적으로 (매시간 및 매일) 사용 현황 데이터를 집계 하 고 사용 현황 데이터베이스에 저장 합니다. Azure Stack 운영자 및 사용자가 Azure Stack 리소스 사용량 Api를 통해 저장 된 사용 현황 데이터를 액세스할 수 있습니다. 

있다면 [Azure를 사용 하 여 Azure Stack 인스턴스 등록](azure-stack-register.md), Azure Stack Azure 상거래에 사용 현황 데이터를 보내도록 구성 됩니다. 데이터를 Azure에 업로드 한 후 청구 포털을 통해 또는 Azure 리소스 사용량 Api를 사용 하 여 액세스할 수 있습니다. 참조를 [사용 데이터 보고](azure-stack-usage-reporting.md) 문서를 더 어떻게 사용 하는 방법에 대 한 데이터를 보고 Azure에 알아봅니다.  

다음 이미지는 사용 현황 파이프라인의 주요 구성 요소를 보여줍니다. 

![파이프라인 사용](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>사용 현황 정보를 찾을 수 있나요, 방법과?

Azure Stack 리소스 공급자 (예: 계산, 저장소 및 네트워크) 각 구독에 대 한 시간별 사용 현황 데이터를 생성합니다. 사용 현황 데이터는 리소스 이름, 구독 사용 수량 등 사용 되는 리소스에 대 한 정보를 포함 합니다. 미터 ID 리소스에 대 한 자세한 참조를 [사용량 API FAQ](azure-stack-usage-related-faq.md) 문서.

사용 현황 데이터를 수집한 후 [Azure에 보고](azure-stack-usage-reporting.md) Azure 청구 포털을 통해 볼 수 있는 자재 명세서를 생성 합니다. 

> [!NOTE]  
> 사용 데이터 보고 용량 모델에서 사용 허가 받은 Azure Stack 통합 시스템 사용자 및 Azure Stack 개발 키트에 대 한 필요 하지 않습니다. Azure Stack에서 라이선스에 대 한 자세한 내용은 참조는 [패키징 및 가격 책정](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) 데이터 시트입니다.

Azure 청구 포털에서는 청구 가능한 리소스에 대 한 사용 현황 데이터를 보여 줍니다. Azure Stack 유료 리소스와 함께 REST Api 또는 PowerShell을 통해 Azure Stack 환경에서 액세스할 수 있는 리소스의 광범위 한 집합에 대 한 사용 현황 데이터를 캡처합니다. Azure Stack 운영자는 사용자에 대 한 모든 구독에 대 한 사용 현황 데이터를 가져올 수 있습니다. 개별 사용자 에서만 해당 사용 정보를 가져올 수 있습니다. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>다중 테 넌 트 클라우드 서비스 공급자에 대 한 보고 사용

다중 테 넌 트 클라우드 서비스 공급자 (CSP)에 많은 고객이 Azure Stack을 사용 하 여 사용자 할 개별적으로 각 고객 사용량을 보고 공급자 서로 다른 Azure 구독에 대 한 사용량 요금을 부과할 수 있도록 합니다. 

각 고객을 다른 Azure Active Directory (Azure AD) 테 넌 트를 나타내는 해당 id가 것입니다. Azure Stack 각 Azure AD 테 넌 트에 할당 한 CSP 구독을 지원합니다. 기본 Azure Stack 등록을 위해 테 넌 트 및 구독을 추가할 수 있습니다. 모든 Azure 스택에 대 한 기본 등록 됩니다. 테 넌 트에 대 한 구독 등록 되지 않은 경우 사용자는 Azure Stack을 계속 사용할 수 및 용도 기본 등록에 사용 되는 구독에 전송 됩니다. 


## <a name="next-steps"></a>다음 단계

[Azure Stack을 사용 하 여 등록](azure-stack-registration.md)

[Azure에 Azure Stack 사용 현황 데이터를 보고 합니다.](azure-stack-usage-reporting.md)

[공급자 리소스 사용 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)