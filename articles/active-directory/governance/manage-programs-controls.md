---
title: Azure AD 액세스 검토에 대 한 프로그램 및 컨트롤 관리 | Microsoft Docs
description: 수집 하 고 Azure Active Directory 액세스 검토 컨트롤을 구성 하기 위한 조직의 각 거 버 넌 스, 위험 관리 및 규정 준수 이니셔티브에 대 한 추가 프로그램을 만드는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3056976ada6c0a0287eec608dbad230e8dbb2625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895181"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Azure AD 액세스 검토에 대 한 프로그램 및 컨트롤 관리

Azure AD(Azure Active Directory)에는 그룹 구성원 및 애플리케이션 액세스에 대한 액세스 검토가 포함됩니다. 이러한 컨트롤 예제를 통해 조직의 그룹 구성원 자격 및 애플리케이션에 대한 액세스 권한이 있는 사용자를 확인할 수 있습니다. 조직에서 이러한 컨트롤을 사용하여 거버넌스, 위험 관리 및 준수 요구 사항을 효율적으로 처리할 수 있습니다.

## <a name="create-and-manage-programs-and-their-controls"></a>프로그램 및 해당 컨트롤 만들기 및 관리
액세스 검토를 프로그램으로 구성하여 다양한 용도로 추적하고 수집하는 방법을 간소화할 수 있습니다. 각 액세스 검토를 프로그램에 연결할 수 있습니다. 그런 다음, 감사자에게 제공할 보고서를 준비하면 특정 이니셔티브 범위의 액세스 검토에 초점을 맞출 수 있습니다.  프로그램 및 액세스 검토 결과 전역 관리자, 사용자 관리자, 보안 관리자 또는 보안 읽기 권한자 역할의 사용자에 게 표시 됩니다.

프로그램 목록을 보려면 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하고 **프로그램**을 선택합니다.

**기본 프로그램**은 항상 존재합니다. 전역 관리자 또는 사용자 관리자 역할의 경우 추가 프로그램을 만들 수 있습니다. 예를 들어 준수 이니셔티브 또는 비즈니스 목표 각각에 대한 프로그램을 하나씩 선택할 수 있습니다.

프로그램이 더 이상 필요하지 않고 연결된 컨트롤이 없는 경우 해당 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램의 액세스 검토 만들기](create-access-review.md)
- [그룹 또는 응용 프로그램에 대 한 액세스 검토 결과 검색 합니다.](retrieve-access-review.md)
