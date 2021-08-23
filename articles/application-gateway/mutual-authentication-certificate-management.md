---
title: 클라이언트 인증을 위해 신뢰할 수 있는 클라이언트 CA 인증서 체인 내보내기
titleSuffix: Azure Application Gateway
description: Azure Application Gateway에서 클라이언트 인증을 위해 신뢰할 수 있는 클라이언트 CA 인증서 체인을 내보내는 방법에 대해 알아봅니다.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 15655ba313a3a3cba23289c6d829523db6735129
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536308"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>클라이언트 인증에 사용할 신뢰할 수 있는 클라이언트 CA 인증서 체인 내보내기
클라이언트 또는 클라이언트 인증을 사용하여 상호 인증을 구성하려면 Application Gateway에는 신뢰할 수 있는 클라이언트 CA 인증서 체인이 업로드되어 있어야 합니다. 여러 인증서 체인이 있는 경우 개별적으로 체인을 만들고 Application Gateway에 다른 파일로 업로드해야 합니다. 이 문서에서는 게이트웨이의 클라이언트 인증 구성에 사용할 수 있는 신뢰할 수 있는 클라이언트 CA 인증서 체인을 내보내는 방법에 대해 알아봅니다.  

## <a name="prerequisites"></a>필수 구성 요소

기존 클라이언트 인증서는 신뢰할 수 있는 클라이언트 CA 인증서 체인을 생성하는 데 필요합니다. 

## <a name="export-trusted-client-ca-certificate"></a>신뢰할 수 있는 클라이언트 CA 인증서 내보내기

Application Gateway에서 클라이언트 인증을 허용하려면 신뢰할 수 있는 클라이언트 CA 인증서가 필요합니다. 이 예제에서는 클라이언트 인증서에 대한 TLS/SSL 인증서를 사용하고, 공개 키를 내보낸 다음 공개 키에서 CA 인증서를 내보내 신뢰할 수 있는 클라이언트 CA 인증서를 가져옵니다. 그런 다음 모든 클라이언트 CA 인증서를 신뢰할 수 있는 클라이언트 CA 인증서 체인 하나로 연결합니다. 

다음 단계는 인증서에 대해 .pem 또는 .cer 파일을 내보내는 데 도움이 됩니다.

### <a name="export-public-certificate"></a>공용 인증서 내보내기 

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리** 를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크** 를 클릭한 다음 **내보내기** 를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사** 가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr* 을 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![인증서가 선택된 인증서 관리자와 모든 작업, 내보내기가 선택된 상황에 맞는 메뉴를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/export.png)

2. 마법사에서 **다음** 을 클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![인증서 내보내기](./media/certificates-for-backend-authentication/exportwizard.png)

3. **아니요, 프라이빗를 내보내지 않습니다.** 를 선택한 후, **다음** 을 클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![아니요, 프라이빗 키를 내보내지 않습니다](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음** 을 클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![Base-64로 인코딩됨](./media/certificates-for-backend-authentication/base64.png)

5. **내보낼 파일** 에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름** 에는 인증서 파일의 이름을 입력합니다. 그런 후 **다음** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
   > ![내보낼 파일을 지정하는 인증서 내보내기 마법사를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/browse.png)

6. **마침** 을 클릭하여 인증서를 내보냅니다.

    > [!div class="mx-imgBorder"]
    > ![파일 내보내기를 완료한 후의 인증서 내보내기 마법사를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/finish-screen.png),

7. 인증서가 성공적으로 내보내졌습니다.

    > [!div class="mx-imgBorder"]
    > ![성공 메시지가 있는 인증서 내보내기 마법사를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/success.png)

   내보낸 인증서는 다음과 비슷합니다.

    > [!div class="mx-imgBorder"]
    > ![인증서 기호를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>공용 인증서에서 CA 인증서 내보내기

공용 인증서를 내보낸 후 이제 공용 인증서에서 CA 인증서를 내보냅니다. 루트 CA만 있는 경우 해당 인증서만 내보내면 됩니다. 그러나 하나 이상의 중간 CA가 있는 경우 해당 CA도 모두 내보내야 합니다. 

1. 공개 키를 내보낸 후 파일을 엽니다.

    > [!div class="mx-imgBorder"]
    > ![Open authorization 인증서](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![인증서 정보](./media/mutual-authentication-certificate-management/general.png)

1. 인증 경로 탭을 선택하여 인증 기관을 표시합니다.

    > [!div class="mx-imgBorder"]
    > ![인증서 세부 정보](./media/mutual-authentication-certificate-management/cert-details.png) 

1. 루트 인증서를 선택하고 **인증서 보기** 를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![인증서 경로](./media/mutual-authentication-certificate-management/root-cert.png) 

   루트 인증서 세부 정보가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![인증서 정보](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. **세부 정보** 탭을 선택하고 **파일에 복사...** 를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![루트 인증서 복사](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. 이 시점에서는 공용 인증서의 루트 CA 인증서에 대한 세부 정보를 추출했습니다. **인증서 내보내기 마법사** 가 표시됩니다. 이전 섹션([공개 인증서 내보내기](./mutual-authentication-certificate-management.md#export-public-certificate))의 2~7단계에 따라 인증서 내보내기 마법사를 완료합니다. 

1. 이제 모든 중간 CA에 대해 이 현재 섹션([공개 인증서에서 CA 인증서 내보내기](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate))의 2~6단계를 반복하여 Base-64로 인코딩된 X.509(.CER) 형식으로 모든 중간 CA 인증서를 내보냅니다.

    > [!div class="mx-imgBorder"]
    > ![중간 인증서](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    예를 들어 *MSIT CAZ2* 중간 CA에서는 이 섹션의 2~6단계를 반복하여 자체 인증서로 추출합니다. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>모든 CA 인증서를 하나의 파일로 연결

1. 이전에 추출한 모든 CA 인증서를 사용하여 다음 명령을 실행합니다. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    결과적으로 통합된 인증서는 다음과 유사합니다.
    
    > [!div class="mx-imgBorder"]
    > ![통합된 인증서](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>다음 단계

이제 신뢰할 수 있는 클라이언트 CA 인증서 체인이 있습니다. 이를 Application Gateway의 클라이언트 인증 구성에 추가하여 게이트웨이와 상호 인증할 수 있습니다. [Portal에서 Application Gateway를 사용하여 상호 인증 구성](./mutual-authentication-portal.md) 또는 [PowerShell에서 Application Gateway를 사용하여 상호 인증 구성](./mutual-authentication-powershell.md)을 참조하세요.

