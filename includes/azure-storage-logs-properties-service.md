---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557230"
---
| 속성 | Description |
|:--- |:---|
|**accountName** | 스토리지 계정 이름입니다. 예: `mystorageaccount`  |
|**requestUrl** | 요청된 URL입니다. |
|**userAgentHeader** | **User-Agent 헤더** 값으로, 따옴표로 묶여 있습니다. 예: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`|
|**referrerHeader** | **Referrer** 헤더 값입니다. 예: `http://contoso.com/about.html`|
|**clientRequestId** | 요청의 **x-ms-client-request-id** 헤더 값입니다. 예: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6` |
|**etag** | 반환된 개체의 ETag 식별자로, 따옴표로 묶여 있습니다. 예: `0x8D101F7E4B662C4`  |
|**serverLatencyMs** | 요청된 작업을 수행하는 데 걸린 총 시간으로 밀리초 단위입니다. 이 값은 네트워크 대기 시간(수신 요청을 읽는 시간과 요청자에게 응답을 보내는 시간)은 포함하지 않습니다. 예: `22` |
|**serviceType** | 이 요청과 연관된 서비스입니다. 예: `blob`, `table`, `files` 또는 `queue`. |
|**operationCount** | 요청에 관련된 각 로깅된 작업의 수입니다. 이 수는 인덱스 `0`에서 시작합니다. 일부 요청에는 두 개 이상의 작업이 필요 합니다. 대부분의 요청에서는 하나의 작업만 수행합니다. 예: `1` |
|**requestHeaderSize** | 요청 헤더의 크기로, 바이트 단위입니다. 예: `578` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다. |
|**requestBodySize** | 스토리지 서비스에서 읽은 요청 패킷의 크기로, 바이트 단위입니다. <br> 예: `0` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다.  |
|**responseHeaderSize** | 응답 헤더의 크기로, 바이트 단위입니다. 예: `216` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다.  |
|**responseBodySize** | 스토리지 서비스가 쓴 응답 패킷의 크기로, 바이트 단위입니다. 요청이 실패할 경우 이 값은 비어 있을 수 있습니다. 예: `216`  |
|**requestMd5** | 요청의 **Content-MD5** 헤더 또는 **x-ms-content-md5** 헤더의 값입니다. 이 필드에 지정된 MD5 해시 값은 요청의 콘텐츠를 나타냅니다. 예: `788815fd0198be0d275ad329cafd1830` <br>이 필드는 비어 있을 수 있습니다.  |
|**serverMd5** | 스토리지 서비스에 의해 계산된 MD5 해시 값입니다. 예: `3228b3cf1069a5489b298446321f8521` <br>이 필드는 비어 있을 수 있습니다.  |
|**lastModifiedTime** | 반환된 개체의 LMT(마지막 수정 시간)입니다.  예: `Tuesday, 09-Aug-11 21:13:26 GMT` <br>여러 개체를 반환할 수 있는 작업의 경우 이 필드가 비어 있습니다. |
|**conditionsUsed** | 조건을 나타내는 키-값 쌍의 세미콜론으로 구분된 목록입니다. 조건은 다음 중 하나일 수 있습니다. <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 예: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT` |
|**contentLengthHeader** | 스토리지 서비스로 전송된 요청의 Content-Length 헤더 값입니다. 요청이 성공할 경우 이 값은 requestBodySize와 같습니다. 요청에 실패할 경우 이 값은 requestBodySize와 다르거나 비어 있을 수 있습니다. |
|**tlsVersion** | 요청 연결에 사용되는 TLS 버전입니다. 예: `TLS 1.2` |
|**smbTreeConnectID** | 트리 연결 시간에 설정된 SMB(서버 메시지 블록) **treeConnectId** 입니다. 예: `0x3` |
|**smbPersistentHandleID** | 네트워크 다시 연결 시에도 존속되는 SMB2 CREATE 요청의 영구 핸들 ID입니다.  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1에서 **SMB2_FILEID.Persistent** 로 참조됩니다. 예: `0x6003f` |
|**smbVolatileHandleID** | 네트워크 다시 연결 시에 재활용되는 SMB2 CREATE 요청의 휘발성 핸들 ID입니다.  [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1에서 **SMB2_FILEID.Volatile** 로 참조됩니다. 예: `0xFFFFFFFF00000065` |
|**smbMessageID** | 연결에 상대적인 **MessageId** 입니다. 예: `0x3b165` |
|**smbCreditsConsumed** | 요청에서 사용하는 수신 또는 송신으로, 64k 단위입니다. 예: `0x3` |
|**smbCommandDetail** | 일반적인 요청 유형이 아닌 이 특정 요청에 대한 자세한 정보입니다. 예: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 파일 또는 디렉터리와 연결된 **FileId** 입니다.  NTFS FileId와 대략적으로 유사합니다. 예: `0x9223442405598953` |
|**smbSessionID** | 세션 설정 시점에 설정된 SMB2 **SessionId** 입니다. 예: `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command** 의 값입니다. 현재 이 값은 0과 18 사이의 숫자입니다(0과 18 포함). 예: `0x6` |
|**smbCommandMinor** | 해당하는 경우 **SmbCommandMajor** 의 서브클래스입니다. 예: `DirectoryCloseAndDelete` |