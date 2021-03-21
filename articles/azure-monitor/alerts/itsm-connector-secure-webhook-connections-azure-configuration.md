---
title: Azure Monitor에서 IT 서비스 관리 커넥터-보안 내보내기-Azure 구성
description: 이 문서에서는 itsm 제품/서비스를 Azure Monitor의 보안 내보내기와 연결 하 여 ITSM 작업 항목을 중앙에서 모니터링 하 고 관리 하기 위해 Azure를 구성 하는 방법을 보여 줍니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037543"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>보안 내보내기를 사용 하 여 ITSM 도구를 연결 하도록 Azure 구성

이 문서에서는 "보안 내보내기"를 사용 하기 위해 Azure를 구성 하는 방법에 대 한 정보를 제공 합니다.
"보안 내보내기"를 사용 하려면 다음 단계를 수행 합니다.

1. [Azure AD에 앱을 등록 합니다.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [서비스 주체를 정의 합니다.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [보안 Webhook 작업 그룹을 만듭니다.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. 파트너 환경을 구성 합니다.
    보안 내보내기는 다음 ITSM 도구와의 연결을 지원 합니다.
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Azure Active Directory 등록

Azure AD에 응용 프로그램을 등록 하려면 다음 단계를 따르세요.

1. [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md) 단계를 수행합니다.
2. Azure AD에서 **응용 프로그램 노출** 을 선택 합니다.
3. **응용 프로그램 ID URI** 에 대해 **설정** 을 선택 합니다.

   [![응용 프로그램 I D의 U R I를 설정 하는 옵션의 스크린샷](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. **저장** 을 선택합니다.

## <a name="define-service-principal"></a>서비스 사용자 정의

작업 그룹 서비스는 자사 응용 프로그램 이므로 이제 서비스를 인증 하기 위해 AAD 응용 프로그램에서 인증 토큰을 획득할 수 있는 권한이 있습니다.
선택적 단계로, 만들어진 응용 프로그램의 매니페스트에 응용 프로그램 역할을 정의할 수 있습니다 .이를 통해 특정 역할의 특정 응용 프로그램만 메시지를 보낼 수 있도록 추가 제한, 액세스를 허용할 수 있습니다. 그런 다음이 역할을 작업 그룹 서비스 주체에 할당 해야 합니다 (테 넌 트 관리자 권한이 필요 함).

이 단계는 동일한 [PowerShell 명령을](../alerts/action-groups.md#secure-webhook-powershell-script)통해 수행할 수 있습니다.

## <a name="create-a-secure-webhook-action-group"></a>보안 웹후크 작업 그룹 만들기

응용 프로그램을 Azure AD에 등록 한 후에는 작업 그룹의 보안 Webhook 작업을 사용 하 여 Azure 경고에 따라 ITSM 도구에서 작업 항목을 만들 수 있습니다.

작업 그룹은 Azure 경고에 대 한 작업을 트리거하는 모듈식 및 재사용 가능한 방법을 제공 합니다. Azure Portal에서 메트릭 경고, 활동 로그 경고 및 Azure Log Analytics 경고와 함께 작업 그룹을 사용할 수 있습니다.
작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](../alerts/action-groups.md)를 참조하세요.

작업에 webhook를 추가 하려면 보안 Webhook에 대 한 다음 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com/)에서 **모니터** 를 검색하여 선택합니다. **모니터** 창은 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.
2. **경고**  >  **관리 작업** 을 선택 합니다.
3. [작업 그룹 추가](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)를 선택하고 필드를 입력합니다.
4. **작업 그룹 이름** 상자에 이름을 입력하고 **약식 이름** 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.
5. **보안 Webhook** 를 선택 합니다.
6. 다음 세부 정보를 선택 합니다.
   1. 등록 한 Azure Active Directory 인스턴스의 개체 ID를 선택 합니다.
   2. URI에 대해 [Itsm 도구 환경](#configure-the-itsm-tool-environment)에서 복사한 webhook URL에 붙여넣습니다.
   3. **일반 경고 스키마 사용** 을 **예** 로 설정 합니다. 

   다음 이미지는 샘플 보안 Webhook 작업의 구성을 보여 줍니다.

   ![보안 Webhook 작업을 보여 주는 스크린샷](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>ITSM 도구 환경 구성

구성에는 두 단계가 포함 됩니다.

1. 보안 내보내기 정의에 대 한 URI를 가져옵니다.
2. ITSM 도구의 흐름에 따라 정의 됩니다.

## <a name="next-steps"></a>다음 단계

* [ServiceNow 보안 내보내기 구성](./itsmc-secure-webhook-connections-servicenow.md)
* [BMC 보안 내보내기 구성](./itsmc-secure-webhook-connections-bmc.md)
