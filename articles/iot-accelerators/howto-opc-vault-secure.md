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
ms.openlocfilehash: a819e0b5a0da92137451eedbf84fe06d22879568
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973780"
---
# <a name="how-to-use-the-opc-ua-certificate-management-service"></a>OPC UA 인증서 관리 서비스를 사용 하는 방법

이 문서에서는 응용 프로그램을 등록 하는 방법과 OPC UA 장치에 대해 서명 된 응용 프로그램 인증서를 발급 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="deploy-the-certificate-management-service"></a>인증서 관리 서비스 배포

먼저 Azure 클라우드에 서비스를 배포 해야 합니다.
[OPC 자격 증명 모음 인증서 관리 서비스를 배포](howto-opc-vault-deploy.md)하는 방법을 설명 하는 문서를 찾아 보세요.

### <a name="create-the-issuer-ca-certificate"></a>발급자 CA 인증서 만들기

아직 수행 하지 않은 경우 발급자 CA 인증서를 만듭니다.

[OPC 자격 증명 모음에 대 한 발급자 인증서를 만들고 관리](howto-opc-vault-manage.md)하는 방법을 설명 하는 문서를 찾습니다.

## <a name="secure-opc-ua-applications"></a>OPC UA 응용 프로그램 보안

### <a name="step-1-register-your-opc-ua-application"></a>1단계: OPC UA 응용 프로그램 등록 

> [!IMPORTANT]
> 응용 프로그램을 등록 하려면 ' Writer ' 역할이 필요 합니다.

1. 에서 `https://myResourceGroup-app.azurewebsites.net` 인증서 서비스를 열고 로그인 합니다.
2. `Register New` 페이지로 이동합니다.
1. 응용 프로그램 등록의 경우 사용자에 게 적어도 ' 작성자 ' 역할이 할당 되어야 합니다.
2. 항목 양식은 OPC UA 세계의 명명 규칙을 따릅니다. 예를 들어 OPC UA의 [OPC Ua 참조 서버](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) 샘플에 대 한 설정 아래 그림에서 NetStandard stack이 표시 됩니다.

   ![UA 참조 서버 등록](media/howto-opc-vault-secure/reference-server-registration.png "UA 참조 서버 등록")

5. 인증서 서비스 응용 프로그램 데이터베이스에 응용 프로그램을 등록 하려면 단추를누릅니다.`Register` 워크플로는 사용자에 게 응용 프로그램에 대해 서명 된 인증서를 요청 하는 다음 단계를 직접 안내 합니다.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2단계: CA 서명 된 응용 프로그램 인증서를 사용 하 여 응용 프로그램 보호

OPC UA 응용 프로그램은 CSR (인증서 서명 요청)을 기반으로 서명 된 인증서를 발급 하거나 새 키 쌍을 요청 하 여 보안을 설정할 수 있습니다. 여기에는 PFX 또는 PEM 형식의 새 개인 키도 포함 됩니다. 응용 프로그램에 대해 지원 되는 방법에 대 한 OPC UA 장치의 설명서를 따릅니다. 일반적으로 CSR 방법은 네트워크를 통해 개인 키를 전송할 필요가 없기 때문에 권장 됩니다.

