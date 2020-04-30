---
title: VM 이미지에 대 한 공유 액세스 서명 URI를 가져옵니다. Azure Marketplace
description: 이 문서에서는 각 VHD (가상 하드 디스크)에 대해 SAS (공유 액세스 서명) URI를 가져오는 방법을 설명 합니다.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732632"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM 이미지에 대한 공유 액세스 서명 URI 가져오기

> [!IMPORTANT]
> Azure Virtual Machine 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품이 마이그레이션될 때까지 Cloud 파트너 포털의 [VM 이미지에 대 한 공유 액세스 서명 URI 가져오기](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) 의 지침에 따라 제품을 관리 하세요.

이 문서에서는 각 VHD (가상 하드 디스크)에 대해 SAS (공유 액세스 서명) URI (uniform resource identifier)를 생성 하는 방법을 설명 합니다.

게시 프로세스 중에 계획과 연결 된 각 VHD에 대 한 URI를 제공 해야 합니다. 이러한 계획은 이전에 Sku 또는 재고 유지 단위 라고 합니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 파트너 센터의 **계획** 탭에서이 URI를 입력 합니다.

Vhd에 대 한 SAS Uri를 생성할 때 다음 요구 사항을 따릅니다.

* 관리되지 않는 VHD만 지원됩니다.
* `List` 및 `Read` 권한만 필요 합니다. 쓰기 또는 삭제 권한을 제공 하지 마세요.
* 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
* UTC 시간 변경을 방지 하려면 시작 날짜를 현재 날짜의 1 일로 설정 합니다. 예를 들어 현재 날짜가 2019 년 10 월 6 일 이면 10/5/2019를 선택 합니다.

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소 (URL)를 만드는 데 사용 되는 두 가지 일반적인 도구는 다음과 같습니다.

* **Microsoft Storage 탐색기** – Windows, Macos 및 Linux에 사용할 수 있는 그래픽 도구입니다.
* **MICROSOFT AZURE CLI** – Windows 이외의 운영 체제 및 자동화 되거나 연속 통합 환경에 권장 됩니다.

### <a name="use-microsoft-storage-explorer"></a>Microsoft Storage 탐색기 사용

