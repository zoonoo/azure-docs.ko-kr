---
title: VM 이미지에 대한 공유 액세스 서명 URI - Azure Marketplace
description: Azure Marketplace에서 VHD(가상 하드 디스크)에 대한 SAS(공유 액세스 서명) URI를 생성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/14/2020
ms.openlocfilehash: f3589fb9ae176e04f727f516cca7c18c87dad9e0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317504"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM 이미지에 대한 공유 액세스 서명 URI 가져오기

이 문서에서는 각 VHD(가상 하드 디스크)에 대해 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 생성하는 방법을 설명합니다.

게시 프로세스 동안 계획 (이전의 Sku)과 연결 된 각 VHD에 대 한 URI를 제공 해야 합니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 파트너 센터의 **계획** 탭에서 이 URI를 입력합니다.

VHD의 SAS URI를 생성할 때 다음 요구 사항을 따릅니다.

* 관리되지 않는 VHD만 지원됩니다.
* `List` 및 `Read` 권한만 필요합니다. 쓰기 또는 삭제 권한을 제공하지 마세요.
* 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
* UTC 시간 변경을 방지하려면 시작 날짜를 현재 날짜 전으로 설정합니다. 예를 들어 현재 날짜가 2019년 10월 6일이면 2019년 10월 5일을 선택합니다.

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소(URL)를 만드는 데 사용되는 일반적인 두 가지 도구는 다음과 같습니다.

* **Microsoft Storage Explorer** – Windows, macOS 및 Linux에서 사용할 수 있는 그래픽 도구
* **Microsoft Azure CLI** – 비 Windows 운영 체제 및 자동 또는 지속적인 통합 환경에 권장됨

### <a name="use-microsoft-storage-explorer"></a>Microsoft Storage Explorer 사용

1. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 다운로드하고 설치합니다.
2. 탐색기를 열고 왼쪽 메뉴에서 **계정 추가**를 선택합니다. **Azure Storage에 연결** 대화 상자가 표시됩니다.
3. **Azure 계정 추가**를 선택한 다음, **로그인**을 선택합니다. Azure 계정에 로그인하는 데 필요한 단계를 완료합니다.
4. 왼쪽 **탐색기** 창에서 **스토리지 계정**으로 이동하고 이 노드를 확장합니다.
5. VHD를 마우스 오른쪽 단추로 클릭한 다음 **공유 액세스 서명 가져오기**를 선택합니다.
6. **공유 액세스 서명** 대화 상자가 나타납니다. 다음 필드를 작성합니다.

    * **시작 시간** – VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * **만료 시간** – VHD 액세스 권한의 만료 날짜입니다. 현재 날짜보다 최소 3주 후의 날짜를 제공합니다.
    * **권한** – 읽기 및 목록 사용 권한을 선택합니다.
    * **컨테이너 수준** – **컨테이너 수준 공유 액세스 서명 URI 생성** 확인란을 선택합니다.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="공유 액세스 서명 대화 상자가 나타납니다.":::

7. 이 VHD에 연결되는 SAS URI를 만들려면 **만들기**를 선택합니다. 대화 상자가 새로 고쳐지고 이 작업에 대한 세부 정보가 표시됩니다.
8. **URI**를 복사하고, 안전한 위치에 텍스트 파일로 저장합니다.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="공유 액세스 서명 세부 정보 상자가 나타납니다.":::

    이 생성된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 구체적으로 만들려면 텍스트 파일을 편집하여 VHD 이름을 추가합니다(다음 단계).

9. SAS URI의 VHD 문자열 뒤에 VHD 이름을 삽입합니다(앞의 슬래시 포함). 최종 SAS URI는 다음과 같습니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` 예를 들어 VDH의 이름이 `TestRGVM2.vhd`이면 결과 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 게시할 계획의 각 VHD에 대해 이 단계를 반복합니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

1. [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)를 다운로드하고 설치합니다. 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일(.ps1 파일 확장명)을 만들고, 다음 코드를 복사한 다음, 로컬로 저장합니다.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 파일을 편집하여 다음 매개 변수 값을 사용합니다. `2020-04-01T00:00:00Z`와 같이 UTC 날짜/시간 형식으로 날짜를 제공합니다.

    * `<account-name>` –Azure Storage 계정 이름
    * `<account-key>` – Azure Storage 계정 키
    * `<vhd-name>` – VHD 이름
    * `<start-date>` – VHD 액세스 권한의 시작 날짜. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * `<expiry-date>` – VHD 액세스 권한의 만료 날짜. 현재 날짜 이후 최소 3주 후의 날짜를 제공합니다.

    이 예제에서는 적절한 매개 변수 값을 보여 줍니다(이 문서의 작성 시점 기준).

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 변경 내용을 저장합니다.
5. 다음 방법 중 하나를 사용하여 이 스크립트를 관리자 권한으로 실행해 컨테이너 수준 액세스를 위한 **SAS 연결 문자열**을 만듭니다.

    * 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.
    * [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)와 같은 PowerShell 스크립트 편집기에서 스크립트를 실행합니다. 이 화면은 이 편집기 내에서 SAS 연결 문자열을 만드는 것을 보여 줍니다.

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Windows PowerShell ISE를 사용하여 SAS 연결 문자열을 만드는 방법을 보여 줍니다.":::

6. SAS 연결 문자열을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 문자열을 편집하여 최종 SAS URI를 만들기 위한 VHD 위치 정보를 추가합니다.
7. Azure Portal에서 새 URI와 연결된 VHD를 포함하는 BLOB 스토리지로 이동합니다.
8. 다음 스크린샷에 표시된 것처럼 **Blob service 엔드포인트**의 URL을 복사합니다.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Blob service 엔드포인트를 보여 줍니다.":::

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용하여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    예를 들어 VHD의 이름이 `TestRGVM2.vhd`인 경우 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

게시할 계획의 각 VHD에 대해 이 단계를 반복합니다.

## <a name="verify-the-sas-uri"></a>SAS URI 확인

다음 검사 목록을 사용하여 생성된 각 SAS URI를 검토하여 다음을 확인합니다.

* URI는 다음과 같습니다. `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI에는 ".vhd" 파일 이름 확장명이 있는 VHD 이미지 파일 이름이 포함됩니다.
* `sp=rl`이 URI의 중간에 표시됩니다. 이 문자열은 `Read` 및 `List` 액세스를 지정하는 것을 보여 줍니다.
* `sr=c`이 표시되면 컨테이너 수준 액세스가 지정된 것입니다.
* Blob을 테스트 다운로드하려면 URI를 브라우저에 복사하여 붙여넣습니다(다운로드를 완료하기 전에 작업을 취소할 수 있음).

## <a name="next-step"></a>다음 단계

SAS URI를 만드는 데 문제가 있는 경우 [일반적인 SAS URL 문제](common-sas-uri-issues.md)를 참조하세요. 그렇지 않으면 나중에 사용할 수 있도록 안전한 위치에 SAS URI를 저장합니다. 파트너 센터에서 VM 제품을 게시하는 데 필요합니다.

* [Azure 가상 머신 제품 만들기](azure-vm-create-offer.md)
