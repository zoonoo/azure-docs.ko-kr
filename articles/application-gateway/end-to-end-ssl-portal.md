---
title: 포털을 사용하여 종단 간 TLS 암호화 구성
titleSuffix: Azure Application Gateway
description: Azure 포털을 사용하여 종단 간 TLS 암호화를 사용하여 응용 프로그램 게이트웨이를 만드는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133004"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>포털에서 응용 프로그램 게이트웨이를 사용하여 종단 간 TLS 구성

이 문서에서는 Azure 포털을 사용하여 Azure 응용 프로그램 게이트웨이 v1 SKU를 통해 이전에 SSL(보안 소켓 계층) 암호화라고 하는 종단 간 전송 계층 보안(TLS) 암호화를 구성하는 방법을 설명합니다.

> [!NOTE]
> 응용 프로그램 게이트웨이 v2 SKU는 종단 간 구성을 사용하도록 설정하기 위해 신뢰할 수 있는 루트 인증서가 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 게이트웨이를 사용하여 종단 간 TLS를 구성하려면 게이트웨이에 대한 인증서가 필요합니다. 백 엔드 서버에도 인증서가 필요합니다. 게이트웨이 인증서는 TLS 프로토콜 사양을 준수하는 대칭 키를 파생하는 데 사용됩니다. 그런 다음 대칭 키를 사용하여 게이트웨이로 전송된 트래픽을 암호화하고 해독합니다. 

종단 간 TLS 암호화의 경우 응용 프로그램 게이트웨이에서 올바른 백 엔드 서버를 허용해야 합니다. 이 액세스를 허용하려면 인증 인증서(v1) 또는 신뢰할 수 있는 루트 인증서(v2)라고도 하는 백 엔드 서버의 공용 인증서를 응용 프로그램 게이트웨이에 업로드합니다. 인증서를 추가하면 응용 프로그램 게이트웨이가 알려진 백 엔드 인스턴스와만 통신할 수 있습니다. 이 구성은 종단 간 통신을 더욱 보호합니다.

