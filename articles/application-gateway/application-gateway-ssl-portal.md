---
title: "SSL 오프로드 구성 - Azure Application Gateway - Azure Portal | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Azure Portal을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트 엔드 서버 설치 및 웹 응용 프로그램의 관리를 간소화합니다.

## <a name="scenario"></a>시나리오

다음 시나리오에서는 기존 응용 프로그램 게이트웨이에서 SSL 오프로드를 구성하는 과정을 살펴봅니다. 이 시나리오에서는 [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md)단계를 이미 수행한 것으로 가정합니다.

## <a name="before-you-begin"></a>시작하기 전에

응용 프로그램 게이트웨이에서 SSL 오프로드를 구성하려면 인증서가 필요합니다. 이 인증서는 응용 프로그램 게이트웨이에 로드되며 SSL을 통해 전송된 트래픽을 암호화 및 해독하는 데 사용됩니다. 인증서는 개인 정보 교환(.pfx) 형식이어야 합니다. 이 파일 형식을 사용하면 응용 프로그램 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 개인 키를 내보낼 수 있습니다.

## <a name="add-an-https-listener"></a>HTTPS 수신기 추가

HTTPS 수신기는 구성에 따라 트래픽을 확인하며, 백 엔드 풀로 트래픽을 라우팅하도록 도와줍니다. HTTPS 수신기를 추가하려면 다음 단계를 수행합니다.

   1. Azure Portal로 이동하여 기존 응용 프로그램 게이트웨이를 선택합니다.

   2. **수신기**를 선택한 다음 **추가** 단추를 선택하여 수신기를 추가합니다.

   ![Application Gateway 개요 창][1]


   3. 수신기에 대한 다음 필수 정보를 입력하고 .pfx 인증서를 업로드합니다.
      - **이름**: 수신기의 이름입니다.

      - **프런트 엔드 IP 구성**: 수신기에 사용되는 프런트 엔드 IP 구성입니다.

      - **프런트 엔드 포트(이름/포트)**: 응용 프로그램 게이트웨이의 프런트 엔드에서 사용되는 포트 및 실제로 사용되는 포트의 이름입니다.

      - **프로토콜**: 프런트 엔드에 HTTPS 또는 HTTP가 사용되는지 여부를 결정하는 스위치입니다.

      - **인증서(이름/암호)**: SSL 오프로드를 사용하는 경우 이 설정에는 .pfx 인증서가 필요합니다. 이름 및 암호도 필요합니다.

   4. **확인**을 선택합니다.

![수신기 창 추가][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>규칙을 만들고 수신기에 연결

이제 수신기가 만들어졌습니다. 다음으로 수신기에서 트래픽을 처리할 규칙을 만듭니다. 규칙은 여러 구성 설정에 따라 트래픽이 백 엔드 풀에 전달되는 방식을 정의합니다. 이러한 설정에는 프로토콜, 포트 및 상태 프로브와 쿠키 기반 세션 선호도가 사용되는지 여부를 포함합니다. 규칙을 만들고 수신기에 연결하려면 다음 단계를 따릅니다.


   1. 응용 프로그램 게이트웨이의 **규칙**을 선택한 다음 **추가**를 선택합니다.

   ![Application Gateway 규칙 창][3]


   2. **기본 규칙 추가** 아래에서 **이름** 필드에 규칙의 이름을 입력한 다음 이전 단계에서 만든 **수신기**를 선택합니다. 적절한 **백 엔드 풀** 및 **HTTP 설정**을 선택한 다음 **확인**을 선택합니다.

   ![HTTPS 설정 창][4]

이제 설정이 응용 프로그램 게이트웨이에 저장됩니다. 이러한 설정의 저장 프로세스에는 약간의 시간이 걸릴 수 있으며, 그런 다음 포털 또는 PowerShell을 통해 볼 수 있습니다. 저장된 후 응용 프로그램 게이트웨이에서 트래픽의 암호화 및 암호 해독을 처리합니다. 응용 프로그램 게이트웨이와 백 엔드 웹 서버 간의 모든 트래픽은 HTTP를 통해 처리됩니다. HTTPS를 통해 시작된 경우에는 클라이언트와의 모든 통신이 암호화된 상태로 클라이언트에 반환됩니다.

## <a name="next-steps"></a>다음 단계

Azure Application Gateway를 사용하여 사용자 지정 상태 프로브를 구성하는 방법을 알아보려면 [사용자 지정 상태 프로브 만들기](application-gateway-create-gateway-portal.md)를 참조하세요.

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

