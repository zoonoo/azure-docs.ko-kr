---
title: Azure Portal에서 관리 되는 id의 서비스 사용자 보기-Azure AD
description: Azure Portal에서 관리 ID의 서비스 주체를 보기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611931"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Azure Portal에서 관리 ID의 서비스 주체 보기

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 관리 ID의 서비스 주체를 보는 방법을 알아봅니다.

 > [!NOTE] 
 > 서비스 주체는 엔터프라이즈 응용 프로그램입니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없는 경우 [체험 계정에 가입](https://azure.microsoft.com/free/)합니다.
- [가상 머신](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) 또는 [애플리케이션에서 시스템 할당 ID](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)를 사용하도록 설정합니다.

## <a name="view-the-service-principal"></a>서비스 주체 보기

이 프로시저는 시스템 할당 ID를 사용하도록 설정된 VM의 서비스 주체를 보는 방법을 보여줍니다(애플리케이션에도 동일하게 적용됨).

1. **Azure Active Directory**를 클릭한 다음 **Enterprise 애플리케이션**을 클릭합니다.
2. **응용 프로그램 종류**아래에서 **모든 응용 프로그램** 을 선택 하 고 **적용**을 클릭 합니다.
3. 검색 필터 상자에 관리 id가 활성화 된 Azure 리소스의 이름을 입력 하거나 표시 된 목록에서 선택 합니다.

   ![포털에서 관리 ID 서비스 주체 보기](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>다음 단계

[Azure 리소스에 대한 관리 ID](./overview.md)