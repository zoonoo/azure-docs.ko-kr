---
title: "고객의 요금 청구서 주소와 Azure 스택의 비용 정산 | Microsoft Docs"
description: "스택에서 Azure 리소스 사용 정보를 검색 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>사용 및 청구 Azure 스택

사용 현황 사용자가 사용 되는 리소스의 수량을 나타냅니다. 각 사용자에 대 한 사용 정보를 수집 하 고 대금을 청구를 사용 하는 azure 스택 합니다. 이 문서에서는 Azure 스택 사용자, 리소스 사용에 대 한 청구 됩니다. 어떻게 및 청구 정보 분석, 청구 등 액세스 하는 방법을 설명 합니다.

Azure 스택 모든 리소스에 대 한 사용 현황 데이터 수집 및 집계 하 고이 데이터를 Azure 상거래를 전달 하는 인프라를 포함 합니다. 이 데이터에 액세스 하 고 청구 어댑터를 사용 하 여 청구 시스템으로 내보낼 하거나 Microsoft Power BI와 같은 비즈니스 인텔리전스 도구를 내보낼 수 있습니다. 를 내보낸 후이 청구 정보 분석에 사용 되었거나 비용 청구 시스템에 전송 합니다.

![청구 어댑터는 청구에 연결 하는 Azure 스택의 개념적 모델 응용 프로그램](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>사용 현황 파이프라인

각 리소스 공급자 스택에서 Azure 리소스 사용량에 따른 사용 현황 데이터를 내보냅니다. 사용 서비스를 정기적으로 (매시간 또는 매일)이 사용 현황 데이터를 집계 하 고 사용 데이터베이스에 저장 합니다. 사용량 Api를 사용 하 여 로컬로 저장 된 사용 현황 데이터를 Azure 스택 연산자 및 사용자가 액세스할 수 있습니다. 

있는 경우 [Azure 스택 인스턴스를 Azure에 등록](azure-stack-register.md), 사용량 브리지가 구성 되는 Azure 상거래 사용 현황 데이터를 보내려고 합니다. 데이터를 Azure에서 제공 되 면 청구 포털을 통해 또는 Azure 사용량 API를 사용 하 여 액세스할 수 있습니다. 참조는 [사용 데이터 보고](azure-stack-usage-reporting.md) 더 어떤 사용에 대 한 데이터를 보고 Azure에 알아보려면 항목입니다. 

다음 이미지는 사용 현황 파이프라인의 주요 구성 요소를 보여 줍니다.

![사용 현황 파이프라인](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>사용 현황 정보를 찾을 수 있나요, 및 방법?

Azure 스택 리소스 공급자의 경우 계산, 저장소 및 네트워크와 같은 각 구독에 대 한 시간 간격 사용 현황 데이터를 생성합니다. 사용 현황 데이터 등 리소스 이름, 사용 되는 구독, 사용 되는 수량 등 사용 되는 리소스에 대 한 정보를 포함 합니다. 에 미터 ID 리소스에 대 한 자세한 참조는 [사용량 API FAQ](azure-stack-usage-related-faq.md) 문서. 

사용 현황 데이터를 수집한 후에 [Azure에 보고 된](azure-stack-usage-reporting.md) Azure 청구 포털을 통해 볼 수 있는 청구서를 생성 하 합니다. 

> [!NOTE]
> 사용 데이터 보고는 Azure 스택 개발 키트용 및 용량 모델에서 구입한 Azure 스택 통합 시스템 사용자에 대 한 필요 하지 않습니다. Azure 스택에서 라이선스에 대 한 자세한 내용은 참조는 [패키징 및 가격](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) 데이터 시트입니다.

Azure 청구 포털 유료 리소스에 대 한 사용 현황 데이터를 보여 줍니다. 유료 리소스 외에도 Azure 스택은 광범위 한 REST Api 또는 PowerShell을 통해 Azure 스택 환경에서 액세스할 수 있는 리소스에 대 한 사용 현황 데이터를 캡처합니다. Azure 스택 운영자 사용자는 해당 사용 정보를 얻을 수 있지만 사용자에 대 한 모든 구독에 대 한 사용 현황 데이터를 검색할 수 있습니다.

## <a name="retrieve-usage-information"></a>사용 정보를 검색

사용 현황 데이터를 생성 하려면 실행 되 고 예를 들어 시스템에서 현재 사용 중인 리소스를, 활성 가상 컴퓨터 또는 등 데이터를 포함 하는 저장소 계정이 있어야 합니다. 확실 하지 않은 Azure 스택 Marketplace에서 실행 되는 리소스가 있는지, 가상 컴퓨터 (VM)를 배포 및 VM 확인 여부 경우 모니터링 되도록 하려면 블레이드 실행 됩니다. 다음 PowerShell cmdlet을 사용 하 여 사용 데이터를 보려면:

1. [Azure 스택에 대 한 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)
2. [Azure 스택 사용자 구성](user/azure-stack-powershell-configure-user.md) 또는 [Azure 스택 연산자](azure-stack-powershell-configure-admin.md) PowerShell 환경 

3. 사용 현황 데이터를 검색 하려면 사용 된 [Get UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell cmdlet:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>다음 단계

[Azure에 Azure 스택 사용 현황 데이터를 보고 합니다.](azure-stack-usage-reporting.md)

[공급자 리소스 사용량 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)

