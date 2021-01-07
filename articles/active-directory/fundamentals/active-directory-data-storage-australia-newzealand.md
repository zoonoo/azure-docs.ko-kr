---
title: 오스트레일리아 및 뉴질랜드 고객을 위한 고객 데이터 저장소-Azure AD
description: 오스트레일리아 및 뉴질랜드 고객을 위해 고객 관련 데이터를 저장 하는 Azure Active Directory에 대해 알아보세요.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836974"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory에서 오스트레일리아 및 뉴질랜드 고객을 위한 고객 데이터 저장소 

Azure Active Directory (Azure AD)는 Microsoft 온라인 서비스에 등록할 때 제공한 국가에 따라 지리적 위치에 고객 데이터를 저장 합니다. Microsoft Online services에는 Microsoft 365 및 Azure가 포함 됩니다. 

Azure AD 및 기타 Microsoft 서비스의 데이터가 있는 위치에 대 한 자세한 내용은 Microsoft 보안 센터의 [어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조 하세요.

2020 년 2 월 26 일부 터 Microsoft는 오스트레일리아 데이터 센터 내에서 오스트레일리아 또는 뉴질랜드 청구 주소를 사용 하 여 새 테 넌 트에 대해 Azure AD의 고객 데이터를 저장 하기 시작 했습니다. 2020 년 5 월 1 일부 터 2021 년 3 월 31 일 사이에 Microsoft에서 고객의 조치를 요구 하지 않고 오스트레일리아 또는 뉴질랜드 청구 주소가 있는 기존 테 넌 트를 오스트레일리아 데이터 센터로 마이그레이션합니다. 마이그레이션 프로세스에는 고객에 대 한 가동 중지 시간이 포함 되지 않으며 마이그레이션 중에 테 넌 트의 기능에 영향을 주지 않습니다.

또한 일부 Azure AD 기능은 오스트레일리아에서 고객 데이터의 저장소를 아직 지원 하지 않습니다. 특정 기능 정보는 [AZURE AD 데이터 맵으로](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)이동 하세요. 예를 들어 Microsoft Azure AD Multi-Factor Authentication는 미국에 고객 데이터를 저장 하 고 전역적으로 처리 합니다. [AZURE AD Multi-Factor Authentication 데이터 상주 및 고객 데이터를](../authentication/concept-mfa-data-residency.md)참조 하세요.

> [!NOTE]
> Microsoft 제품, 서비스 및 Azure AD와 통합 된 타사 응용 프로그램은 고객 데이터에 액세스할 수 있습니다. 특정 제품, 서비스 및 응용 프로그램에서 고객 데이터를 처리 하는 방법 및 회사의 데이터 저장소 요구 사항을 충족 하는지 여부를 확인 하는 데 사용 하는 각 제품, 서비스 및 응용 프로그램을 평가 합니다. Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.