1. [Microsoft Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 다운로드 하 고 설치 합니다.
2. 탐색기를 열고 왼쪽 메뉴에서 **계정 추가**를 선택 합니다. **Azure Storage에 연결** 대화 상자가 나타납니다.
3. **Azure 계정 추가** 를 선택 하 고 **로그인**합니다. Azure 계정에 로그인 하는 데 필요한 단계를 완료 합니다.
4. 왼쪽**탐색기** 창에서 **저장소 계정** 으로 이동 하 고이 노드를 확장 합니다.
5. VHD를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다.
6. **공유 액세스 서명** 대화 상자가 나타납니다. 다음 필드를 작성합니다.

    * **시작 시간** – VHD 액세스를 위한 사용 권한 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * **만료 시간** – VHD 액세스를 위한 사용 권한 만료 날짜 현재 날짜 로부터 3 주 이상 지난 날짜를 제공 합니다.
    * **권한** – 읽기 및 목록 사용 권한을 선택 합니다.
    * **컨테이너 수준** – **컨테이너 수준 공유 액세스 서명 URI 생성** 확인란을 선택 합니다.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="공유 액세스 서명 대화 상자를 보여 줍니다.":::

7. 이 VHD에 연결 된 SAS URI를 만들려면 **만들기**를 선택 합니다. 대화 상자가 새로 고쳐지고이 작업에 대 한 세부 정보가 표시 됩니다.
8. **URI** 를 복사 하 여 안전한 위치의 텍스트 파일에 저장 합니다.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="공유 액세스 서명 정보 상자를 보여 줍니다.":::

    이 생성 된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 지정 하려면 텍스트 파일을 편집 하 여 VHD 이름 (다음 단계)을 추가 합니다.

9. SAS URI의 vhd 문자열 뒤에 VHD 이름을 삽입 합니다 (슬래시 포함). 최종 SAS URI는 다음과 같습니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`예를 들어 VDH `TestRGVM2.vhd`의 이름이 인 경우 결과 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 게시할 계획의 각 VHD에 대해이 단계를 반복 합니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

1. [MICROSOFT AZURE CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)를 다운로드 하 여 설치 합니다. 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일 (ps1 파일 확장명)을 만들고 다음 코드를 복사 하 여 로컬에 저장 합니다.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 다음 매개 변수 값을 사용 하도록 파일을 편집 합니다. 과 `2020-04-01T00:00:00Z`같은 UTC 날짜/시간 형식으로 날짜를 제공 합니다.

    * `<account-name>`– Azure storage 계정 이름
    * `<account-key>`– Azure storage 계정 키
    * `<vhd-name>`– 사용자의 VHD 이름
    * `<start-date>`– VHD 액세스를 위한 사용 권한 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * `<expiry-date>`– VHD 액세스 권한이 만료 되는 날짜입니다. 현재 날짜 로부터 3 주 이상 지난 날짜를 제공 합니다.

    이 예제에서는이 문서를 작성할 당시에 적절 한 매개 변수 값을 보여 줍니다.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 변경 내용을 저장합니다.
5. 다음 방법 중 하나를 사용 하 여 관리자 권한으로이 스크립트를 실행 하 여 컨테이너 수준 액세스에 대 한 **SAS 연결 문자열** 을 만듭니다.

    * 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭 하 고 **관리자 권한으로 실행**을 선택 합니다.
    * [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)와 같은 PowerShell 스크립트 편집기에서 스크립트를 실행 합니다. 이 화면에서는이 편집기 내에서 SAS 연결 문자열을 만드는 방법을 보여 줍니다.

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Windows PowerShell ISE 사용 하 여 SAS 연결 문자열을 만드는 방법을 보여 줍니다.":::

6. SAS 연결 문자열을 복사 하 여 안전한 위치의 텍스트 파일에 저장 합니다. 이 문자열을 편집 하 여 최종 SAS URI를 만드는 데 VHD 위치 정보를 추가 합니다.
7. Azure Portal에서 새 URI와 연결 된 VHD를 포함 하는 blob 저장소로 이동 합니다.
8. 다음 스크린샷에 표시 된 것 처럼 **Blob service 끝점**의 URL을 복사 합니다.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Blob service 끝점을 보여 줍니다.":::

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용 하 여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    예를 들어, VHD의 이름이 인 `TestRGVM2.vhd`경우 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

게시하려는 SKU의 각 VHD에 대해 이러한 단계를 반복합니다.

## <a name="verify-the-sas-uri"></a>SAS URI 확인

다음 검사 목록을 사용 하 여 생성 된 각 SAS URI를 검토 하 여 다음을 확인 합니다.

* URI는 다음과 같습니다.`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI에는 파일 이름 확장명 ".vhd"를 포함 하 여 VHD 이미지 파일 이름이 포함 됩니다.
* `sp=rl`는 URI의 중간에 표시 됩니다. 이 문자열은 및 `Read` `List` 액세스를 지정 하는 것을 보여 줍니다.
* 이 `sr=c` 표시 되 면 컨테이너 수준 액세스가 지정 된 것입니다.
* URI를 복사 하 여 브라우저에 붙여 넣습니다. blob을 테스트 하려면 다운로드를 완료 하기 전에 작업을 취소할 수 있습니다.

## <a name="next-step"></a>다음 단계

SAS URI를 만드는 데 문제가 있는 경우 [일반적인 SAS URL 문제](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)를 참조 하세요. 그렇지 않으면 나중에 사용할 수 있도록 안전한 위치에 SAS URI를 저장합니다. 파트너 센터에서 VM 제품을 게시 하는 데 필요 합니다.

* [Azure Virtual Machine 제품 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
