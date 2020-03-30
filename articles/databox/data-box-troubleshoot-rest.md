---
title: REST 인터페이스 사용에 대한 Azure 데이터 박스 문제 해결 | 마이크로 소프트 문서
description: 데이터 복사본이 REST 인터페이스를 통해 있을 때 Azure 데이터 상자에 보이는 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297137"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure 데이터 상자 Blob 저장소와 관련된 문제 해결

이 문서에서는 데이터 상자의 REST 인터페이스를 통해 데이터 상자 Blob 저장소를 사용하여 데이터를 복사할 때 발생할 수 있는 문제를 해결하는 방법에 대한 정보를 자세히 설명합니다. 이러한 문제는 Azure 저장소 탐색기, AzCopy 또는 파이썬용 Azure 저장소 라이브러리와 같은 다른 응용 프로그램 또는 클라이언트 라이브러리와 함께 데이터 상자 Blob 저장소를 사용하는 경우 나타납니다.

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure 저장소 탐색기에서 볼 수 있는 오류

이 섹션에서는 데이터 상자 Blob 저장소와 함께 Azure 저장소 탐색기를 사용할 때 직면한 몇 가지 문제에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|**편집** 메뉴에서 대상 **Azure 스택 API를**선택합니다. <br>Azure 저장소 탐색기를 다시 시작합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |이 경로의 호스트 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 파일에 끝점 이름이 추가되어 있는지 확인합니다. <li>`C:\Windows\System32\drivers\etc\hosts`윈도우에서, 또는 </li><li> `/etc/hosts`리눅스에.</li>|
|자식 리소스를 검색할 수 없습니다. <br>세부 정보: 자체 서명된 인증서 |장치에 대한 TLS/SSL 인증서를 Azure 저장소 탐색기로 가져옵니다. <li>Azure 포털에서 인증서를 다운로드합니다. 자세한 내용은 인증서 [다운로드로](data-box-deploy-copy-data-via-rest.md#download-certificate)이동하십시오.</li><li>**편집** 메뉴에서 **SSL 인증서를** 선택한 다음 **인증서 가져오기를 선택합니다.**</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Windows용 AzCopy에서 볼 수 있는 오류

이 섹션에서는 데이터 상자 Blob 저장소를 사용하여 Windows용 AzCopy를 사용할 때 직면한 몇 가지 문제에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|AzCopy 명령은 이 오류를 표시하기 전에 잠시 중단된 것처럼 보입니다. <br>디렉터리 https:// 등록하지 못했습니다... 원격 이름을 확인할 수 없습니다.`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|다음 의 호스트 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 파일에 끝점 이름이 추가되어 있는지 `C:\Windows\System32\drivers\etc\hosts`확인합니다.|
|AzCopy 명령은 이 오류를 표시하기 전에 잠시 중단된 것처럼 보입니다. <br>소스 위치를 구문 분석하는 오류입니다. 기본 연결이 닫혔습니다: SSL/TLS 보안 채널에 대한 신뢰 관계를 설정할 수 없습니다.|장치의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 인증서 [다운로드로](data-box-deploy-copy-data-via-rest.md#download-certificate)이동하십시오.|


## <a name="errors-seen-in-azcopy-for-linux"></a>리눅스에 대한 AzCopy에서 본 오류

이 섹션에서는 데이터 상자 Blob 저장소를 사용하여 Linux용 AzCopy를 사용할 때 직면한 몇 가지 문제에 대해 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|AzCopy 명령은 이 오류를 표시하기 전에 20분 동안 중단된 것처럼 보입니다. <br>소스 위치를 구문 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`분석하는 오류 입니다. 해당 디바이스 또는 주소 없음|다음 의 호스트 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 파일에 끝점 이름이 추가되어 있는지 `/etc/hosts`확인합니다.|
|AzCopy 명령은 이 오류를 표시하기 전에 20분 동안 중단된 것처럼 보입니다. <br>소스 위치를 구문 분석하는 오류... SSL 연결을 설정하지 못했습니다.|장치의 TLS/SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 인증서 [다운로드로](data-box-deploy-copy-data-via-rest.md#download-certificate)이동하십시오.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>파이썬에 대한 Azure 저장소 라이브러리에 보이는 오류

이 섹션에서는 데이터 복사를 위해 Linux 클라이언트를 사용하는 경우 Data Box Disk를 배포하는 동안 발생하는 몇 가지 주요 문제를 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다. |파이썬에 대한 마이크로 소프트 Azure 스토리지 라이브러리의 설치된 버전은 데이터 박스에 의해 지원되지 않습니다. 지원되는 버전에 대한 Azure 데이터 상자 Blob 저장소 요구 사항을 참조하십시오.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Python을 실행하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 Base64 인코딩된 TLS 인증서 파일의 경로로 설정합니다(인증서 [다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)방법 참조). <br>예를 들어:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>또는 시스템의 인증서 저장소에 인증서를 추가한 다음 이 환경 변수를 해당 저장소의 경로로 설정합니다. <br> 예를 들어 Ubuntu에서  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>일반 오류

이러한 오류는 응용 프로그램에만 국한되지 않습니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|연결 시간이 시간 단축됩니다. |데이터 확인란 장치에 로그인하여 잠금이 해제된 지 확인합니다. 장치가 다시 시작될 때마다 누군가가 로그인할 때까지 잠긴 상태로 유지됩니다.|

## <a name="next-steps"></a>다음 단계

- [데이터 상자 Blob 저장소 시스템 요구 사항에](data-box-system-requirements-rest.md)대해 알아봅니다.
