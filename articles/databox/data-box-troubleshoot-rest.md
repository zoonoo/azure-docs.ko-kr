---
title: REST 인터페이스 사용에 대한 Azure Data Box 문제 해결 | Microsoft Docs
description: REST 인터페이스를 통해 데이터를 복사할 때 Azure Data Box에 표시되는 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 17b8d6de198746a79a50c4fbda805b364212e3c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98796047"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure Data Box Blob Storage와 관련된 문제 해결

이 문서에서는 Data Box에서 REST 인터페이스를 통해 Data Box Blob Storage를 사용하여 데이터를 복사할 때 나타날 수 있는 문제를 해결하는 방법에 대한 정보를 자세히 설명합니다. 이러한 문제는 다른 애플리케이션 또는 Azure Storage Explorer, AzCopy 또는 Python용 Azure Storage 라이브러리와 같은 클라이언트 라이브러리와 함께 Data Box Blob Storage를 사용할 때 나타납니다.

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure Storage Explorer 에 표시되는 오류

이 섹션에서는 Data Box Blob Storage와 Azure Storage 탐색기를 사용할 때 발생하는 문제 중 일부에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|**편집** 메뉴에서 **대상 Azure Stack API** 를 선택합니다. <br>Azure Storage Explorer를 다시 시작합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 다음 경로의 호스트 파일에 추가되었는지 확인합니다. <li>Windows에서 `C:\Windows\System32\drivers\etc\hosts` 또는 </li><li> Linux의 경우 `/etc/hosts`입니다.</li>|
|자식 리소스를 검색할 수 없습니다. <br>세부 정보: 자체 서명된 인증서 |디바이스에 대한 TLS/SSL 인증서를 Azure Storage Explorer 로 가져옵니다. <li>Azure Portal에서 인증서를 다운로드합니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.</li><li>**편집** 메뉴에서 **SSL 인증서** 를 선택한 다음 **인증서 가져오기** 를 선택합니다.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Windows용 AzCopy에 표시되는 오류

이 섹션에서는 Data Box Blob Storage로 Windows용 AzCopy를 사용하는 경우 발생하는 문제 중 일부에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|이 오류가 표시되기 전에 AzCopy 명령이 1분 동안 응답을 중지하는 것으로 나타납니다. <br>디렉터리 열거 실패 https://... 원격 이름을 확인할 수 없습니다. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `C:\Windows\System32\drivers\etc\hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|이 오류가 표시되기 전에 AzCopy 명령이 1분 동안 응답을 중지하는 것으로 나타납니다. <br>원본 위치를 구문 분석하는 동안 오류가 발생했습니다. 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 트러스트 관계를 설정할 수 없습니다.|디바이스의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|


## <a name="errors-seen-in-azcopy-for-linux"></a>Linux용 AzCopy에 표시되는 오류

이 섹션에서는 Data Box Blob Storage를 사용하여 Linux용 AzCopy를 사용할 때 발생하는 문제 중 일부에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|이 오류를 표시하기 전에 AzCopy 명령이 20분 동안 응답하지 않는 것으로 나타납니다. <br>원본 위치 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` 구문 분석 중 오류가 발생했습니다. 해당 디바이스 또는 주소 없음|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `/etc/hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|이 오류를 표시하기 전에 AzCopy 명령이 20분 동안 응답하지 않는 것으로 나타납니다. <br>원본 위치를 구문 분석하는 중에 오류 발생… SSL 연결을 설정할 수 없습니다.|디바이스의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Python용 Azure Storage 라이브러리에 표시되는 오류

이 섹션에서는 데이터 복사를 위해 Linux 클라이언트를 사용하는 경우 Data Box Disk를 배포하는 동안 발생하는 몇 가지 주요 문제를 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다. |Data Box에서 Python용 Microsoft Azure Storage 라이브러리의 설치된 버전이 지원되지 않습니다. 지원되는 버전에 대한 Azure Data Box Blob Storage 요구 사항을 참조하세요.|
|… [SSL: 인증서_검증_실패]...|Python을 실행하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 Base64로 인코딩된 TLS 인증서 파일의 경로로 설정합니다([인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate) 방법 참조). <br>예를 들면 다음과 같습니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>또는 인증서를 시스템의 인증서 저장소에 추가한 다음 이 환경 변수를 해당 저장소의 경로로 설정합니다. <br> 예를 들어 Ubuntu에서  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>일반 오류

이러한 오류는 애플리케이션에 국한되지 않습니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|연결 시간 초과 |Data Box 디바이스에 로그인하여 잠금이 해제되었는지 확인합니다. 디바이스가 다시 시작될 때마다 사용자가 로그인할 때까지 잠긴 상태로 유지됩니다.|
|서버에서 요청을 인증하지 못했습니다. 오류가 발생하여 REST API 인증에 실패합니다. 서명을 비롯한 권한 부여 헤더 값이 올바르게 구성되어 있는지 확인합니다. ErrorCode:AuthenticationFailed. |이 문제가 발생하는 이유 중 하나는 디바이스 시간이 Azure의 디바이스와 동기화되지 않는 경우입니다. 시간이 많이 걸리는 경우 REST API를 통해 Data Box에 데이터를 복사하려고 하면 REST API 인증이 중단됩니다. 이 상황에서는 아웃바운드 UDP 123 포트를 열어 `time.windows.com`에 대한 액세스를 허용할 수 있습니다. 디바이스 시간이 Azure와 동기화되면 인증이 성공합니다. |

## <a name="next-steps"></a>다음 단계

- [Data Box Blob Storage 시스템 요구 사항](data-box-system-requirements-rest.md)에 대해 알아봅니다.
