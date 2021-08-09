---
title: Azure Application Gateway의 상호 인증 개요
description: 이 문서는 Application Gateway의 상호 인증에 대한 개요입니다.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230955"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Application Gateway를 사용한 상호 인증 개요(미리 보기)

상호 인증 또는 클라이언트 인증을 통해 Application Gateway에서 클라이언트 전송 요청을 인증할 수 있습니다. 일반적으로 클라이언트만 Application Gateway를 인증합니다. 상호 인증을 사용하면 클라이언트와 Application Gateway 모두가 서로를 인증할 수 있습니다. 

> [!NOTE]
> TLS 1.2는 나중에 위임될 것이므로 상호 인증과 함께 TLS 1.2를 사용하는 것이 좋습니다. 

## <a name="mutual-authentication"></a>상호 인증

Application Gateway는 신뢰할 수 있는 클라이언트 CA 인증서를 Application Gateway에 업로드할 수 있는 인증서 기반 상호 인증을 지원하며, 게이트웨이에서는 해당 인증서를 사용하여 게이트웨이로 요청을 보내는 클라이언트를 인증합니다. IoT 사용 사례가 증가하고 산업 전반에서 보안 요구 사항이 증가함에 따라 상호 인증은 Application Gateway와 통신할 수 있는 클라이언트를 관리하고 제어할 수 있는 방법을 제공합니다. 

상호 인증을 구성하려면 SSL 프로필의 클라이언트 인증 부분의 일부로 신뢰할 수 있는 클라이언트 CA 인증서를 업로드해야 합니다. 상호 인증 구성을 완료하려면 SSL 프로필을 수신기에 연결해야 합니다. 업로드하는 클라이언트 인증서에는 항상 루트 CA 인증서가 있어야 합니다. 인증서 체인도 업로드할 수 있지만 체인은 원하는 수의 중간 CA 인증서 외에 루트 CA 인증서를 포함해야 합니다. 

예를 들어 클라이언트 인증서가 루트 CA 인증서, 여러 중간 CA 인증서 및 리프 인증서를 포함하는 경우 루트 CA 인증서와 모든 중간 CA 인증서가 한 파일로 Application Gateway에 업로드되었는지 확인합니다. 신뢰할 수 있는 클라이언트 CA 인증서를 추출하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 클라이언트 CA 인증서를 추출하는 방법](./mutual-authentication-certificate-management.md)을 참조하세요.

루트 CA 및 중간 CA 인증서를 사용하여 인증서 체인을 업로드하는 경우 인증서 체인을 PEM 또는 CER 파일로 게이트웨이에 업로드해야 합니다. 

> [!NOTE] 
> 상호 인증은 Standard_v2 및 WAF_v2 SKU에서만 사용할 수 있습니다. 

### <a name="certificates-supported-for-mutual-authentication"></a>상호 인증을 지원하는 인증서

Application Gateway는 다음과 같은 유형의 인증서를 지원합니다.

- CA(인증 기관) 인증서: CA 인증서는 CA(인증 기관)에서 발급한 디지털 인증서입니다.
- 자체 서명된 CA 인증서: 클라이언트 브라우저는 이러한 인증서를 신뢰하지 않으며 사용자에게 가상 서비스의 인증서가 신뢰 체인에 포함되지 않는다고 경고합니다. 자체 서명된 CA 인증서는 관리자가 클라이언트를 제어하고 브라우저의 보안 경고를 안전하게 무시할 수 있는 테스트 또는 환경에 적합합니다. 프로덕션 워크로드는 자체 서명된  CA 인증서를 사용해서는 안 됩니다.

상호 인증을 설정하는 방법에 대한 자세한 내용은 [Application Gateway로 상호 인증 구성](./mutual-authentication-portal.md)을 참조하세요.

> [!IMPORTANT]
> 상호 인증을 사용할 때는 신뢰할 수 있는 클라이언트 CA 인증서 체인 전체를 Application Gateway에 업로드해야 합니다. 

