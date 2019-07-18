---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182421"
---
저장소 계정에서 리소스에 대한 액세스를 허용하는 공유 액세스 서명(SAS) URL을 보유하는 경우 연결 문자열에 SAS를 사용할 수 있습니다. SAS가 요청을 인증하는 데 필요한 정보를 포함하므로 SAS로 시작되는 연결 문자열은 프로토콜, 서비스 엔드포인트 및 필요한 자격 증명을 제공하여 리소스에 액세스합니다.

공유 액세스 서명을 포함하는 연결 문자열을 만들려면 다음과 같은 형식의 문자열을 지정합니다.

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

각 서비스 엔드포인트는 선택 사항이지만 연결 문자열에는 최소한 하나 이상이 포함되어야 합니다.

> [!NOTE]
> SAS와 함께 HTTPS를 사용하는 것이 가장 좋습니다.
>
> 구성 파일에서 연결 문자열의 SAS를 지정하는 경우 URL의 특수 문자 인코딩이 필요할 수 있습니다.
>
>

### <a name="service-sas-example"></a>서비스 SAS 예
다음은 Blob Storage에 대한 서비스 SAS를 포함하는 연결 문자열의 예제입니다.

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

그리고 다음은 특수 문자의 인코딩을 사용하는 동일한 연결 문자열의 예제입니다.

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>계정 SAS 예
다음은 Blob 및 File Storage에 대한 계정 SAS를 포함하는 연결 문자열의 예제입니다. 두 서비스에 대한 엔드포인트가 지정됩니다.

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

그리고 다음은 URL 인코딩을 사용하는 동일한 연결 문자열의 예제입니다.

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