자세한 내용은 [응용 프로그램 게이트웨이를 사용하여 TLS 종료 및 끝 TLS에](https://docs.microsoft.com/azure/application-gateway/ssl-overview)대한 개요를 참조하십시오.

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>종단 간 TLS를 사용하여 새 애플리케이션 게이트웨이 만들기

종단 간 TLS 암호화를 사용하여 새 응용 프로그램 게이트웨이를 만들려면 새 응용 프로그램 게이트웨이를 만드는 동안 먼저 TLS 종료를 사용하도록 설정해야 합니다. 이 작업을 사용하면 클라이언트와 응용 프로그램 게이트웨이 간의 통신을 위해 TLS 암호화를 사용할 수 있습니다. 그런 다음 HTTP 설정에서 백 엔드 서버에 대한 인증서를 나열하는 안전 받는 사람에 넣어야합니다. 이 구성을 사용하면 응용 프로그램 게이트웨이와 백 엔드 서버 간의 통신을 위해 TLS 암호화를 사용할 수 있습니다. 이를 통해 종단 간 TLS 암호화가 수행됩니다.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>새 애플리케이션 게이트웨이를 만드는 동안 TLS 종료 를 활성화합니다.

자세한 내용은 [새 응용 프로그램 게이트웨이를 만드는 동안 TLS 종료 를 사용하도록 설정합니다.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/루트 인증서 추가

1. **모든 리소스를**선택한 다음 **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정을** 선택합니다. Azure는 응용 프로그램 게이트웨이를 만들 때 기본 HTTP 설정인 **appGatewayBackendHttpSettings를**자동으로 만들었습니다. 

3. **앱게이트웨이백엔드Http설정.**

4. **프로토콜에서** **HTTPS를**선택합니다. 백 엔드 **인증 인증서 또는 신뢰할 수 있는 루트 인증서에** 대 한 창이 나타납니다.

5. **새로 만들기**를 선택합니다.

6. **이름** 필드에 적합한 이름을 입력합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택합니다.

   표준 및 WAF(v1) 응용 프로그램 게이트웨이의 경우 .cer 형식으로 백 엔드 서버 인증서의 공개 키를 업로드해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 .cer 형식으로 백 엔드 서버 인증서의 루트 인증서를 업로드해야 합니다. 잘 알려진 CA(공인 인증서 기관)에서 백 엔드 인증서를 발급한 경우 **잘 알려진 CA 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서 추가](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![루트 인증서](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **저장**을 선택합니다.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에 대한 종단 간 TLS 사용

종단 간 TLS 암호화를 사용하여 기존 응용 프로그램 게이트웨이를 구성하려면 먼저 수신기에서 TLS 종료를 사용하도록 설정해야 합니다. 이 작업을 사용하면 클라이언트와 응용 프로그램 게이트웨이 간의 통신을 위해 TLS 암호화를 사용할 수 있습니다. 그런 다음 보안 받는 사람 목록의 HTTP 설정에 백 엔드 서버에 대한 인증서를 넣습니다. 이 구성을 사용하면 응용 프로그램 게이트웨이와 백 엔드 서버 간의 통신을 위해 TLS 암호화를 사용할 수 있습니다. 이를 통해 종단 간 TLS 암호화가 수행됩니다.

HTTPS 프로토콜과 TLS 종료를 활성화하기 위한 인증서가 있는 수신기를 사용해야 합니다. 이러한 조건을 충족하는 기존 수신기를 사용하거나 새 수신기를 만들 수 있습니다. 이전 옵션을 선택한 경우 다음 "기존 응용 프로그램 게이트웨이에서 TLS 종료 사용" 섹션을 무시하고 "백 엔드 서버에 대한 인증/신뢰할 수 있는 루트 인증서 추가" 섹션으로 직접 이동할 수 있습니다.

후자의 옵션을 선택하는 경우 다음 절차의 단계를 적용합니다.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에서 TLS 종료 활성화

1. **모든 리소스를**선택한 다음 **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **리스너를** 선택합니다.

3. 요구 사항에 따라 **기본** 또는 **다중 사이트** 수신기 중 하나를 선택합니다.

4. **프로토콜에서** **HTTPS를**선택합니다. **인증서창이** 나타납니다.

5. 클라이언트와 응용 프로그램 게이트웨이 간의 TLS 종료에 사용할 PFX 인증서를 업로드합니다.

   > [!NOTE]
   > 테스트 목적으로 자체 서명된 인증서를 사용할 수 있습니다. 그러나 관리하기 어렵고 완전히 안전하지 않기 때문에 프로덕션 워크로드에는 이 방법을 권장하지 않습니다. 자세한 내용은 [자체 서명된 인증서 만들기를](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)참조하십시오.

6. 요구 사항에 따라 **리스너에**필요한 다른 설정을 추가합니다.

7. **확인**을 선택하여 저장합니다.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/신뢰할 수 있는 루트 인증서 추가

1. **모든 리소스를**선택한 다음 **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정을** 선택합니다. 인증서를 기존 백 엔드 HTTP 설정에 안전 받는 사람 목록에 넣거나 새 HTTP 설정을 만들 수 있습니다. 다음 단계에서는 기본 HTTP 설정인 **appGatewayBackendHttpSettings에**대한 인증서가 안전 받는 사람 목록에 추가됩니다.

3. **앱게이트웨이백엔드Http설정.**

4. **프로토콜에서** **HTTPS를**선택합니다. 백 엔드 **인증 인증서 또는 신뢰할 수 있는 루트 인증서에** 대 한 창이 나타납니다. 

5. **새로 만들기**를 선택합니다.

6. **이름** 필드에 적합한 이름을 입력합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택합니다.

   표준 및 WAF(v1) 응용 프로그램 게이트웨이의 경우 .cer 형식으로 백 엔드 서버 인증서의 공개 키를 업로드해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 .cer 형식으로 백 엔드 서버 인증서의 루트 인증서를 업로드해야 합니다. 잘 알려진 CA에서 백 엔드 인증서를 발급하는 경우 **잘 알려진 CA 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서 추가](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
