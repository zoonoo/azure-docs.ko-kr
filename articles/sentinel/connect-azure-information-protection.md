---
title: Azure Information Protection을 Azure Sentinel에 연결
description: Azure Information Protection 데이터 커넥터를 구성하여 Azure Information Protection 로깅 정보를 Azure Sentinel에 스트리밍합니다.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: f936208e936bfe65b28816cc373d9ba1190308e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535645"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure Information Protection에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel의 Azure Information Protection 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 서비스 수준 약정 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Information Protection 데이터 커넥터를 구성하여 [Azure Information Protection](https://azure.microsoft.com/services/information-protection/)에서 Azure Sentinel로 정보를 스트림 로깅할 수 있습니다. Azure Information Protection은 클라우드 또는 온-프레미스에 저장되어 있는지 여부에 관계 없이 중요한 데이터를 컨트롤하고 보호하는 데 유용합니다.

이 서비스의 로깅 정보가 현재 Azure Sentinel 대해 선택한 것과 동일한 Log Analytics 작업 영역에 저장되도록 [Azure Information Protection 대한 중앙 보고](/azure/information-protection/reports-aip)가 이미 구성된 경우 이 데이터 커넥터의 구성을 건너뛸 수 있습니다. Azure Information Protection 로깅 정보는 이미 Azure Sentinel에 사용할 수 있습니다.

그러나 Azure Information Protection 로깅 정보가 현재 Azure Sentinel 대해 선택한 작업 영역과 다른 Log Analytics 작업 영역으로 가는 경우 다음 중 하나를 수행합니다.

- Azure Sentinel에서 선택한 작업 영역을 변경합니다.

- 이 데이터 커넥터를 구성하여 수행할 수 있는 Azure Information Protection 작업 영역을 변경합니다.
    
    작업 영역을 변경하면 이제 Azure Information Protection에 대한 새 보고 데이터가 Azure Sentinel에 사용하는 작업 영역에 저장되고, 기록 데이터를 Azure Sentinel에서 사용할 수 없습니다. 또한 이전 작업 영역이 사용자 지정 쿼리, 경고 또는 REST API에 대해 구성된 경우 Azure Information Protection을 계속 사용하려면 Azure Sentinel 작업 영역에 대해 다시 구성해야 합니다. Azure Information Protection을 사용하는 클라이언트 및 서비스에는 재구성이 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 테넌트에 쓰이는 다음 Azure AD 관리자 역할 중 하나입니다. 
    - Azure Information Protection 관리자
    - 보안 관리자
    - 규정 준수 관리자
    - 규정 준수 데이터 관리자
    - 전역 관리자
    
    > [!NOTE]
    > 테넌트는 [통합 레이블지정 플랫폼](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)에 있는 경우 Azure Information Protection 관리자 역할을 사용할 수 없습니다.
    
    이러한 관리자 역할은 Azure Information Protection 커넥터를 구성하는 데만 필요하며 Azure Sentinel이 Azure Information Protection에 연결된 경우에는 필요하지 않습니다.

- Azure Sentinel 및 Azure Information Protection에 사용 중인 Log Analytics 작업 영역에 대한 읽기 및 쓰기 사용 권한입니다.

- Azure Information Protection이 Azure Portal에 추가되었습니다. 이러한 작업을 수행하는 데 도움이 필요한 경우 [Azure Portal에 Azure Information Protection 추가](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)를 참조하세요.

## <a name="connect-to-azure-information-protection"></a>Azure Information Protection에 연결

Azure Information Protection에 대한 Log Analytics 작업 영역을 구성하지 않았거나 Azure Information Protection 로깅 정보를 저장하는 작업 영역을 변경해야 하는 경우 다음 지침을 따르십시오.

1. Azure Sentinel에서 **데이터 커넥터** > **Azure Information Protection(미리 보기)** 를 선택합니다.

2. **커넥터 페이지 열기** 를 선택합니다.

3. **구성** 에서 **Azure Information Protection 로그 연결** 을 선택합니다.

4. **분석 구성(미리 보기)** 블레이드에서 현재 Azure Sentinel에 사용하는 작업 영역을 선택합니다. 다른 작업 영역을 선택하면 Azure Information Protection의 보고 데이터를 Azure Sentinel에서 사용할 수 없습니다.

5. 작업 영역을 선택한 후 **확인** 을 선택합니다. 커넥터 **상태** 가 **연결됨** 으로 변경됩니다.

6. Azure Information Protection의 보고 데이터는 선택한 작업 영역의 **InformationProtectionLogs_CL** 테이블에 저장됩니다. 
    
    이 보고 데이터에 Azure Monitor의 관련 스키마를 사용하려면 **InformationProtectionEvents** 를 검색합니다. 이러한 이벤트 함수에 대한 자세한 내용은 Azure Information Protection 설명서에서 [이벤트 함수에 대한 친숙한 스키마 참조](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Information Protection을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.