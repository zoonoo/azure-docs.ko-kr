---
title: Azure Data Box/Azure Data Box Heavy 디바이스에서 본인의 인증서 사용하기
description: 본인의 인증서를 사용하여 Data Box 또는 Data Box Heavy 디바이스의 로컬 웹 UI 및 블로그 스토리지에 액세스하는 방법.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545176"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Data Box 및 Data Box Heavy 디바이스에서 본인의 인증서 사용하기

주문 처리 중에는 Data Box 또는 Data Box Heavy 디바이스의 로컬 웹 UI 및 Blob Storage에 액세스하기 위해 자체 서명된 인증서가 생성됩니다. 신뢰할 수 있는 채널을 통해 디바이스와 통신하는 경우 본인의 인증서를 사용할 수 있습니다.

이 문서에서는 본인의 인증서를 설치하는 방법과 데이터 센터에 디바이스를 반환하기 전에 기본 인증서로 되돌리는 방법을 설명합니다. 인증서 요구 사항에 대한 요약도 제공합니다.

## <a name="about-certificates"></a>인증서 정보

인증서는 **인증 기관** 과 같이 신뢰할 수 있는 타사로부터 **서명**(확인)된 엔터티(예: 도메인 이름)와 **퍼블릭 키** 간의 링크를 제공합니다.  인증서는 신뢰할 수 있는 퍼블릭 암호화 키를 배포하여 편리성을 도모합니다. 이와 같이 인증서는 통신의 신뢰 여부와 암호화된 정보가 올바른 서버에 전송되고 있는지를 확인합니다.

Data Box 디바이스를 처음 구성하면 자체 서명된 인증서가 자동으로 생성됩니다. 필요에 따라 본인의 인증서를 가져올 수 있습니다. 본인의 인증서를 가져오려는 경우 따라야 할 지침이 있습니다.

Data Box 또는 Data Box Heavy 디바이스에서는 다음 두 가지 유형의 엔드포인트 인증서를 사용합니다.

- Blob Storage 인증서
- 로컬 UI 인증서

### <a name="certificate-requirements"></a>인증서 요구 사항

인증서는 다음 요구 사항을 충족해야 합니다.

- 엔드포인트 인증서는 `.pfx` 내보낼 수 있는 프라이빗 키 형식이어야 합니다.
- 각 엔드포인트에 대하여 개별 인증서를, 여러 엔드포인트에 대하여 다중 도메인 인증서 또는 와일드카드 엔드포인트 인증서를 사용할 수 있습니다.
- 엔드포인트 인증서의 속성은 일반적인 SSL 인증서의 속성과 유사합니다.
- 클라이언트 머신에는 DER 형식(`.cer` 파일 이름 확장명)에 해당하는 인증서가 필요합니다.
- 로컬 UI 인증서를 업로드한 후 브라우저를 다시 시작하고 캐시를 지워야 합니다. 브라우저에 대한 구체적인 지침을 참조하세요.
- 디바이스 이름 또는 DNS 도메인 이름이 바뀌는 경우 인증서를 변경해야 합니다.
- 엔드포인트 인증서를 만들 때 다음 표를 사용하세요.

  |Type |주체 이름(SN)  |SAN(주체 대체 이름)  |주체 이름 예 |
  |---------|---------|---------|---------|
  |로컬 UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |다중 SAN 단일 인증서|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

자세한 내용은 [인증서 요구 사항](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md)을 참조하세요.

## <a name="add-certificates-to-device"></a>디바이스에 인증서 추가

로컬 웹 UI와 Blob Storage에 액세스하기 위해 자체 인증서를 사용할 수 있습니다.

> [!IMPORTANT]
> 디바이스 이름 또는 DNS 도메인을 변경하는 경우 새 인증서를 만들어야 합니다. 그런 다음 클라이언트 인증서와 디바이스 인증서를 새 디바이스 이름 및 DNS 도메인으로 업데이트해야 합니다.

디바이스에 본인의 인증서를 추가하려면 다음 단계를 수행하세요.

