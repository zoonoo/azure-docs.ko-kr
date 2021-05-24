---
title: OPC 자격 증명 모음을 사용하여 OPC UA 디바이스의 통신 보호 - Azure | Microsoft Docs
description: OPC UA 애플리케이션을 등록하는 방법 및 OPC 자격 증명 모음을 사용하여 OPC UA 디바이스에 서명된 애플리케이션 인증서를 발급하는 방법
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646335"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>OPC 자격 증명 모음 인증서 관리 서비스 사용

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

이 문서에서는 애플리케이션을 등록하는 방법 및 OPC UA 디바이스에 서명된 애플리케이션 인증서를 발급하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="deploy-the-certificate-management-service"></a>인증서 관리 서비스 배포

먼저 Azure 클라우드에 서비스를 배포합니다. 자세한 내용은 [OPC 자격 증명 모음 인증서 관리 서비스 배포](howto-opc-vault-deploy.md)를 참조하세요.

### <a name="create-the-issuer-ca-certificate"></a>발급자 CA 인증서 만들기

아직 수행하지 않은 경우 발급자 CA 인증서를 만듭니다. 자세한 내용은 [OPC 자격 증명 모음에 대한 발급자 인증서 만들기 및 관리](howto-opc-vault-manage.md)를 참조하세요.

## <a name="secure-opc-ua-applications"></a>OPC UA 애플리케이션 보안

### <a name="step-1-register-your-opc-ua-application"></a>1단계: OPC UA 애플리케이션 등록 

> [!IMPORTANT]
> 애플리케이션을 등록하려면 쓰기 역할이 필요합니다.

1. `https://myResourceGroup-app.azurewebsites.net`에서 인증서 서비스를 열고, 로그인합니다.
2. **새로 등록** 으로 이동합니다. 애플리케이션 등록의 경우 사용자에게 적어도 쓰기 역할을 할당해야 합니다.
2. 항목 양식은 OPC UA의 명명 규칙을 따릅니다. 예를 들어 다음 스크린샷에서 OPC UA .NET Standard 스택의 [OPC UA 참조 서버](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) 샘플에 대한 설정이 표시됩니다.

   ![UA 참조 서버 등록의 스크린샷](media/howto-opc-vault-secure/reference-server-registration.png "UA 참조 서버 등록")

5. **등록** 을 선택하여 인증서 서비스 애플리케이션 데이터베이스에 애플리케이션을 등록합니다. 워크플로는 사용자에게 애플리케이션에 대해 서명된 인증서를 요청하는 다음 단계를 직접 안내합니다.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2단계: CA 서명된 애플리케이션 인증서를 사용하여 애플리케이션 보호

CSR(인증서 서명 요청)을 기반으로 서명된 인증서를 발급하여 OPC UA 애플리케이션의 보안을 유지합니다. 또는 새 프라이빗 키를 PFX 또는 PEM 형식으로 포함하는 새 키 쌍을 요청할 수 있습니다. 애플리케이션에 대해 지원되는 방법에 대한 자세한 내용은 OPC UA 디바이스의 설명서를 참조하세요. 일반적으로 CSR 방법은 네트워크를 통해 프라이빗 키를 전송할 필요가 없기 때문에 권장됩니다.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>새 키 쌍을 사용하여 새 인증서 요청

1. **애플리케이션** 으로 이동합니다.
3. 나열된 애플리케이션에 대한 **새 요청** 을 선택합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 애플리케이션에 대한 공개 키를 사용하여 프라이빗 키와 새 서명된 인증서를 요청하려면 **새 키 쌍 및 인증서 요청** 을 선택합니다.

   ![새 키 쌍 및 인증서 생성의 스크린샷](media/howto-opc-vault-secure/generate-new-key-pair.png "새 키 쌍 생성")

4. 주체와 도메인 이름을 사용하여 양식을 작성합니다. 프라이빗 키에 대해 PEM 또는 PFX(암호 포함)를 선택합니다. **새 키 쌍 생성** 을 선택하여 인증서 요청을 만듭니다.

   ![인증서 요청 세부 정보 보기 화면 및 새 키 쌍 생성 단추를 보여 주는 스크린샷](media/howto-opc-vault-secure/approve-reject.png "인증서 승인")

