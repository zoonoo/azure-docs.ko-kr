---
title: Azure의 엔터프라이즈 관리자 역할 이해 | Microsoft Docs
description: Azure의 엔터프라이즈 관리자 역할에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370716"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Azure의 Azure 기업계약 관리자 역할 이해

조직의 사용량 및 지출 관리를 지원하기 위해 EA(기업계약)를 체결한 Azure 고객은 다음 5개의 관리 역할을 할당할 수 있습니다.

- 엔터프라이즈 관리자
- 엔터프라이즈 관리자(읽기 전용)
- 부서 관리자
- 부서 관리자(읽기 전용)
- 계정 소유자
 
이러한 역할은 Azure 기업계약 관리에만 적용되며, 리소스에 대한 액세스를 제어하기 위한 Azure의 기본 제공 역할 외에 추가로 제공됩니다. 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

다음 섹션에서는 각 역할의 제한 사항 및 기능에 대해 설명합니다.

## <a name="user-limit-for-admin-roles"></a>관리자 역할에 대한 사용자 제한

|역할| 사용자 제한|
|---|---|
|엔터프라이즈 관리자|Unlimited|
|엔터프라이즈 관리자(읽기 전용)|Unlimited|
|부서 관리자|Unlimited|
|부서 관리자(읽기 전용)|Unlimited|
|계정 소유자|계정당 1명<sup>1</sup>|

<sup>1</sup> 각 계정마다 고유한 Microsoft 계정이나 회사 또는 학교 계정이 필요합니다.

## <a name="organization-structure-and-permissions-by-role"></a>역할별 조직 구조 및 사용 권한

|태스크| 엔터프라이즈 관리자|엔터프라이즈 관리자(읽기 전용)|부서 관리자|부서 관리자(읽기 전용)|계정 소유자|
|---|---|---|---|---|---|
|엔터프라이즈 관리자 보기|✔|✔|✘|✘|✘|
|엔터프라이즈 관리자 추가 또는 제거|✔|✘|✘|✘|✘|
|알림 연락처 보기<sup>2</sup> |✔|✔|✘|✘|✘|
|알림 연락처 추가 또는 제거<sup>2</sup> |✔|✘|✘|✘|✘|
|부서 만들기 및 관리 |✔|✘|✘|✘|✘|
|부서 관리자 보기|✔|✔|✔|✔|✘|
|부서 관리자 추가 또는 제거|✔|✔|✔|✘|✘|
|등록에 포함된 계정 보기 |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|등록에 계정 추가 및 계정 소유자 변경|✔|✘|✔<sup>3</sup>|✘|✘|
|구독 및 구독 사용 권한 만들기/관리|✘|✘|✘|✘|✔|

- <sup>2</sup> 알림 연락처는 Azure 기업계약에 대한 메일 통신을 받습니다.
- <sup>3</sup> 작업은 부서의 계정으로 제한됩니다.


## <a name="usage-and-costs-access-by-role"></a>역할별 사용량 및 비용 액세스

|태스크| 엔터프라이즈 관리자|엔터프라이즈 관리자(읽기 전용)|부서 관리자|부서 관리자(읽기 전용) |계정 소유자|
|---|---|---|---|---|---|
|현금 약정 금액을 포함한 크레딧 잔액 보기|✔|✔|✘|✘|✘|
|부서 지출 할당량 보기|✔|✔|✘|✘|✘|
|부서 지출 할당량 설정|✔|✘|✘|✘|✘|
|조직의 EA 가격표 보기|✔|✔|✘|✘|✘|
|사용량 및 비용 세부 정보 보기|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Azure Portal의 리소스 관리|✘|✘|✘|✘|✔|

- <sup>4</sup> 엔터프라이즈 관리자가 Enterprise Portal에서 **DA 요금 보기** 정책을 사용하도록 설정해야 합니다. 그러면 부서 관리자가 부서의 비용 세부 정보를 볼 수 있습니다.
- <sup>5</sup> 엔터프라이즈 관리자가 Enterprise Portal에서 **AO 요금 보기** 정책을 사용하도록 설정해야 합니다. 그러면 계정 소유자가 계정의 비용 세부 정보를 볼 수 있습니다.


## <a name="pricing-in-azure-portal"></a>Azure Portal의 가격

Azure Portal에서 표시되는 가격은 사용자의 관리 역할 및 엔터프라이즈 관리자가 요금 보기 정책을 설정한 방식에 따라 다를 수 있습니다. Azure Portal에 표시되는 가격에 영향을 주는 Enterprise Portal의 두 가지 정책은 다음과 같습니다.

- DA 요금 보기
- AO 요금 보기

두 정책을 설정하는 방법을 알아보려면 [Azure의 청구 정보에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

다음 표에서는 기업계약 관리자 역할, 요금 보기 정책, Azure Portal의 RBAC(역할 기반 액세스 제어) 역할, Azure Portal에 표시되는 가격 간의 관계를 보여 줍니다. 엔터프라이즈 관리자에게는 항상 조직의 EA 가격을 기준으로 사용량 세부 정보가 표시됩니다. 그러나 부서 관리자 및 계정 소유자에게는 요금 보기 정책 및 해당 RBAC 역할에 따라 다른 가격 보기가 표시됩니다. 다음 표에 나열된 부서 관리자 역할은 부서 관리자 및 부서 관리자(읽기 전용) 역할을 둘 다 가리킵니다.

|기업계약 관리자 역할|역할에 대한 요금 보기 정책|RBAC 역할|가격 보기|
|---|---|---|---|
|계정 소유자 또는 부서 관리자|✔ 사용|소유자|조직의 EA 가격|
|계정 소유자 또는 부서 관리자|✘ 사용 안 함|소유자|소매 가격|
|계정 소유자 또는 부서 관리자|✔ 사용 |없음|가격 없음|
|계정 소유자 또는 부서 관리자|✘ 사용 안 함 |없음|가격 없음|
|없음|해당 없음 |소유자|소매 가격|

엔터프라이즈 관리자 역할 및 요금 보기 정책은 Enterprise Portal에서 설정합니다. RBAC 역할은 Azure Portal에서 업데이트할 수 있습니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure의 청구 정보에 대한 액세스 관리](billing-manage-access.md)
- [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)
- [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)
