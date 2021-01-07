---
title: Azure Digital Twins의 고객 데이터 요청 기능
titleSuffix: Azure Digital Twins
description: 이 문서에서는 Azure Digital Twins에서 개인 데이터를 내보내고 삭제 하는 프로세스를 보여 줍니다.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461762"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure Digital Twins 고객 데이터 요청 기능

Azure Digital Twins는 비즈니스 환경의 보안 디지털 표현을 만들기 위한 개발자 플랫폼입니다. 표현은 사용자가 선택한 데이터 원본의 라이브 상태 데이터에 따라 결정 됩니다.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure digital 쌍에서 *디지털* 쌍 이라는 디지털 표현은 실제 환경에서 엔터티를 나타내며 식별자와 연결 됩니다. Microsoft는 정보를 유지 하지 않으며 식별자를 사용자에 게 상관 관계를 지정 하는 데이터에 액세스할 수 없습니다. 

Azure digital 쌍의 많은 디지털 쌍은 개인 엔터티를 직접 나타내지 않습니다. 일반적으로 표시 되는 개체는 사무실 회의실 이거나 공장 바닥 일 수 있습니다. 그러나 사용자는 일부 엔터티를 개인적으로 식별할 수 있는 것으로 간주할 수 있으며, 그에 따라 개인에 게 디지털 쌍을 연결 하는 자체 자산이 나 인벤토리 추적 방법이 유지 될 수 있습니다. Azure digital 쌍는 디지털 쌍과 연결 된 모든 데이터를 관리 하 고 개인 데이터 처럼 저장 합니다.

데이터 주체 요청에서 참조 될 수 있는 개인 데이터를 확인, 내보내기 및 삭제 하기 위해 Azure Digital Twins 관리자는 사용자 및 역할에 대 한 [**Azure Portal**](https://portal.azure.com/) 또는 디지털 쌍에 대 한 [**Azure DIGITAL Twins REST api**](/rest/api/azure-digitaltwins/) 를 사용할 수 있습니다. Azure Portal 및 REST Api는 사용자가 이러한 데이터 주체 요청을 처리 하는 다양 한 방법을 제공 합니다.

## <a name="identifying-customer-data"></a>고객 데이터 식별

Azure Digital Twins는 *개인 데이터* 를 관리자 및 사용자와 연결 된 데이터로 간주 합니다. 

Azure Digital Twins는 환경에 액세스할 수 있는 사용자의 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *개체 ID* 를 저장 합니다. Azure Portal의 azure Digital Twins는 사용자 전자 메일 주소를 표시 하지만 이러한 전자 메일 주소는 Azure Digital Twins 내에 저장 되지 않습니다. Azure Active Directory 개체 ID를 사용 하 여 Azure Active Directory에서 동적으로 조회 됩니다.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

Azure Digital Twins 관리자는 Azure Portal를 사용 하 여 사용자와 관련 된 데이터를 삭제할 수 있습니다. 또한 Azure digital 쌍 REST api를 사용 하 여 개별 디지털 쌍에서 삭제 작업을 수행할 수 있습니다. 사용 가능한 Api에 대 한 자세한 내용은 [Azure Digital Twins REST api 설명서](/rest/api/azure-digitaltwins/)를 참조 하세요.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

Azure Digital Twins는 디지털 쌍과 관련 된 데이터를 저장 합니다. 사용자는 REST Api를 통해이 데이터를 검색 하 고 볼 수 있으며 복사 및 붙여넣기를 사용 하 여이 데이터를 내보낼 수 있습니다. 

사용자 역할 및 역할 할당을 포함 한 고객 데이터는 Azure Portal에서 선택, 복사 및 붙여넣을 수 있습니다. 

## <a name="links-to-additional-documentation"></a>추가 설명서에 대한 링크

Azure Digital Twins 서비스 Api의 전체 목록은 [Azure 디지털 쌍 REST api 설명서](/rest/api/azure-digitaltwins/)를 참조 하세요.