1. **인증서** **관리** > 로 이동합니다.

   **이름** 은 디바이스 이름을 표시합니다. **DNS 도메인** 은 DNS 서버의 도메인 이름을 표시합니다.

   화면 아래쪽에는 현재 사용 중인 인증서가 표시됩니다. 새 디바이스의 경우 주문 처리 중에 생성한 자체 서명된 인증서가 표시됩니다.

   ![Data Box 디바이스에 대한 인증서 페이지](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. **이름**(디바이스 이름) 또는 **DNS 도메인**(디바이스에 대한 DNS 서버의 도메인)을 변경해야 하는 경우, 인증서를 추가하기 전에 해당 작업을 수행하세요. 그런 다음, **적용** 을 선택합니다.

   디바이스 이름 또는 DNS 도메인 이름이 변경되면 인증서를 바꿔야 합니다.

   ![Data Box에 새로운 디바이스 이름 및 DNS 도메인 적용하기](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. 인증서를 추가하려면 **인증서 추가** 를 선택하여 **인증서 추가** 패널을 엽니다. 그런 다음 **인증서 유형**(**Blob Storage** 또는 **로컬 웹 UI**)을 선택합니다.

   ![Data Box 디바이스에 대한 인증서 페이지에서 인증서 패널 추가](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. 인증서 파일( `.pfx` 형식)을 선택하고 인증서를 내보낼 때 설정된 암호를 입력합니다. 그런 다음, **유효성 검사 및 추가** 를 선택합니다.

   ![Data Box에 Blob 엔드포인트 인증서를 추가하기 위한 설정](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   인증서가 성공적으로 추가되면 **인증서** 화면에 새 인증서의 지문이 표시됩니다. 인증서의 상태가 **유효** 입니다.

   ![유효한 새 인증서가 성공적으로 추가됨](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. 인증서 세부 정보를 보려면 인증서 이름을 선택하고 클릭합니다. 인증서는 1년 후에 만료됩니다.

   ![Data Box 디바이스에 대한 인증서 세부 정보 보기](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. 로컬 웹 UI에 대한 인증서를 변경한 경우 브라우저와 로컬 웹 UI를 다시 시작해야 합니다. 해당 단계는 SSL 캐시 문제를 방지하는 데 필요합니다.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. 로컬 웹 UI에 액세스하는 데 사용하는 클라이언트 머신에 새 인증서를 설치합니다. 자세한 내용은 아래의 [클라이언트로 인증서 가져오기](#import-certificates-to-client)를 참조하세요.


## <a name="import-certificates-to-client"></a>클라이언트에 인증서 가져오기

Data Box 디바이스에 인증서를 추가한 후에는 디바이스에 액세스하는 데 사용하는 클라이언트 머신으로 인증서를 가져와야 합니다. Local Machine의 Trusted Root Certificate Authority 저장소로 인증서를 가져옵니다.

Windows 클라이언트에서 인증서를 가져오려면 다음 단계를 수행합니다.

1. File Explorer에서 인증서 파일(`.cer` 형식)을 우클릭하고 **인증서 설치** 를 선택합니다. 그러면 인증서 가져오기 마법사가 시작됩니다.

    ![인증서 가져오기 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. **저장소 위치** 에 대해 **Local Machine** 을 선택하고 **다음** 을 클릭합니다.

    ![인증서 가져오기 마법사에서 Local Machine을 저장소 위치로 선택합니다.](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. **모든 인증서를 다음 저장소에 저장** 을 선택하고 **신뢰할 수 있는 루트 인증 기관** 을 선택한 후 **다음** 을 선택합니다.

   ![인증서 가져오기 마법사에서 신뢰할 수 있는 루트 인증 기관 저장소를 선택하기](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. 설정을 검토하고 **마침** 을 선택합니다. 가져오기가 완료되었음을 알리는 메시지가 표시됩니다.

   ![인증서 설정을 검토하고 인증서 가져오기 마법사를 완료합니다.](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>기본 인증서로 되돌리기

Azure 데이터 센터에 디바이스를 반환하기 전에 주문 처리 중에 생성된 원래 인증서로 되돌려야 합니다.

주문 처리 중에 생성된 인증서로 되돌리려면 다음 단계를 수행합니다.

1. **인증서** **관리** > 로 이동하여 **인증서 되돌리기** 를 선택합니다.

   인증서를 되돌리면 주문 처리 중에 생성한 자체 서명된 인증서를 사용하는 것으로 돌아갑니다. 디바이스에서 자체 인증서가 제거됩니다.

   ![Data Box 디바이스의 인증서 관리에서 인증서 되돌리기 옵션](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. 인증서 되돌리기가 성공적으로 끝나면 **종료 또는 다시 시작** 으로 이동해서 **다시 시작** 을 선택합니다. 해당 단계는 SSL 캐시 문제를 방지하는 데 필요합니다.

   ![Data Box 디바이스에서 인증서를 되돌린 후 로컬 웹 UI를 종료하거나 다시 시작합니다.](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   몇 분 정도 기다린 다음 로컬 웹 UI에 다시 로그인합니다.
