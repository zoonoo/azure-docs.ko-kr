---
title: Opc 자격 증명 모음을 사용 하 여 OPC UA 장치의 통신 보호-Azure | Microsoft Docs
description: Opc UA 응용 프로그램을 등록 하는 방법 및 OPC 자격 증명 모음을 사용 하 여 OPC UA 장치에 서명 된 응용 프로그램 인증서를 발급 하는 방법
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b5c886625c944e2f5501859e78506ca89ec3d765
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203681"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>OPC 자격 증명 모음 인증서 관리 서비스 사용

이 문서에서는 응용 프로그램을 등록 하는 방법과 OPC UA 장치에 대해 서명 된 응용 프로그램 인증서를 발급 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="deploy-the-certificate-management-service"></a>인증서 관리 서비스 배포

먼저 Azure 클라우드에 서비스를 배포 합니다. 자세한 내용은 [OPC 자격 증명 모음 인증서 관리 서비스 배포](howto-opc-vault-deploy.md)를 참조 하세요.

### <a name="create-the-issuer-ca-certificate"></a>발급자 CA 인증서 만들기

아직 수행 하지 않은 경우 발급자 CA 인증서를 만듭니다. 자세한 내용은 [OPC 자격 증명 모음에 대 한 발급자 인증서 만들기 및 관리](howto-opc-vault-manage.md)를 참조 하세요.

## <a name="secure-opc-ua-applications"></a>OPC UA 응용 프로그램 보안

### <a name="step-1-register-your-opc-ua-application"></a>1단계: OPC UA 응용 프로그램 등록 

> [!IMPORTANT]
> 응용 프로그램을 등록 하려면 기록기 역할이 필요 합니다.

1. 에서 인증서 서비스 `https://myResourceGroup-app.azurewebsites.net`를 열고 로그인 합니다.
2. **등록 새로 만들기**로 이동 합니다. 응용 프로그램 등록의 경우 사용자에 게 적어도 작성자 역할을 할당 해야 합니다.
2. 항목 양식은 OPC UA의 명명 규칙을 따릅니다. 예를 들어 다음 스크린샷에서 OPC UA .NET Standard 스택의 [OPC Ua 참조 서버](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) 샘플에 대 한 설정이 표시 됩니다.

   ![UA 참조 서버 등록의 스크린샷](media/howto-opc-vault-secure/reference-server-registration.png "UA 참조 서버 등록")

5. **등록** 을 선택 하 여 인증서 서비스 응용 프로그램 데이터베이스에 응용 프로그램을 등록 합니다. 워크플로는 사용자에 게 응용 프로그램에 대해 서명 된 인증서를 요청 하는 다음 단계를 직접 안내 합니다.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2단계: CA 서명 된 응용 프로그램 인증서를 사용 하 여 응용 프로그램 보호

CSR (인증서 서명 요청)을 기반으로 서명 된 인증서를 발급 하 여 OPC UA 응용 프로그램의 보안을 유지 합니다. 또는 새 개인 키를 PFX 또는 PEM 형식으로 포함 하는 새 키 쌍을 요청할 수 있습니다. 응용 프로그램에 대해 지원 되는 방법에 대 한 자세한 내용은 OPC UA 장치의 설명서를 참조 하세요. 일반적으로 CSR 방법은 네트워크를 통해 개인 키를 전송할 필요가 없기 때문에 권장 됩니다.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>새 키 쌍을 사용 하 여 새 인증서 요청

1. **응용 프로그램**으로 이동 합니다.
3. 나열 된 응용 프로그램에 대 한 **새 요청** 을 선택 합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 응용 프로그램에 대 한 공개 키를 사용 하 여 개인 키와 새 서명 된 인증서를 요청 하려면 **새 키 쌍 및 인증서 요청** 을 선택 합니다.

   ![새 키 쌍 및 인증서 생성의 스크린샷](media/howto-opc-vault-secure/generate-new-key-pair.png "새 키 쌍 생성")

4. 주체와 도메인 이름을 사용 하 여 양식을 작성 합니다. 개인 키에 대해 PEM 또는 PFX (암호 포함)를 선택 합니다. **새 키 쌍 생성** 을 선택 하 여 인증서 요청을 만듭니다.

   ![인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/approve-reject.png "인증서 승인")

