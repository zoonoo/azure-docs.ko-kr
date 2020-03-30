---
title: OPC 볼트 - Azure를 통해 OPC UA 장치의 통신 보안 | 마이크로 소프트 문서
description: OPC UA 응용 프로그램을 등록하는 방법 및 OPC Vault를 사용하여 OPC UA 장치에 대해 서명된 응용 프로그램 인증서를 발급하는 방법.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454202"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>OPC 볼트 인증서 관리 서비스 사용

이 문서에서는 응용 프로그램을 등록하는 방법과 OPC UA 장치에 대해 서명된 응용 프로그램 인증서를 발급하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="deploy-the-certificate-management-service"></a>인증서 관리 서비스 배포

먼저 Azure 클라우드에 서비스를 배포합니다. 자세한 내용은 [OPC Vault 인증서 관리 서비스 배포를](howto-opc-vault-deploy.md)참조하십시오.

### <a name="create-the-issuer-ca-certificate"></a>발급자 CA 인증서 만들기

아직 수행하지 않은 경우 발급자 CA 인증서를 만듭니다. 자세한 내용은 [OPC Vault에 대한 발급자 인증서 만들기 및 관리를](howto-opc-vault-manage.md)참조하십시오.

## <a name="secure-opc-ua-applications"></a>안전한 OPC UA 애플리케이션

### <a name="step-1-register-your-opc-ua-application"></a>1단계: OPC UA 애플리케이션 등록 

> [!IMPORTANT]
> 응용 프로그램을 등록하려면 Writer 역할이 필요합니다.

1. 에서 `https://myResourceGroup-app.azurewebsites.net`인증서 서비스를 열고 로그인합니다.
2. 새 **등록으로 이동합니다.** 응용 프로그램 등록의 경우 사용자에게 최소한 기록기 역할이 할당되어야 합니다.
2. 항목 양식은 OPC UA의 명명 규칙을 따릅니다. 예를 들어 다음 스크린샷에는 OPC UA .NET 표준 스택의 [OPC UA 참조 서버](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) 샘플에 대한 설정이 표시됩니다.

   ![UA 참조 서버 등록 스크린샷](media/howto-opc-vault-secure/reference-server-registration.png "UA 참조 서버 등록")

5. 인증서 서비스 응용 프로그램 데이터베이스에 응용 프로그램을 등록하려면 **레지스터를** 선택합니다. 워크플로는 사용자에게 다음 단계로 직접 안내하여 응용 프로그램에 대해 서명된 인증서를 요청합니다.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2단계: CA 서명된 응용 프로그램 인증서로 응용 프로그램 보안

인증서 서명 요청(CSR)에 따라 서명된 인증서를 발급하여 OPC UA 응용 프로그램을 보호합니다. 또는 PFX 또는 PEM 형식의 새 개인 키가 포함된 새 키 쌍을 요청할 수 있습니다. 응용 프로그램에 지원되는 방법에 대한 자세한 내용은 OPC UA 장치의 설명서를 참조하십시오. 일반적으로 CSR 메서드는 와이어를 통해 개인 키를 전송할 필요가 없으므로 권장됩니다.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>새 키쌍으로 새 인증서 요청

1. 응용 **프로그램으로 이동합니다.**
3. 나열된 응용 프로그램에 대한 **새 요청을** 선택합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. **새 KeyPair 및 인증서 요청을** 선택하여 응용 프로그램에 대한 공개 키를 사용하여 개인 키와 새 서명된 인증서를 요청합니다.

   ![새 키페어링 및 인증서 생성 스크린샷](media/howto-opc-vault-secure/generate-new-key-pair.png "새 키 쌍 생성")

4. 제목과 도메인 이름으로 양식을 작성합니다. 개인 키의 경우 암호로 PEM 또는 PFX를 선택합니다. **새 키페어링 생성을** 선택하여 인증서 요청을 만듭니다.

   ![인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/approve-reject.png "인증서 승인")