5. 승인에는 승인자 역할을 가진 사용자와 Azure Key Vault에 대한 서명 권한이 필요합니다. 일반적인 워크플로에서 승인자 및 요청자 역할을 다른 사용자에게 할당해야 합니다. **승인** 또는 **거부** 를 선택하여 키 쌍의 실제 생성과 서명 작업을 시작하거나 취소합니다. 새 키 쌍은 인증서 요청자에 의해 다운로드될 때까지 Azure Key Vault에서 안전하게 생성되고 저장됩니다. 공개 키가 포함된 결과 인증서는 CA에서 서명됩니다. 이러한 작업을 완료하는 데 몇 초 정도 걸릴 수 있습니다.

   ![아래쪽에 승인 메시지를 포함한 인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/view-key-pair.png "키 쌍 보기")

7. 결과 프라이빗 키(PFX 또는 PEM) 및 인증서(DER)는 여기에서 이진 파일 다운로드로 선택한 형식으로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서를 복사하여 붙여 넣는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
8. 프라이빗 키를 다운로드하여 안전하게 저장한 후 **프라이빗 키 삭제** 를 선택할 수 있습니다. 공개 키가 있는 인증서는 나중에 사용할 수 있는 상태로 남아 있습니다.
9. CA 서명 인증서를 사용하기 때문에 CA 인증서 및 CRL(인증서 해지 목록)도 여기에서 다운로드해야 합니다.

이제 OPC UA 디바이스에 따라 새 키 쌍을 적용하는 방법을 결정합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사되고, 애플리케이션 인증서의 공개 키와 프라이빗 키는 인증서 저장소의 `own` 폴더에 적용됩니다. 일부 디바이스는 인증서 업데이트를 위해 서버 푸시를 이미 지원할 수 있습니다. OPC UA 디바이스의 설명서를 참조하세요.

#### <a name="request-a-new-certificate-with-a-csr"></a>CSR을 사용하여 새 인증서 요청 

1. **애플리케이션** 으로 이동합니다.
3. 나열된 애플리케이션에 대한 **새 요청** 을 선택합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 애플리케이션에 대한 새 서명된 인증서를 요청하려면 **서명 요청으로 새 인증서 요청** 을 선택합니다.

   ![새 인증서 생성의 스크린샷](media/howto-opc-vault-secure/generate-new-certificate.png "새 인증서 생성")

4. 로컬 파일을 선택하거나 형식으로 base64 인코딩된 CSR을 붙여넣어 CSR을 업로드합니다. **새 인증서 생성** 을 선택합니다.

   ![인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/approve-reject-csr.png "CSR 승인")

5. 승인에는 승인자 역할을 가진 사용자와 Azure Key Vault에 대한 서명 권한이 필요합니다. **승인** 또는 **거부** 를 선택하여 실제 서명 작업을 시작하거나 취소합니다. 공개 키가 포함된 결과 인증서는 CA에서 서명됩니다. 이 작업을 완료하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 요청 세부 정보 보기를 표시하고 맨 아래에 승인 메시지를 포함하는 스크린샷](media/howto-opc-vault-secure/view-cert-csr.png "인증서 보기")

6. 결과 인증서(DER)는 여기에서 이진 파일로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서를 복사하여 붙여 넣는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
10. 인증서를 다운로드하여 안전하게 저장한 후 **인증서 삭제** 를 선택할 수 있습니다.
11. CA 서명 인증서를 사용하기 때문에 CA 인증서 및 CRL도 여기에서 다운로드해야 합니다.

이제 OPC UA 디바이스에 따라 새 인증서를 적용하는 방법을 결정합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사되고, 애플리케이션 인증서는 인증서 저장소의 `own` 폴더에 적용됩니다. 일부 디바이스는 인증서 업데이트를 위해 서버 푸시를 이미 지원할 수 있습니다. OPC UA 디바이스의 설명서를 참조하세요.

### <a name="step-3-device-secured"></a>3단계: 보안된 디바이스

이제 OPC UA 디바이스는 추가 구성 없이 CA 서명된 인증서로 보호되는 다른 OPC UA 디바이스와 통신할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

OPC UA 디바이스를 보호하는 방법을 배웠으므로 이제 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [보안 인증서 관리 서비스 실행](howto-opc-vault-secure-ca.md)