5. 승인에는 승인자 역할을 가진 사용자와 Azure Key Vault에 대 한 서명 권한이 필요 합니다. 일반적인 워크플로에서 승인자 및 요청자 역할을 다른 사용자에 게 할당 해야 합니다. **승인** 또는 **거부** 를 선택 하 여 키 쌍의 실제 생성과 서명 작업을 시작 하거나 취소 합니다. 새 키 쌍은 인증서 요청자에 의해 다운로드 될 때까지 Azure Key Vault에 안전 하 게 생성 되 고 저장 됩니다. 공개 키가 포함 된 결과 인증서는 CA에서 서명 됩니다. 이러한 작업을 완료 하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 요청 세부 정보 보기의 스크린샷, 아래쪽에 승인 메시지 포함](media/howto-opc-vault-secure/view-key-pair.png "키 쌍 보기")

7. 결과 개인 키 (PFX 또는 PEM) 및 인증서 (DER)는 여기에서 이진 파일 다운로드로 선택한 형식으로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서를 복사 하 여 붙여 넣는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
8. 개인 키를 다운로드 하 여 안전 하 게 저장 한 후 **개인 키 삭제**를 선택할 수 있습니다. 공개 키가 있는 인증서는 나중에 사용할 수 있는 상태로 남아 있습니다.
9. CA 서명 인증서를 사용 하기 때문에 CA 인증서 및 CRL (인증서 해지 목록)도 여기에서 다운로드 해야 합니다.

이제 OPC UA 장치에 따라 새 키 쌍을 적용 하는 방법을 결정 합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사 되 고 응용 프로그램 인증서의 공개 키와 개인 키는 인증서 저장소의 `own` 폴더에 적용 됩니다. 일부 장치는 인증서 업데이트를 위해 서버 푸시를 이미 지원할 수 있습니다. OPC UA 장치의 설명서를 참조 하세요.

#### <a name="request-a-new-certificate-with-a-csr"></a>CSR을 사용 하 여 새 인증서 요청 

1. **응용 프로그램**으로 이동 합니다.
3. 나열 된 응용 프로그램에 대 한 **새 요청** 을 선택 합니다.

   ![새 인증서 요청 스크린샷](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 응용 프로그램에 대 한 새 서명 된 인증서를 요청 하려면 **서명 요청으로 새 인증서 요청** 을 선택 합니다.

   ![새 인증서 생성의 스크린샷](media/howto-opc-vault-secure/generate-new-certificate.png "새 인증서 생성")

4. 로컬 파일을 선택 하거나 형식으로 base64 인코딩된 CSR을 붙여넣어 CSR을 업로드 합니다. **새 인증서 생성**을 선택 합니다.

   ![인증서 요청 세부 정보 보기의 스크린샷](media/howto-opc-vault-secure/approve-reject-csr.png "CSR 승인")

5. 승인에는 승인자 역할을 가진 사용자와 Azure Key Vault에 대 한 서명 권한이 필요 합니다. **승인** 또는 **거부** 를 선택 하 여 실제 서명 작업을 시작 하거나 취소 합니다. 공개 키가 포함 된 결과 인증서는 CA에서 서명 됩니다. 이 작업을 완료 하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 요청 세부 정보 보기의 스크린샷, 아래쪽에 승인 메시지 포함](media/howto-opc-vault-secure/view-cert-csr.png "인증서 보기")

6. 결과 인증서 (DER)는 여기에서 이진 파일로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서를 복사 하 여 붙여 넣는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
10. 인증서를 다운로드 하 여 안전 하 게 저장 한 후 **인증서 삭제**를 선택할 수 있습니다.
11. CA 서명 인증서를 사용 하기 때문에 CA 인증서 및 CRL도 여기에서 다운로드 해야 합니다.

이제 OPC UA 장치에 따라 새 인증서를 적용 하는 방법을 결정 합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사 되 고 응용 프로그램 인증서는 인증서 저장소의 `own` 폴더에 적용 됩니다. 일부 장치는 인증서 업데이트를 위해 서버 푸시를 이미 지원할 수 있습니다. OPC UA 장치의 설명서를 참조 하세요.

### <a name="step-4-device-secured"></a>4단계: 장치 보안

이제 OPC UA 장치는 추가 구성 없이 CA 서명 된 인증서로 보호 되는 다른 OPC UA 장치와 통신할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

OPC UA 장치를 보호 하는 방법을 배웠으므로 이제 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [보안 인증서 관리 서비스 실행](howto-opc-vault-secure-ca.md)