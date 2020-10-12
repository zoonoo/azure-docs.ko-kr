---
title: REST 인터페이스 사용에 대 한 Azure Data Box 문제 해결 | Microsoft Docs
description: REST 인터페이스를 통해 데이터를 복사할 때 Azure Data Box에 표시 되는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b950f80ba8c2bdbaf7a515dc1ce127b934723177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85558563"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure Data Box Blob storage와 관련 된 문제 해결

이 문서에서는 Data Box에서 REST 인터페이스를 통해 Data Box Blob storage를 사용 하 여 데이터를 복사할 때 나타날 수 있는 문제를 해결 하는 방법에 대 한 정보를 자세히 설명 합니다. 이러한 문제는 Data Box Blob storage를 다른 응용 프로그램 또는 클라이언트 라이브러리 (예: Python 용 Azure Storage 탐색기, AzCopy 또는 Azure Storage 라이브러리)와 함께 사용 하는 경우에 발생 합니다.

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure Storage 탐색기에 표시 되는 오류

이 섹션에서는 Data Box Blob Storage와 Azure Storage 탐색기를 사용할 때 직면 하는 문제 중 일부에 대해 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|**편집** 메뉴에서 **대상 Azure Stack api**를 선택 합니다. <br>Azure Storage 탐색기를 다시 시작 합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |끝점 이름이 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 다음 경로에 있는 호스트 파일에 추가 되었는지 확인 합니다. <li>`C:\Windows\System32\drivers\etc\hosts` Windows 또는 </li><li> `/etc/hosts` Linux에서</li>|
|자식 리소스를 검색할 수 없습니다. <br>세부 정보: 자체 서명 된 인증서 |장치에 대 한 TLS/SSL 인증서를 Azure Storage 탐색기으로 가져옵니다. <li>Azure Portal에서 인증서를 다운로드 합니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)로 이동 하세요.</li><li>**편집** 메뉴에서 **SSL 인증서** 를 선택한 다음 **인증서 가져오기**를 선택 합니다.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>AzCopy for Windows에 표시 되는 오류

이 섹션에서는 Data Box Blob storage로 Windows 용 AzCopy을 사용 하는 경우 발생 하는 문제 중 일부에 대해 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|이 오류를 표시 하기 전에 AzCopy 명령이 1 분 동안 응답을 중지 하는 것으로 나타납니다. <br>Https://디렉터리를 열거 하지 못했습니다. 원격 이름을 확인할 수 없습니다. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|끝점 이름이의 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 호스트 파일에 추가 되었는지 확인 `C:\Windows\System32\drivers\etc\hosts` 합니다.|
|이 오류를 표시 하기 전에 AzCopy 명령이 1 분 동안 응답을 중지 하는 것으로 나타납니다. <br>소스 위치를 구문 분석 하는 동안 오류가 발생 했습니다. 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다.|장치의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)로 이동 하세요.|


## <a name="errors-seen-in-azcopy-for-linux"></a>Linux 용 AzCopy에 표시 되는 오류

이 섹션에서는 Data Box Blob storage를 사용 하 여 Linux 용 AzCopy을 사용할 때 발생 하는 문제 중 일부에 대해 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 응답 하지 않는 것으로 나타납니다. <br>소스 위치를 구문 분석 하는 동안 오류가 발생 했습니다 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . 해당 디바이스 또는 주소 없음|끝점 이름이의 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 호스트 파일에 추가 되었는지 확인 `/etc/hosts` 합니다.|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 응답 하지 않는 것으로 나타납니다. <br>소스 위치를 구문 분석 하는 동안 오류가 발생 했습니다. SSL 연결을 설정할 수 없습니다.|장치의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)로 이동 하세요.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Python 용 Azure Storage 라이브러리에 표시 되는 오류

이 섹션에서는 데이터 복사를 위해 Linux 클라이언트를 사용하는 경우 Data Box Disk를 배포하는 동안 발생하는 몇 가지 주요 문제를 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다. |Data Box에서 Python 용 Microsoft Azure Storage 라이브러리의 설치 된 버전이 지원 되지 않습니다. 지원 되는 버전에 대 한 Azure Data Box Blob 저장소 요구 사항을 참조 하세요.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Python을 실행 하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 b a s e 64로 인코딩된 TLS 인증서 파일의 경로로 설정 합니다. [인증서를 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)하는 방법을 참조 하세요. <br>예를 들면 다음과 같습니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>또는 시스템의 인증서 저장소에 인증서를 추가한 다음이 환경 변수를 해당 저장소의 경로로 설정 합니다. <br> 예를 들어 Ubuntu에서  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>일반 오류

이러한 오류는 응용 프로그램에 국한 되지 않습니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|연결 시간이 초과 되었습니다. |Data Box 장치에 로그인 하 여 잠금이 해제 되었는지 확인 합니다. 장치가 다시 시작 될 때마다 사용자가 로그인 할 때까지 잠긴 상태로 유지 됩니다.|

## <a name="next-steps"></a>다음 단계

- [Data Box Blob storage 시스템 요구 사항](data-box-system-requirements-rest.md)에 대해 알아봅니다.
