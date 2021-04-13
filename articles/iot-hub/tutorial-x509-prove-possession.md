---
title: 자습서 - Azure IoT Hub에서 CA 인증서의 소유권 증명 | Microsoft Docs
description: 자습서 - Azure IoT Hub에 대한 CA 인증서를 소유하고 있음을 증명
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 5e2827a4f87398f0a37ef04f797d2c7276d1a66d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384173"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>자습서: CA 인증서 소유 증명

루트 CA(인증 기관) 인증서 또는 하위 CA 인증서를 IoT 허브에 업로드한 후에는 인증서를 소유하고 있음을 증명해야 합니다.

1. Azure Portal에서 IoT Hub로 이동하고, **설정 > 인증서** 를 차례로 선택합니다.

2. **추가** 를 선택하여 새 CA 인증서를 추가합니다.

3. **인증서 이름** 필드에 표시 이름을 입력하고 추가할 PEM 인증서를 선택합니다.

4. **저장** 을 선택합니다. 인증서가 **확인되지 않음** 상태로 인증서 목록에 표시됩니다. 이 확인 프로세스는 인증서를 소유하고 있음을 증명합니다.

5. 인증서를 선택하여 **인증서 세부 정보** 대화 상자를 표시합니다.

6. 대화 상자에서 **확인 코드 생성** 을 선택합니다.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{인증서 세부 정보 대화 상자}":::

7. 확인 코드를 클립보드에 복사합니다. 인증 코드를 인증서 주체로 설정해야 합니다. 예를 들어 확인 코드가 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3인 경우 다음 단계에 표시된 대로 인증서의 주체로 추가합니다.

8. 확인 인증서를 생성하는 방법에는 다음 세 가지가 있습니다.

    * Microsoft에서 제공하는 PowerShell 스크립트를 사용하는 경우 `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"`을 실행하여 `VerifyCert4.cer`이라는 인증서를 만듭니다. 자세한 내용은 [Microsoft 제공 스크립트 사용](tutorial-x509-scripts.md)을 참조하세요.

    * Microsoft에서 제공하는 Bash 스크립트를 사용하는 경우 `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"`을 실행하여 `verification-code.cert.pem`이라는 인증서를 만듭니다. 자세한 내용은 [Microsoft 제공 스크립트 사용](tutorial-x509-scripts.md)을 참조하세요.

    * OpenSSL을 사용하여 인증서를 생성하는 경우 먼저 프라이빗 키와 CSR(인증서 서명 요청)을 생성해야 합니다.

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      그런 다음, 루트 CA 구성 파일(아래에 표시) 또는 하위 CA 구성 파일 및 CSR을 사용하여 인증서를 만듭니다.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    자세한 내용은 [OpenSSL을 사용하여 테스트 인증서 만들기](tutorial-x509-openssl.md)를 참조하세요.

10. **인증서 세부 정보** 보기에서 새 인증서를 선택합니다.

11. 인증서를 업로드한 후 **확인** 을 선택합니다. CA 인증서 상태를 **확인됨** 으로 변경해야 합니다.
