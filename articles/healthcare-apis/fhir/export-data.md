---
title: Azure API for FHIR $export 명령을 호출하여 내보내기 실행
description: 이 문서에서는 $export 사용하여 FHIR 데이터를 내보내는 방법을 설명합니다.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: 7df88f1a425b563733310d69fc14d85f9251ae44
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108794379"
---
# <a name="how-to-export-fhir-data"></a>FHIR 데이터를 내보내는 방법


대량 내보내기 기능을 사용하면 [FHIR 사양](https://hl7.org/fhir/uv/bulkdata/export/index.html)에 따라 FHIR 서버에서 데이터를 내보낼 수 있습니다. 

$export 사용하기 전에 Azure API for FHIR 사용하도록 구성해야 합니다. 내보내기 설정을 구성하고 Azure Storage 계정을 만들려면 [내보내기 데이터 구성 페이지를 참조하세요.](configure-export-data.md)

## <a name="using-export-command"></a>$export 명령 사용

내보내기를 위해 Azure API for FHIR 구성한 후 $export 명령을 사용하여 서비스에서 데이터를 내보낼 수 있습니다. 데이터는 내보내기 구성 중 지정한 스토리지 계정에 저장됩니다. FHIR 서버에서 $export 명령을 호출하는 방법을 알아보려면 [HL7 FHIR $export 사양](https://hl7.org/Fhir/uv/bulkdata/export/index.html)에 대한 설명서를 참조하세요.


**작업이 잘못된 상태로 고정되었습니다.**

경우에 따라 작업이 잘못된 상태로 고정될 수 있습니다. 이 문제는 특히 스토리지 계정 권한이 제대로 설정되지 않은 경우에 발생할 수 있습니다. 내보내기가 성공했는지 확인하는 한 가지 방법은 스토리지 계정을 확인하여 해당 컨테이너(즉, ndjson) 파일이 있는지 확인하는 것입니다. 실행 중인 다른 내보내기 작업이 없는 경우 현재 작업이 잘못된 상태로 고정된 것일 수 있습니다. 취소 요청을 전송하여 내보내기 작업을 취소하고 작업을 다시 큐에 대기시켜야 합니다. 잘못된 상태의 내보내기 기본 실행 시간은 중지되고 새 작업으로 이동하거나 내보내기를 다시 시도하기 전에 10분입니다. 

Azure API For FHIR은 다음 수준에서 $export 지원합니다.
* [시스템](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [환자:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)`GET https://<<FHIR service base URL>>/Patient/$export>>`
* [환자 *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -FHIR 용 Azure API는 관련 된 모든 리소스를 내보내며 그룹의 특성은 내보내지 않습니다. `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

데이터를 내보내면 각 리소스 유형에 대해 별도의 파일이 만들어집니다. 내보낸 파일이 너무 커지지 않도록 합니다. 단일 내보낸 파일의 크기가 64 MB 보다 커지면 새 파일을 만듭니다. 결과적으로 각 리소스 종류에 대 한 여러 파일을 가져올 수 있습니다 (즉, 환자-1. ndjson, 환자-2. ndjson). 


> [!Note] 
> `Patient/$export``Group/[ID]/$export`리소스가 둘 이상의 리소스 구획에 있거나 여러 그룹에 있는 경우는 중복 리소스를 내보낼 수 있습니다.

또한 큐 중에 위치 헤더에서 반환 된 URL을 통해 내보내기 상태를 확인 하는 작업은 실제 내보내기 작업을 취소 하는 것과 함께 지원 됩니다.

### <a name="exporting-fhir-data-to-adls-gen2"></a>ADLS Gen2로 FHIR 데이터 내보내기

현재 ADLS Gen2 사용 가능한 저장소 계정에 대 한 $export 지원 되며 다음과 같은 제한 사항이 있습니다.

- 사용자는 [계층적 네임 스페이스](../../storage/blobs/data-lake-storage-namespace.md)를 사용할 수 없지만 컨테이너 내의 특정 하위 디렉터리로 내보내기를 대상으로 지정할 수 있는 방법은 없습니다. 특정 컨테이너를 대상으로 지정 하는 기능만 제공 합니다 (각 내보내기에 대 한 새 폴더를 만드는 경우).
- 내보내기가 완료 된 후에는 동일한 컨테이너에 대 한 후속 내보내기가 새로 만든 폴더 내에 있기 때문에 해당 폴더에 아무것도 내보내지 않습니다.


## <a name="settings-and-parameters"></a>설정 및 매개 변수

### <a name="headers"></a>헤더
$Export 작업에 대해 설정 해야 하는 두 개의 필수 헤더 매개 변수가 있습니다. 값은 현재 [$export 사양](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)에 따라 정의 됩니다.
* **Accept** -application/fhir + json
* **선호** -응답-async

### <a name="query-parameters"></a>쿼리 매개 변수
FHIR 용 Azure API는 다음과 같은 쿼리 매개 변수를 지원 합니다. 이러한 매개 변수는 모두 선택 사항입니다.

|쿼리 매개 변수        | FHIR 사양에 정의 되어 있나요?    |  Description|
|------------------------|---|------------|
| \_outputFormat | 예 | 현재 FHIR 사양에 맞게 세 가지 값(application/fhir+ndjson, application/ndjson 또는 ndjson)을 지원합니다. 모든 내보내기 작업이 `ndjson` 반환되고 전달된 값은 코드 동작에 영향을 미치지 않습니다. |
| \_since | 예 | 제공된 시간 이후에 수정된 리소스만 내보낼 수 있습니다. |
| \_형식 | 예 | 포함할 리소스 유형을 지정할 수 있습니다. 예를 들어 \_ type=Patient는 환자 리소스만 반환합니다.|
| \_typefilter | 예 | 세분화된 필터링을 요청하려면 \_ typefilter를 형식 매개 변수와 함께 사용할 수 \_ 있습니다. _typeFilter 매개 변수의 값은 결과를 추가로 제한하는 FHIR 쿼리의 쉼표로 구분된 목록입니다. |
| \_container | 예 |  데이터를 내보내야 하는 구성된 스토리지 계정 내의 컨테이너를 지정합니다. 컨테이너를 지정하면 이름이 인 새 폴더의 해당 컨테이너로 데이터를 내보냅니다. 컨테이너를 지정하지 않으면 타임스탬프 및 작업 ID를 사용하여 새 컨테이너로 내보냅니다. |

> [!Note]
> Azure API for FHIR 구독과 동일한 구독의 스토리지 계정만 $export 작업의 대상으로 등록할 수 있습니다.

## <a name="secure-export-to-azure-storage"></a>Azure Storage 내보내기 보호

Azure API for FHIR 보안 내보내기 작업을 지원합니다. 보안 내보내기를 실행하는 한 가지 옵션은 Azure API for FHIR 연결된 특정 IP 주소가 Azure Storage 계정에 액세스하도록 허용하는 것입니다. 스토리지 계정이 Azure API for FHIR 위치와 동일하거나 다른 위치에 있는지 여부에 따라 구성이 다릅니다.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Azure Storage 계정이 다른 지역에 있는 경우

포털에서 Azure Storage 계정의 **네트워킹을** 선택합니다. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="네트워킹 설정을 Azure Storage 합니다." lightbox="media/export-data/storage-networking.png":::
   
**선택한 네트워크** 를 선택합니다. 방화벽 섹션 아래의 **주소 범위** 상자에 IP 주소를 지정 합니다. 인터넷 또는 온-프레미스 네트워크에서의 액세스를 허용 하는 IP 범위를 추가 합니다. Azure API for FHIR 서비스가 프로 비전 되는 Azure 지역에 대 한 아래 표에서 IP 주소를 찾을 수 있습니다.

|**Azure 지역**         |**공용 IP 주소** |
|:----------------------|:-------------------|
| 오스트레일리아 동부       | 20.53.44.80       |
| 캐나다 중부       | 20.48.192.84      |
| 미국 중부           | 52.182.208.31     |
| 미국 동부              | 20.62.128.148     |
| 미국 동부 2            | 20.49.102.228     |
| 미국 동부 2 EUAP       | 20.39.26.254      |
| 독일 북부        | 51.116.51.33      |
| 독일 중서부 | 51.116.146.216    |
| 일본 동부           | 20.191.160.26     |
| 한국 중부        | 20.41.69.51       |
| 미국 중북부     | 20.49.114.188     |
| 북유럽         | 52.146.131.52     |
| 남아프리카 북부   | 102.133.220.197   |
| 미국 중남부     | 13.73.254.220     |
| 동남 아시아       | 23.98.108.42      |
| 스위스 북부    | 51.107.60.95      |
| 영국 남부             | 51.104.30.170     |
| 영국 서부              | 51.137.164.94     |
| 미국 중서부      | 52.150.156.44     |
| 서유럽          | 20.61.98.66       |
| 미국 서부 2            | 40.64.135.77      |

> [!NOTE]
> 위의 단계는 데이터를 FHIR로 변환하는 방법(미리 보기) 문서에 설명된 구성 단계와 비슷합니다. 자세한 내용은 [템플릿 호스트 및 사용을 참조하세요.](https://docs.microsoft.com/azure/healthcare-apis/fhir/convert-data#host-and-use-templates)

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Azure Storage 계정이 동일한 지역에 있는 경우

구성 프로세스는 CIDR 형식의 특정 IP 주소 범위가 100.64.0.0/10 대신 사용됨을 제외하고 위와 동일합니다. 100.64.0.0 – 100.127.255.255를 포함하는 IP 주소 범위를 지정해야 하는 이유는 서비스에서 사용하는 실제 IP 주소가 다양하지만 각 $export 요청에 대해 범위 내에 있기 때문입니다.

> [!Note] 
> 10.0.2.0/24 범위 내의 개인 IP 주소를 대신 사용할 수 있습니다. 이 경우 $export 작업이 성공하지 않습니다. $export 요청을 다시 시도할 수 있지만 100.64.0.0/10 범위 내의 IP 주소가 다음에 사용된다는 보장은 없습니다. 이는 의도적으로 알려진 네트워킹 동작입니다. 대안은 다른 지역에서 스토리지 계정을 구성하는 것입니다.
    
## <a name="next-steps"></a>다음 단계

이 문서에서는 $export 명령을 사용하여 FHIR 리소스를 내보내는 방법을 배웠습니다. 다음으로, 식별되지 않은 데이터를 내보내는 방법을 알아보려면 다음을 참조하세요.
 
>[!div class="nextstepaction"]
>[식별되지 않은 데이터 내보내기](de-identified-export.md)
