---
title: 분석 데이터에 프로그래밍 방식으로 액세스 하기 위한 필수 구성 요소
description: 상업적 marketplace 분석 데이터에 프로그래밍 방식으로 액세스 하기 위해 충족 해야 하는 요구 사항을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 1cdd3dba8203ce9e8daeaa963f1722389d89d19d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563823"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>분석 데이터에 프로그래밍 방식으로 액세스 하기 위한 필수 구성 요소

상용 marketplace 분석 데이터에 프로그래밍 방식으로 액세스 하려면 먼저 다음 요구 사항을 충족 해야 합니다.

## <a name="commercial-marketplace-enrollment"></a>상업적 marketplace 등록

상용 marketplace 분석 데이터에 프로그래밍 방식으로 액세스 하려면 상업적 marketplace 프로그램에 등록 되어 있고 파트너 센터 계정이 있어야 합니다. 방법을 알아보려면 [파트너 센터에서 상용 마켓플레이스 계정 만들기](./partner-center-portal/create-account.md)를 참조 하세요.

## <a name="create-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

일반 사용자 자격 증명은 상용 marketplace 분석 데이터의 프로그래밍 방식 액세스에 사용할 수 없습니다. 분석 Api에 액세스 하려면 암호와 함께 Azure Active Directory (Azure AD) 응용 프로그램을 만들어야 합니다. Azure AD 응용 프로그램 및 비밀을 만드는 방법을 알아보려면 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](../active-directory/develop/quickstart-register-app.md)을 참조 하세요.

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>파트너 센터 테 넌 트에 Azure AD 응용 프로그램 연결

Azure Portal에서 만든 Azure AD 응용 프로그램을 파트너 센터 계정에 연결 해야 합니다. 단계는 다음과 같습니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard)에 로그인합니다.
1. 오른쪽 위에서 기어 아이콘을 선택한 다음 **계정 설정** 을 선택 합니다.
1. **계정 설정** 메뉴에서 **사용자 관리** 를 선택 합니다.
1. **AZURE ad 응용 프로그램** 을 선택 하 고 **+ azure Ad 응용 프로그램 만들기** 를 선택 합니다.
1. Azure Portal에서 만든 Azure AD 응용 프로그램을 선택 하 고 **다음** 을 선택 합니다.
1. **관리자 (Windows)** 확인란을 선택 하 고 **추가** 를 선택 합니다.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="역할 선택을 위한 확인란을 사용 하 여 Azure AD 응용 프로그램 만들기 페이지를 보여 줍니다.":::

## <a name="generate-an-azure-ad-token"></a>Azure AD 토큰 생성

응용 프로그램 (클라이언트) ID를 사용 하 여 Azure AD 토큰을 생성 해야 합니다. 이 ID는 Microsoft id 플랫폼에서 클라이언트 응용 프로그램을 고유 하 게 식별 하 고 이전 단계에서 클라이언트 암호를 식별 하는 데 도움이 됩니다. Azure AD 토큰을 생성 하는 단계는 [클라이언트 자격 증명을 사용한 서비스 간 호출 (공유 암호 또는 인증서)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 참조 하세요.

> [!NOTE]
> 토큰은 1 시간 동안 유효 합니다.

## <a name="next-steps"></a>다음 단계

- [프로그래밍 방식 액세스 페러다임](analytics-programmatic-access.md)