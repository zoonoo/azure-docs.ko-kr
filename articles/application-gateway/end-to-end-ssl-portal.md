---
title: 빠른 시작-Azure 애플리케이션 게이트웨이를 사용 하 여 종단 간 SSL 암호화 구성-Azure Portal | Microsoft Docs
description: Azure Portal를 사용 하 여 종단 간 SSL 암호화를 사용 하는 Azure 애플리케이션 게이트웨이를 만드는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097153"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>포털에서 Application Gateway를 사용 하 여 종단 간 SSL 구성

이 문서에서는 Azure Portal를 사용 하 여 application gateway v1 SKU를 사용 하 여 종단 간 SSL 암호화를 구성 하는 방법을 보여 줍니다.  

> [!NOTE]
> Application Gateway v2 SKU에는 종단 간 구성을 사용 하기 위해 신뢰할 수 있는 루트 인증서가 필요 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

Application Gateway를 사용 하 여 종단 간 SSL을 구성 하려면 게이트웨이에 대 한 인증서가 필요 하며 백 엔드 서버에 인증서가 필요 합니다. 게이트웨이 인증서는 SSL 프로토콜 사양에 따라 대칭 키를 파생하는 데 사용됩니다. 대칭 키는 게이트웨이로 전송 된 트래픽을 암호화 하 고 암호 해독 하는 데 사용 됩니다. 종단 간 SSL 암호화의 경우 응용 프로그램 게이트웨이에서 올바른 백 엔드 서버를 허용 해야 합니다. 이렇게 하려면 인증 인증서 (v1) 또는 신뢰할 수 있는 루트 인증서 (v2) 라고도 하는 백 엔드 서버의 공용 인증서를 Application Gateway 업로드 합니다. 인증서를 추가 하면 Application Gateway 알려진 백 엔드 인스턴스와만 통신 합니다. 그러면 엔드투엔드 통신의 보안이 유지됩니다.

자세히 알아보려면 [ssl 종료 및 종단 간 ssl](https://docs.microsoft.com/azure/application-gateway/ssl-overview)을 참조 하세요.

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>종단 간 SSL을 사용 하 여 새 응용 프로그램 게이트웨이 만들기

종단 간 SSL 암호화를 사용 하 여 새 응용 프로그램 게이트웨이를 만들려면 먼저 새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료를 사용 하도록 설정 해야 합니다. 이렇게 하면 클라이언트와 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화를 사용할 수 있습니다. 그런 다음 응용 프로그램 게이트웨이와 백 엔드 서버 간 통신에 SSL 암호화를 사용 하도록 설정 하 고 종단 간 SSL 암호화를 수행 하려면 HTTP 설정에서 백 엔드 서버용 인증서를 허용 목록 해야 합니다.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료를 사용 하도록 설정

[새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료를 사용 하도록 설정](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)하는 방법을 이해 하려면이 문서를 참조 하세요.

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>백 엔드 서버의 인증/루트 인증서 추가

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택 합니다. 응용 프로그램 게이트웨이를 만들 때 Azure에서 기본 HTTP 설정인 **appGatewayBackendHttpSettings**을 자동으로 만들었습니다. 

3. **AppGatewayBackendHttpSettings**를 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 에 대 한 창이 표시 됩니다. 

5. **새로 만들기**를 선택 합니다.

6. 적절 한 **이름을**입력 합니다.

7. **CER 인증서 업로드** 상자를 사용 하 여 인증서 파일을 선택 합니다.

   표준 및 WAF (v1) 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드 해야 합니다.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 **루트 인증서** 를 .cer 형식으로 업로드 해야 합니다. 백 엔드 인증서를 잘 알려진 CA에서 발급 한 경우 "잘 알려진 CA 인증서 사용" 상자를 확인할 수 있으며 인증서를 업로드할 필요가 없습니다.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **저장**을 선택합니다.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>기존 application gateway에 대 한 종단 간 SSL 사용

종단 간 SSL 암호화를 사용 하 여 기존 응용 프로그램 게이트웨이를 구성 하려면 먼저 수신기에서 SSL 종료를 사용 하도록 설정 해야 합니다. 이렇게 하면 클라이언트와 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화를 사용할 수 있습니다. 그런 다음 응용 프로그램 게이트웨이와 백 엔드 서버 간 통신에 SSL 암호화를 사용 하도록 설정 하 고 종단 간 SSL 암호화를 수행 하려면 HTTP 설정에서 백 엔드 서버용 인증서를 허용 목록 해야 합니다.

SSL 종료를 사용 하도록 설정 하려면 HTTPS 프로토콜 및 인증서를 사용 하는 수신기를 사용 해야 합니다. 따라서 HTTPS 프로토콜 및 인증서를 사용 하 여 기존 수신기를 사용 하도록 선택 하거나 새 수신기를 만들 수 있습니다. 이전을 선택 하는 경우 아래 언급 된 단계를 무시 하 여 **기존 응용 프로그램 게이트웨이에서 SSL 종료를 사용 하도록 설정** 하 고 직접 이동 하 여 **백 엔드 서버에 대 한 인증/신뢰할 수 있는 루트 인증서를 추가할** 수 있습니다. 후자를 선택 하는 경우 다음 단계를 사용 합니다.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>기존 application gateway에서 SSL 종료 사용

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **수신기** 를 선택 합니다.

3. 요구 사항에 따라 **기본** 및 **다중 사이트** 수신기 중에서 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **인증서** 에 대 한 창이 표시 됩니다.

5. 클라이언트와 응용 프로그램 게이트웨이 간에 SSL 종료에 사용 하려는 PFX 인증서를 업로드 합니다.

   > [!NOTE]
   > 테스트 목적으로 자체 서명 된 인증서를 사용할 수 있습니다. 그러나 프로덕션 워크 로드에 대 한 권장은 없지만 관리가 어렵고 완전히 안전 하지 않기 때문입니다. [자체 서명 된 인증서를 만드는](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)방법에 대해 알아봅니다.

6. 요구 사항에 따라 **수신기** 에 대 한 다른 필수 설정을 추가 합니다.

7. **확인**을 선택하여 저장합니다.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/신뢰할 수 있는 루트 인증서 추가

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택 합니다. 기존 백 엔드 HTTP 설정에서 인증서를 허용 목록 하거나 새 HTTP 설정을 만들 수 있습니다. 아래 단계에서는 기본 HTTP 설정인 **appGatewayBackendHttpSettings**에 대 한 인증서를 허용 목록 합니다.

3. **AppGatewayBackendHttpSettings**를 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 에 대 한 창이 표시 됩니다. 

5. **새로 만들기**를 선택 합니다.

6. 적절 한 **이름을**입력 합니다.

7. **CER 인증서 업로드** 상자를 사용 하 여 인증서 파일을 선택 합니다.

   표준 및 WAF (v1) 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드 해야 합니다.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 **루트 인증서** 를 .cer 형식으로 업로드 해야 합니다. 백 엔드 인증서를 잘 알려진 CA에서 발급 한 경우 "잘 알려진 CA 인증서 사용" 상자를 확인할 수 있으며 인증서를 업로드할 필요가 없습니다.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