- 장치에서 새 키 쌍을 발급 해야 하는 경우 [Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair)를 따르세요.
- 장치가 CSR (인증서 서명 요청)을 발급 하도록 지 원하는 경우 [Step3b](#step-3b-request-a-new-certificate-with-a-csr)를 따르세요.

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>3a단계: 새 키 쌍을 사용 하 여 새 인증서 요청

1. `Applications` 페이지로 이동합니다.
3. 나열 된 응용 프로그램에 대해를 `New Request` 선택 하 여 인증서 요청 워크플로를 시작 합니다.

   ![새 인증서 요청](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 응용 프로그램에 대 한 공개 키를 사용 하 여 개인 키와 새 서명 된 인증서를 요청 하려면 ' 새 키 쌍 및 인증서 요청 '을 누릅니다.

   ![새 키 쌍 생성](media/howto-opc-vault-secure/generate-new-key-pair.png "새 키 쌍 생성")

4. 양식에 주체, 도메인 이름을 입력 하 고 개인 키에 대 한 암호를 사용 하 여 PEM 또는 PFX를 선택 합니다. 인증서 요청을 만들려면 단추를누릅니다.`Generate New Certificate`

   ![인증서 승인](media/howto-opc-vault-secure/approve-reject.png "인증서 승인")

5. 승인 단계에는 ' 승인자 ' 역할의 사용자와 Azure Key Vault에 대 한 서명 권한이 필요 합니다. 일반적인 워크플로에서 승인자와 요청자 역할은 다른 사용자에 게 할당 되어야 합니다.
6. 인증서 요청을 승인 하거나 거부 하 여 키 쌍의 실제 생성과 서명 작업을 시작 하거나 취소 합니다. 새 키 쌍이 생성 되 고 인증서 요청자에 의해 다운로드 될 때까지 Azure Key Vault 안전 하 게 저장 됩니다. 공개 키가 포함 된 결과 인증서는 CA에서 서명 됩니다. 이러한 작업을 완료 하는 데 몇 초 정도 걸릴 수 있습니다.

   ![키 쌍 보기](media/howto-opc-vault-secure/view-key-pair.png "키 쌍 보기")

7. 결과 개인 키 (PFX 또는 PEM) 및 인증서 (DER)는 여기에서 이진 파일 다운로드로 선택한 형식으로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서 붙여넣기를 복사 하는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
8. 개인 키를 다운로드 하 여 안전 하 게 저장 한 후에는 `Delete Private Key` 단추를 사용 하 여 서비스에서 삭제할 수 있습니다. 공개 키가 있는 인증서는 나중에 사용할 수 있는 상태로 남아 있습니다.
9. CA 서명 인증서를 사용 하기 때문에 CA 인증서 및 CRL도 여기에서 다운로드 해야 합니다.
10. 이제 OPC UA 장치에 따라 새 키 쌍을 적용 하는 방법을 결정 합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사 되 고 응용 프로그램 인증서의 공개 키와 개인 키는 인증서 저장소의 `own` 폴더에 적용 됩니다. 일부 장치는 인증서 업데이트를 위해 이미 ' 서버 푸시 '를 지원할 수 있습니다. OPC UA 장치의 설명서를 참조 하세요.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>3b 단계: CSR을 사용 하 여 새 인증서 요청 

1. `Applications` 페이지로 이동합니다.
3. 나열 된 응용 프로그램에 대해를 `New Request` 선택 하 여 인증서 요청 워크플로를 시작 합니다.

   ![새 인증서 요청](media/howto-opc-vault-secure/request-new-certificate.png "새 인증서 요청")

3. 응용 프로그램에 대 한 새 서명 된 인증서를 요청 하려면 ' 서명 요청으로 새 인증서 요청 '을 누릅니다.

   ![새 인증서 생성](media/howto-opc-vault-secure/generate-new-certificate.png "새 인증서 생성")

4. 로컬 파일을 선택 하거나 형식으로 base64 인코딩된 CSR을 붙여넣어 CSR을 업로드 합니다. 인증서 요청을 만들려면 단추를누릅니다.`Generate New Certificate`

   ![CSR 승인](media/howto-opc-vault-secure/approve-reject-csr.png "CSR 승인")

5. 승인 단계에는 ' 승인자 ' 역할의 사용자와 Azure Key Vault에 대 한 서명 권한이 필요 합니다. 인증서 요청을 승인 하거나 거부 하 여 실제 서명 작업을 시작 하거나 취소 합니다. 공개 키가 포함 된 결과 인증서는 CA에서 서명 됩니다. 이 작업을 완료 하는 데 몇 초 정도 걸릴 수 있습니다.

   ![인증서 보기](media/howto-opc-vault-secure/view-cert-csr.png "인증서 보기")

6. 결과 인증서 (DER)는 여기에서 이진 파일로 다운로드할 수 있습니다. 예를 들어, 명령줄 또는 텍스트 항목에 인증서 붙여넣기를 복사 하는 경우에도 base64 인코딩 버전을 사용할 수 있습니다. 
10. 인증서를 다운로드 하 고 안전 하 게 저장 한 후에는 `Delete Certificate` 단추를 사용 하 여 서비스에서 인증서를 삭제할 수 있습니다.
11. CA 서명 인증서를 사용 하기 때문에 CA 인증서 및 CRL도 여기에서 다운로드 해야 합니다.
12. 이제 OPC UA 장치에 따라 새 인증서를 적용 하는 방법을 결정 합니다. 일반적으로 CA 인증서 및 CRL은 `trusted` 폴더에 복사 되 고 응용 프로그램 인증서는 인증서 저장소의 `own` 폴더에 적용 됩니다. 일부 장치는 인증서 업데이트를 위해 이미 ' 서버 푸시 '를 지원할 수 있습니다. OPC UA 장치의 설명서를 참조 하세요.

### <a name="step-4-device-secured"></a>4단계: 장치 보안

이제 OPC UA 장치는 추가 구성 없이 CA 서명 된 인증서로 보호 되는 다른 OPC UA 장치와 통신할 준비가 되었습니다. 마음껏 즐기세요!


## <a name="next-steps"></a>다음 단계

이제 OPC UA 장치를 보호 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [보안 인증서 관리 서비스 실행](howto-opc-vault-secure-ca.md)