5. 승인을 받으려면 승인자 역할이 있고 Azure Key Vault에서 서명 권한이 있는 사용자가 필요합니다. 일반적인 워크플로에서는 승인자 및 요청자 역할을 다른 사용자에게 할당해야 합니다. 키 쌍 및 서명 작업의 실제 생성을 시작하거나 취소하려면 **승인** 또는 **거부를** 선택합니다. 새 키 쌍은 인증서 요청자가 다운로드할 때까지 Azure Key Vault에 안전하게 만들어지고 저장됩니다. 공개 키가 있는 결과 인증서는 CA에서 서명합니다. 이러한 작업을 완료하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 요청 세부 정보 보기의 스크린샷, 하단에 승인 메시지](media/howto-opc-vault-secure/view-key-pair.png "키 쌍 보기")

7. 생성된 개인 키(PFX 또는 PEM) 및 인증서(DER)는 바이너리 파일 다운로드로 선택된 형식으로 여기에서 다운로드할 수 있습니다. 예를 들어, 기본 64 인코딩 된 버전을 복사 하 고 명령줄 또는 텍스트 항목에 인증서를 붙여 넣기도 사용할 수 있습니다. 
8. 개인 키를 다운로드하여 안전하게 저장한 후 **개인 키 삭제를**선택할 수 있습니다. 공개 키가 있는 인증서는 나중에 사용할 수 있습니다.
9. CA 서명 인증서를 사용하기 때문에 CA 인증서 및 CRL(인증서 해지 목록)도 여기에서 다운로드해야 합니다.

이제 새 키 쌍을 적용하는 방법은 OPC UA 장치에 따라 달라집니다. 일반적으로 CA 인증서와 CRL은 `trusted` 폴더에 복사되고 응용 프로그램 인증서의 공개 및 개인 `own` 키는 인증서 저장소의 폴더에 적용됩니다. 일부 장치는 이미 인증서 업데이트에 대한 서버 푸시를 지원할 수 있습니다. OPC UA 장치의 설명서를 참조하십시오.

#### <a name="request-a-new-certificate-with-a-csr"></a>CSR을 통해 새 인증서 요청 

1. 응용 **프로그램으로 이동합니다.**
3. 나열된 응용 프로그램에 대한 **새 요청을** 선택합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. **서명 요청이 있는 새 인증서 요청을** 선택하여 응용 프로그램에 대해 새 서명된 인증서를 요청합니다.

   ![새 인증서 생성 스크린샷](media/howto-opc-vault-secure/generate-new-certificate.png "새 인증서 생성")

4. 로컬 파일을 선택하거나 양식에 base64 인코딩된 CSR을 붙여서 CSR을 업로드합니다. **새 인증서 생성을**선택합니다.

   ![인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/approve-reject-csr.png "CSR 승인")

5. 승인을 받으려면 승인자 역할이 있고 Azure Key Vault에서 서명 권한이 있는 사용자가 필요합니다. 실제 서명 작업을 시작하거나 취소하려면 **승인** 또는 **거부를** 선택합니다. 공개 키가 있는 결과 인증서는 CA에서 서명합니다. 이 작업을 완료하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 요청 세부 정보 보기의 스크린샷, 하단에 승인 메시지](media/howto-opc-vault-secure/view-cert-csr.png "인증서 보기")

6. 결과 인증서(DER)는 여기에서 이진 파일로 다운로드할 수 있습니다. 예를 들어, 기본 64 인코딩 된 버전을 복사 하 고 명령줄 또는 텍스트 항목에 인증서를 붙여 넣기도 사용할 수 있습니다. 
10. 인증서를 다운로드하여 안전하게 저장한 후 **인증서 삭제를**선택할 수 있습니다.
11. CA 서명 인증서를 사용하기 때문에 CA 인증서와 CRL도 여기에서 다운로드해야 합니다.

이제 새 인증서를 적용하는 방법에 따라 OPC UA 장치에 따라 다릅니다. 일반적으로 CA 인증서와 CRL은 `trusted` 폴더에 복사되고 응용 프로그램 인증서는 `own` 인증서 저장소의 폴더에 적용됩니다. 일부 장치는 이미 인증서 업데이트에 대한 서버 푸시를 지원할 수 있습니다. OPC UA 장치의 설명서를 참조하십시오.

### <a name="step-3-device-secured"></a>3단계: 장치 보안

이제 OPC UA 장치는 추가 구성 없이 CA 서명 된 인증서로 보안된 다른 OPC UA 장치와 통신할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

이제 OPC UA 장치를 보호하는 방법을 배웠으므로 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [보안 인증서 관리 서비스 실행](howto-opc-vault-secure-ca.md)