## <a name="additional-client-authentication-validation"></a>추가 클라이언트 인증 유효성 검사

### <a name="verify-client-certificate-dn"></a>클라이언트 인증서 DN 확인

클라이언트 인증서의 직접 발급자를 확인하고 Application Gateway가 해당 발급자만 신뢰하도록 허용할 수 있습니다. 이 옵션은 기본적으로 해제되어 있지만 Portal, PowerShell 또는 Azure CLI를 통해 사용하도록 설정할 수 있습니다. 

클라이언트 인증서의 직접 발급자를 확인하기 위해 Application Gateway를 사용하도록 선택하는 경우 업로드된 인증서에서 추출되는 클라이언트 인증서 발급자 DN을 결정하는 방법은 다음과 같습니다. 
* **시나리오 1:** 인증서 체인에는 루트 인증서 - 중간 인증서 - 리프 인증서가 포함됩니다. 
    * *중간 인증서의* 주체 이름은 Application Gateway가 클라이언트 인증서 발급자 DN으로 추출하고 확인할 대상의 이름입니다. 
* **시나리오 2:** 인증서 체인에는 루트 인증서 - 중간1 인증서 - 중간2 인증서 - 리프 인증서가 포함됩니다.
    * *중간2 인증서* 의 주체 이름은 클라이언트 인증서 발급자 DN으로 추출되어 확인 대상이 됩니다. 
* **시나리오 3:** 인증서 체인에는 루트 인증서 - 리프 인증서가 포함됩니다. 
    * *루트 인증서의* 주체 이름은 추출되어 클라이언트 인증서 발급자 DN으로 사용됩니다.
* **시나리오 4:** 동일한 파일에 동일한 길이의 여러 인증서 체인이 있습니다. 체인 1에는 루트 인증서 - 중간 1 인증서 - 리프 인증서가 포함됩니다. 체인 2에는 루트 인증서 - 중간 2 인증서 - 리프 인증서가 포함됩니다. 
    * *중간 1 인증서의* 주체 이름은 클라이언트 인증서 발급자 DN으로 추출됩니다.  
* **시나리오 5:** 동일한 파일에 길이가 다른 여러 인증서 체인이 있습니다. 체인 1에는 루트 인증서 - 중간 1 인증서 - 리프 인증서가 포함됩니다. 체인 2에는 루트 인증서 - 중간 2 인증서 - 중간 3 인증서 - 리프 인증서가 포함됩니다. 
    * *중간 3 인증서의* 주체 이름은 클라이언트 인증서 발급자 DN으로 추출됩니다. 

> [!IMPORTANT]
> 파일당 하나의 인증서 체인만 업로드하는 것이 좋습니다. 클라이언트 인증서 DN 확인을 사용하도록 설정하는 경우에 특히 중요합니다. 한 파일에 여러 인증서 체인을 업로드하면 시나리오 4 또는 시나리오 5에 이르게 되며 제공된 클라이언트 인증서가 체인에서 추출된 클라이언트 인증서 발급자 DN Application Gateway와 일치하지 않을 때 나중에 문제가 발생할 수 있습니다. 

신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법](./mutual-authentication-certificate-management.md)을 참조하세요.

## <a name="server-variables"></a>서버 변수 

상호 인증을 사용하는 경우 클라이언트 인증서에 대한 정보를 Application Gateway 뒤의 백 엔드 서버에 전달하는 데 사용할 수 있는 추가 서버 변수가 있습니다. 사용 가능한 서버 변수와 사용 방법에 대한 자세한 내용은 [서버 변수](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview)를 참조하세요.

## <a name="next-steps"></a>다음 단계

상호 인증에 대해 학습한 후 [PowerShell에서 상호 인증을 사용하여 Application Gateway 구성](./mutual-authentication-powershell.md)으로 이동하여 상호 인증을 사용하여 Application Gateway를 만듭니다. 

