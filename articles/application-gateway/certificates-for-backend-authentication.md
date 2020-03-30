---
title: 백 엔드 서버를 허용하는 데 필요한 인증서
titleSuffix: Azure Application Gateway
description: 이 문서에서는 TLS/SSL 인증서를 인증 인증서및 Azure 응용 프로그램 게이트웨이에서 백 엔드 인스턴스를 허용하는 데 필요한 신뢰할 수 있는 루트 인증서로 변환하는 방법에 대한 예제를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133040"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Azure 응용 프로그램 게이트웨이를 사용하여 백 엔드를 허용하는 인증서 만들기

TLS를 끝까지 수행하려면 응용 프로그램 게이트웨이에서 인증/신뢰할 수 있는 루트 인증서를 업로드하여 백 엔드 인스턴스를 허용해야 합니다. v1 SKU의 경우 인증 인증서가 필요하지만 v2 SKU의 경우 인증서를 허용하려면 신뢰할 수 있는 루트 인증서가 필요합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - 백 엔드 인증서에서 인증 인증서 내보내기(v1 SKU용)
> - 백 엔드 인증서에서 신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)

## <a name="prerequisites"></a>사전 요구 사항

응용 프로그램 게이트웨이를 사용하여 백 엔드 인스턴스를 허용하는 데 필요한 인증 인증서 또는 신뢰할 수 있는 루트 인증서를 생성하는 데 기존 백 엔드 인증서가 필요합니다. 백 엔드 인증서는 TLS/SSL 인증서와 같거나 보안을 추가하기 위해 다를 수 있습니다. 응용 프로그램 게이트웨이는 TLS/SSL 인증서를 만들거나 구입하는 메커니즘을 제공하지 않습니다. 테스트 목적으로 자체 서명된 인증서를 만들 수 있지만 프로덕션 워크로드에 사용해서는 안 됩니다. 

## <a name="export-authentication-certificate-for-v1-sku"></a>내보내기 인증 인증서(v1 SKU용)

응용 프로그램 게이트웨이 v1 SKU에서 백 엔드 인스턴스를 허용하려면 인증 인증서가 필요합니다. 인증 인증서는 Base-64 인코딩된 X.509(에서 백 엔드 서버 인증서의 공개 키입니다. CER) 형식. 이 예제에서는 백 엔드 인증서에 대 한 TLS/SSL 인증서를 사용 하 고 인증 인증으로 사용할 공개 키를 내보냅니다. 또한 이 예제에서는 Windows 인증서 관리자 도구를 사용하여 필요한 인증서를 내보냅니다. 편리한 다른 도구를 사용하도록 선택할 수 있습니다.

TLS/SSL 인증서에서 개인 키가 아닌 공개 키 .cer 파일을 내보냅니다. 다음 단계는 Base-64 인코딩된 X.509(에서 .cer 파일을 내보내는 데 도움이 됩니다. CER) 인증서에 대한 형식:

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리**를 엽니다. 일반적으로 '인증서 - 현재 사용자\개인\인증서'에서 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr*을 입력합니다.

   ![내보내기](./media/certificates-for-backend-authentication/export.png)

2. 마법사에서 **다음**을 클릭합니다.

   ![인증서 내보내기](./media/certificates-for-backend-authentication/exportwizard.png)

3. **아니요, 프라이빗를 내보내지 않습니다.** 를 선택한 후, **다음**을 클릭합니다.

   ![아니요, 프라이빗 키를 내보내지 않습니다](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음**을 클릭합니다.

   ![Base-64로 인코딩됨](./media/certificates-for-backend-authentication/base64.png)

5. **내보낼 파일**에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름**에는 인증서 파일의 이름을 입력합니다. **다음**을 클릭합니다.

   ![찾아보기](./media/certificates-for-backend-authentication/browse.png)

6. **마침**을 클릭하여 인증서를 내보냅니다.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. 인증서가 성공적으로 내보내졌습니다.

   ![Success](./media/certificates-for-backend-authentication/success.png)

   내보낸 인증서는 다음과 비슷합니다.

   ![내보냄](./media/certificates-for-backend-authentication/exported.png)

8. 메모장을 사용하여 내보낸 인증서를 열면 이 예제와 비슷한 내용이 표시됩니다. 파란색 섹션에는 응용 프로그램 게이트웨이에 업로드되는 정보가 포함되어 있습니다. 메모장을 사용하여 인증서를 열고 이와 유사하지 않은 경우 일반적으로 Base-64 인코딩된 X.509(를 사용하여 내보내지 않았음을 의미합니다. CER) 형식. 또한 다른 텍스트 편집기를 사용하려면 일부 편집자가 백그라운드에서 의도하지 않은 형식을 도입할 수도 있음을 이해해야 합니다. 이 경우 인증서의 텍스트를 Azure로 업로드할 때 문제가 발생할 수 있습니다.

   ![메모장에서 열기](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)

신뢰할 수 있는 루트 인증서는 응용 프로그램 게이트웨이 v2 SKU에서 백 엔드 인스턴스를 화이트리스트에 포함해야 합니다. 루트 인증서는 Base-64 인코딩된 X.509(입니다. CER) 백 엔드 서버 인증서에서 루트 인증서를 포맷합니다. 이 예제에서는 백 엔드 인증서에 대 한 TLS/SSL 인증서를 사용 하 여 공개 키를 내보낸 다음 base64 인코딩 된 형식의 공개 키에서 신뢰할 수 있는 CA의 루트 인증서를 내보내 신뢰할 수 있는 루트 인증서를 가져옵니다. 중간 인증서는 서버 인증서와 함께 번들로 묶고 백 엔드 서버에 설치해야 합니다.

다음 단계는 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 위의 **백 엔드 인증서(v1 SKU의 경우)의 내보내기 인증 인증서** 섹션에 언급된 1-9단계를 사용하여 백 엔드 인증서에서 공개 키를 내보냅니다.

2. 공개 키를 내보내면 파일을 엽니다.

   ![오픈 인증 인증서](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![인증서 에 대한](./media/certificates-for-backend-authentication/general.png)

3. 인증 경로 보기로 이동하여 인증 기관을 봅니다.

   ![인증서 세부 정보](./media/certificates-for-backend-authentication/certdetails.png)

4. 루트 인증서를 선택하고 **인증서 보기**를 클릭합니다.

   ![인증서 경로](./media/certificates-for-backend-authentication/rootcert.png)

   루트 인증서 세부 정보가 표시됩니다.

   ![인증서 정보](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. **세부 정보** 보기로 이동하여 **파일로 복사를 클릭합니다...**

   ![루트 인증서 복사](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 이 시점에서 백 엔드 인증서에서 루트 인증서의 세부 정보를 추출했습니다. **인증서 내보내기 마법사가**표시됩니다. 이제 위의 **백 엔드 인증서(v1 SKU의 경우)에서** 인증 인증서내보내기 섹션에서 언급한 2-9단계를 사용하여 Base-64 인코딩된 X.509(에서 신뢰할 수 있는 루트 인증서를 내보냅니다.). CER) 형식.

## <a name="next-steps"></a>다음 단계

이제 Base-64 인코딩된 X.509(인증 된 루트 인증서)에 인증 인증서 /신뢰할 수 있는 루트 인증서가 있습니다. CER) 형식. 이를 응용 프로그램 게이트웨이에 추가하여 종단 간 TLS 암호화를 위해 백 엔드 서버를 화이트리스트로 만들 수 있습니다. [PowerShell을 사용하여 응용 프로그램 게이트웨이를 사용하여 종단 간 TLS 구성을](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)참조하십시오.

