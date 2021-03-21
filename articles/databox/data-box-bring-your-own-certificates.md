---
title: Azure Data Box/Azure Data Box Heavy 장치에서 사용자 고유의 인증서 사용
description: 사용자 고유의 인증서를 사용 하 여 Data Box 또는 Data Box Heavy 장치의 로컬 웹 UI 및 블로그 저장소에 액세스 하는 방법입니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545176"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Data Box 및 Data Box Heavy 장치에서 사용자 고유의 인증서 사용

주문 처리 중에는 Data Box 또는 Data Box Heavy 장치의 로컬 웹 UI 및 Blob storage에 액세스 하기 위해 자체 서명 된 인증서가 생성 됩니다. 신뢰할 수 있는 채널을 통해 장치와 통신 하는 경우 사용자 고유의 인증서를 사용할 수 있습니다.

이 문서에서는 사용자 고유의 인증서를 설치 하는 방법 및 데이터 센터에 장치를 반환 하기 전에 기본 인증서로 되돌리는 방법을 설명 합니다. 인증서 요구 사항에 대 한 요약도 제공 합니다.

## <a name="about-certificates"></a>인증서 정보

인증서는 **인증 기관과** 같이 신뢰할 수 있는 타사에 의해 **서명** (확인) 된 엔터티 (예: 도메인 이름)와 **공개 키** 간의 링크를 제공 합니다.  인증서는 신뢰할 수 있는 공용 암호화 키를 배포 하는 편리한 방법을 제공 합니다. 이러한 방식으로 인증서는 통신을 신뢰할 수 있고 암호화 된 정보를 올바른 서버에 전송 하 고 있는지 확인 합니다.

Data Box 장치를 처음 구성 하면 자체 서명 된 인증서가 자동으로 생성 됩니다. 필요에 따라 사용자 고유의 인증서를 가져올 수 있습니다. 사용자 고유의 인증서를 가져올 계획인 경우 따라야 하는 지침이 있습니다.

Data Box 또는 Data Box Heavy 장치에서는 두 가지 유형의 끝점 인증서를 사용 합니다.

- Blob storage 인증서
- 로컬 UI 인증서

### <a name="certificate-requirements"></a>인증서 요구 사항

인증서는 다음 요구 사항을 충족 해야 합니다.

- 끝점 인증서는 `.pfx` 내보낼 수 있는 개인 키를 사용 하 여 형식 이어야 합니다.
- 각 끝점에 개별 인증서, 여러 끝점에 대 한 다중 도메인 인증서 또는 와일드 카드 끝점 인증서를 사용할 수 있습니다.
- 끝점 인증서의 속성은 일반적인 SSL 인증서의 속성과 유사 합니다.
- 클라이언트 컴퓨터에는 DER 형식 ( `.cer` 파일 이름 확장명)의 해당 인증서가 필요 합니다.
- 로컬 UI 인증서를 업로드 한 후 브라우저를 다시 시작 하 고 캐시를 지워야 합니다. 브라우저에 대 한 특정 지침을 참조 하세요.
- 장치 이름 또는 DNS 도메인 이름이 변경 되 면 인증서를 변경 해야 합니다.
- 끝점 인증서를 만들 때 다음 표를 사용 합니다.

  |Type |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
  |---------|---------|---------|---------|
  |로컬 UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob 스토리지|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |다중 SAN 단일 인증서|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

자세한 내용은 [인증서 요구 사항](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md)을 참조 하세요.

## <a name="add-certificates-to-device"></a>장치에 인증서 추가

로컬 웹 UI에 액세스 하 고 Blob storage에 액세스 하기 위해 자체 인증서를 사용할 수 있습니다.

> [!IMPORTANT]
> 장치 이름 또는 DNS 도메인을 변경 하는 경우 새 인증서를 만들어야 합니다. 그런 다음 클라이언트 인증서와 장치 인증서를 새 장치 이름 및 DNS 도메인으로 업데이트 해야 합니다.

장치에 사용자 고유의 인증서를 추가 하려면 다음 단계를 수행 합니다.

