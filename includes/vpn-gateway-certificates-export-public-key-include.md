---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553224"
---
자체 서명된 루트 인증서를 만든 후에는 프라이빗 키가 아닌 루트 인증서 공개 키 .cer 파일을 내보냅니다. 이 파일은 나중에 Azure에 업로드합니다. 다음 단계는 자체 서명된 루트 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리** 를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 자체 서명된 루트 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크** 를 클릭한 다음 **내보내기** 를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사** 가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr* 을 입력합니다.

   ![스크린샷 선택한 인증서가 있는 현재 사용자의 인증서 창과 모든 작업에서 내보내기가 선택 된 상황에 맞는 메뉴를 보여 줍니다.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. 마법사에서 **다음** 을 클릭합니다.

   ![인증서 내보내기](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. **아니요, 프라이빗를 내보내지 않습니다.** 를 선택한 후, **다음** 을 클릭합니다.

   ![아니요, 프라이빗 키를 내보내지 않습니다](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음** 을 클릭합니다.

   ![Base-64로 인코딩됨](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. **내보낼 파일** 에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름** 에는 인증서 파일의 이름을 입력합니다. 그런 후 **다음** 을 클릭합니다.

   ![스크린샷 파일 이름 텍스트 상자와 찾아보기 옵션을 사용 하 여 인증서 내보내기 마법사를 보여 줍니다.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. **마침** 을 클릭하여 인증서를 내보냅니다.

   ![선택한 설정을 사용 하 여 인증서 내보내기 마법사를 보여 주는 스크린샷](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. 인증서가 성공적으로 내보내졌습니다.

   ![내보내기가 성공적으로 완료 되었다는 메시지를 보여 주는 스크린샷](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. 내보낸 인증서는 다음과 비슷합니다.

   ![스크린샷 c e r 파일 이름 확장명을 사용 하는 인증서 아이콘 및 파일 이름을 보여 줍니다.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. 메모장을 사용하여 내보낸 인증서를 열면 이 예제와 비슷한 내용이 표시됩니다. 파란색 섹션에는 Azure에 업로드되는 정보가 포함되어 있습니다. 메모장에서 인증서를 열었지만 이와 비슷하게 표시되지 않으면, 일반적으로 Base-64로 인코딩된 X.509(.CER) 형식을 사용하여 인증서를 내보내지 않았음을 의미합니다. 또한 다른 텍스트 편집기를 사용하려면 일부 편집자가 백그라운드에서 의도하지 않은 형식을 도입할 수도 있음을 이해해야 합니다. 이 경우 인증서의 텍스트를 Azure로 업로드할 때 문제가 발생할 수 있습니다.

   ![메모장에서 열기](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
