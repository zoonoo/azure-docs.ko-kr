---
title: 분석 데이터에 프로그래밍 방식으로 액세스하기 위한 사전 요구 사항
description: 상용 Marketplace 분석 데이터에 프로그래밍 방식으로 액세스하기 위해 충족해야 하는 요구 사항을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 1cdd3dba8203ce9e8daeaa963f1722389d89d19d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563823"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>분석 데이터에 프로그래밍 방식으로 액세스하기 위한 사전 요구 사항

상용 Marketplace 분석 데이터에 프로그래밍 방식으로 액세스하려면 먼저 다음 요구 사항을 충족해야 합니다.

## <a name="commercial-marketplace-enrollment"></a>상용 Marketplace 등록

프로그래밍 방식으로 상용 Marketplace 분석 데이터에 액세스하려면 상용 Marketplace 프로그램에 등록하고 파트너 센터 계정이 있어야 합니다. 방법을 알아보려면 [파트너 센터에서 상업용 마켓플레이스 계정을 만듭니다](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

일반 사용자 자격 증명은 상용 Marketplace 분석 데이터의 프로그래밍 방식 액세스에 사용할 수 없습니다. 분석 API에 액세스하려면 비밀과 함께 Azure AD(Azure Active Directory) 애플리케이션을 만들어야 합니다. Azure AD 애플리케이션 및 비밀을 만드는 방법을 알아보려면 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)을 참조하세요.

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>파트너 센터 테넌트에 Azure AD 애플리케이션 연결

Azure Portal에서 만든 Azure AD 애플리케이션을 파트너 센터 계정에 연결해야 합니다. 단계는 다음과 같습니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard)에 로그인합니다.
1. 페이지 오른쪽 위에서 기어 아이콘을 선택한 후 **계정 설정** 을 선택합니다.
1. **계정 설정** 메뉴에서 **사용자 관리** 를 선택합니다.
1. **Azure AD 애플리케이션** 을 선택하고 **+ Azure AD 애플리케이션 만들기** 를 선택합니다.
1. Azure Portal에서 만든 Azure AD 애플리케이션을 선택하고 **다음** 을 선택합니다.
1. **관리자(Windows)** 확인란을 선택하고 **추가** 를 선택합니다.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="역할 선택을 위한 확인란을 포함하는 Azure AD 애플리케이션 만들기 페이지를 보여 줍니다.":::

## <a name="generate-an-azure-ad-token"></a>Azure AD 토큰 생성

애플리케이션(클라이언트) ID를 사용하여 Azure AD 토큰을 생성해야 합니다. 이 ID는 Microsoft ID 플랫폼의 클라이언트 애플리케이션, 이전 단계의 클라이언트 암호를 고유하게 식별하는 데 유용합니다. Azure AD 토큰을 생성하는 단계는 [클라이언트 자격 증명을 사용한 서비스 간 호출(공유 암호 또는 인증서)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 참조하세요.

> [!NOTE]
> 이 토큰은 1시간 동안 유효합니다.

## <a name="next-steps"></a>다음 단계

- [프로그래밍 방식 액세스 페러다임](analytics-programmatic-access.md)