1. 인증서 **관리** 로 이동  >  합니다.

   **이름** 장치 이름을 표시 합니다. Dns **도메인** dns 서버의 도메인 이름을 표시 합니다.

   화면 아래쪽에는 현재 사용 중인 인증서가 표시 됩니다. 새 장치의 경우 주문 처리 중에 생성 된 자체 서명 된 인증서가 표시 됩니다.

   ![Data Box 장치에 대 한 인증서 페이지](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. **이름** (장치 이름) 또는 **dns 도메인** (장치에 대 한 dns 서버의 도메인)을 변경 해야 하는 경우 인증서를 추가 하기 전에이 작업을 수행 합니다. 그런 다음, **적용** 을 선택합니다.

   장치 이름 또는 DNS 도메인 이름이 변경 되 면 인증서를 변경 해야 합니다.

   ![Data Box에 대 한 새 장치 이름 및 DNS 도메인을 적용 합니다.](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. 인증서를 추가 하려면 인증서 **추가** 를 선택 하 여 **인증서 패널 추가** 를 엽니다. 그런 다음 **인증서 유형** ( **Blob storage** 또는 **로컬 웹 UI**)을 선택 합니다.

   ![Data Box 장치에 대 한 인증서 페이지에서 인증서 패널 추가](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. 인증서 파일 ( `.pfx` 형식)을 선택 하 고 인증서를 내보낼 때 설정 된 암호를 입력 합니다. 그런 다음 **유효성 검사 & 추가** 를 선택 합니다.

   ![Data Box에 Blob 끝점 인증서를 추가 하기 위한 설정](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   인증서가 성공적으로 추가 되 면 **인증서** 화면에 새 인증서의 지문이 표시 됩니다. 인증서의 상태가 **유효함** 입니다.

   ![성공적으로 추가 된 유효한 새 인증서](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. 인증서 세부 정보를 보려면 인증서 이름을 선택 하 고 클릭 합니다. 인증서는 1 년 후에 만료 됩니다.

   ![Data Box 장치에 대 한 인증서 세부 정보 보기](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. 로컬 웹 UI에 대 한 인증서를 변경한 경우 브라우저를 다시 시작한 다음 로컬 웹 UI를 다시 시작 해야 합니다. 이 단계는 SSL 캐시 문제를 방지 하는 데 필요 합니다.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. 로컬 웹 UI에 액세스 하는 데 사용 중인 클라이언트 컴퓨터에 새 인증서를 설치 합니다. 자세한 내용은 아래의 [클라이언트로 인증서 가져오기](#import-certificates-to-client)를 참조 하세요.


## <a name="import-certificates-to-client"></a>클라이언트에 인증서 가져오기

Data Box 장치에 인증서를 추가한 후에는 장치에 액세스 하는 데 사용 하는 클라이언트 컴퓨터로 인증서를 가져와야 합니다. 로컬 컴퓨터의 신뢰할 수 있는 루트 인증 기관 저장소로 인증서를 가져옵니다.

Windows 클라이언트에서 인증서를 가져오려면 다음 단계를 수행 합니다.

1. 파일 탐색기에서 인증서 파일 (형식)을 마우스 오른쪽 단추로 클릭 `.cer` 하 고 **인증서 설치** 를 선택 합니다. 그러면 인증서 가져오기 마법사가 시작됩니다.

    ![인증서 가져오기 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. **저장소 위치** 에 대해 **로컬 컴퓨터** 를 선택 하 고 **다음** 을 선택 합니다.

    ![인증서 가져오기 마법사에서 로컬 컴퓨터를 저장소 위치로 선택 합니다.](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. **모든 인증서를 다음 저장소에** 저장을 선택 하 고 **신뢰할 수 있는 루트 인증 기관** 을 선택한 후 **다음** 을 선택 합니다.

   ![인증서 가져오기 마법사에서 신뢰할 수 있는 루트 인증 기관 저장소를 선택 합니다.](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. 설정을 검토 하 고 **마침** 을 선택 합니다. 가져오기가 완료 되었음을 알리는 메시지가 표시 됩니다.

   ![인증서 설정을 검토 하 고 인증서 가져오기 마법사를 완료 합니다.](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>기본 인증서로 되돌리기

Azure 데이터 센터에 장치를 반환 하기 전에 주문 처리 중에 생성 된 원래 인증서로 되돌려야 합니다.

주문 처리 중에 생성 된 인증서로 되돌리려면 다음 단계를 수행 합니다.

1. 인증서 **관리** 로 이동 하  >  고 **인증서 되돌리기** 를 선택 합니다.

   인증서를 되돌리면 주문 처리 중에 생성 된 자체 서명 된 인증서를 사용 하 여로 돌아갑니다. 장치에서 자체 인증서가 제거 됩니다.

   ![Data Box 장치의 인증서 관리에서 인증서 옵션 되돌리기](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Certificate 변경할지가 성공적으로 완료 되 면 **종료 또는 다시 시작** 으로 이동 하 고 **다시 시작** 을 선택 합니다. 이 단계는 SSL 캐시 문제를 방지 하는 데 필요 합니다.

   ![Data Box 장치에서 인증서를 되돌린 후 로컬 웹 UI를 종료 하거나 다시 시작 합니다.](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   몇 분 정도 기다린 다음 로컬 웹 UI에 다시 로그인 합니다.
