---
title: Azure Monitor에서 IT 서비스 관리 커넥터-보안 내보내기-BMC를 사용 하 여 구성
description: 이 문서에서는 Azure Monitor의 보안 내보내기에서 ITSM 제품/서비스를 BMC와 연결 하는 방법을 보여 줍니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 5a78dc3923c8183db6dd2ddea1d2233149201c07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620834"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix Azure Monitor에 연결

다음 섹션에서는 Azure에서 BMC Helix 제품 및 보안 내보내기를 연결 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 요소를 충족 하는지 확인 합니다.

* Azure AD가 등록 됩니다.
* 지원 되는 버전의 BMC Helix 다중 클라우드 서비스 관리 (버전 19.08 이상)가 있습니다.

## <a name="configure-the-bmc-helix-connection"></a>BMC Helix 연결 구성

1. 보안 내보내기에 대 한 URI를 가져오기 위해 BMC Helix 환경에서 다음 절차를 사용 합니다.

   1. Integration Studio에 로그인 합니다.
   1. **Azure 경고 흐름에서 인시던트 만들기** 를 검색 합니다.
   1. Webhook URL을 복사 합니다.
   
   ![Integration Studio의 webhook U R L 스크린샷](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. 버전에 따라 다음 지침을 따르세요.
   * [버전 20.02에 대해 Azure Monitor와의 미리 작성 된 통합을 사용 하도록 설정](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)합니다.
   * [버전 19.11에 대해 Azure Monitor와의 미리 작성 된 통합을 사용 하도록 설정](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)합니다.

3. BMC Helix의 연결 구성의 일부로 통합 BMC 인스턴스로 이동 하 여 다음 지침을 따릅니다.

   1. **카탈로그** 를 선택 합니다.
   2. **Azure alerts** 를 선택 합니다.
   3. **커넥터** 를 선택 합니다.
   4. **구성** 을 선택 합니다.
   5. **새 연결 구성 추가** 를 선택 합니다.
   6. 구성 섹션에 대 한 정보를 입력 합니다.
      - **이름**: 직접 설정 합니다.
      - **권한 부여 유형**: **없음**
      - **설명**: 직접 작성 합니다.
      - **사이트**: **클라우드**
      - **인스턴스 수**: **2**(기본값)
      - **선택: 기본적** 으로 사용 하도록 설정 합니다.
      - Azure 테 넌 트 ID 및 Azure 응용 프로그램 ID는 앞에서 정의한 응용 프로그램에서 가져옵니다.

![BMC 구성을 보여 주는 스크린샷](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
