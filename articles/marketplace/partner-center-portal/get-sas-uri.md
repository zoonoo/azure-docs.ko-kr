---
title: VM 이미지에 대한 공유 액세스 서명 URI 받기 | Azure 마켓플레이스
description: 이 문서에서는 각 가상 하드 디스크(VHD)에 대한 공유 액세스 서명(SAS) URI를 얻는 방법을 설명합니다.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732632"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM 이미지에 대한 공유 액세스 서명 URI 가져오기

> [!IMPORTANT]
> Azure 가상 컴퓨터 의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털에 [대한 VM 이미지에 대한 공유 액세스 서명 URI의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) 지침을 따라 오퍼를 관리하십시오.

이 문서에서는 각 가상 하드 디스크(VHD)에 대해 SAS(공용 액세스 서명) 균일 한 리소스 식별자(URI)를 생성하는 방법을 설명합니다.

게시 프로세스 중에 계획과 연결된 각 VHD에 대해 URI를 제공해야 합니다. 이러한 계획은 이전에 SCO 또는 재고 유지 단위로 언급되었습니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 파트너 센터의 **계획** 탭에서 이 URI를 입력합니다.

VHD에 대한 SAS URI를 생성할 때다음 요구 사항을 따르십시오.

* 관리되지 않는 VHD만 지원됩니다.
* `Read` 권한만 `List` 필요합니다. 쓰기 또는 삭제 액세스를 제공하지 마십시오.
* 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
* UTC 시간 변경으로부터 보호하려면 시작 날짜를 현재 날짜 보다 1일 앞의 날짜로 설정합니다. 예를 들어 현재 날짜가 2019년 10월 6일인 경우 2019년 10월 5일을 선택합니다.

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소(URL)를 만드는 데 사용되는 두 가지 일반적인 도구가 있습니다.

* **마이크로소프트 스토리지 탐색기-** 윈도우에 사용할 수 있는 그래픽 도구, 맥 OS, 그리고 리눅스.
* **Microsoft Azure CLI** – Windows 이외의 운영 체제 및 자동화또는 지속적인 통합 환경에 권장됩니다.

### <a name="use-microsoft-storage-explorer"></a>마이크로소프트 스토리지 탐색기 사용

1. 다운로드 및 [마이크로 소프트 Azure 스토리지 탐색기를](https://azure.microsoft.com/features/storage-explorer/)설치합니다.
2. 탐색기를 열고 왼쪽 메뉴에서 **계정 추가를**선택합니다. **Azure 저장소에 연결** 대화 상자가 나타납니다.
3. **Azure 계정 추가를** 선택한 다음 에 **로그인합니다.** Azure 계정에 로그인하는 데 필요한 단계를 완료합니다.
4. 왼쪽**탐색기** 창에서 **저장소 계정으로** 이동하여 이 노드를 확장합니다.
5. VHD를 마우스 오른쪽 단추로 클릭한 다음 **공유 액세스 서명 받기를**선택합니다.
6. **공유 액세스 서명** 대화 상자가 나타납니다. 다음 필드를 작성합니다.

    * **시작 시간** – VHD 액세스에 대한 권한 시작 날짜. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * **만료 시간** – VHD 액세스에 대한 권한 만료 날짜입니다. 현재 날짜보다 3주 이상 지난 날짜를 제공합니다.
    * **사용 권한** - 읽기 및 목록 사용 권한을 선택합니다.
    * **컨테이너 수준** - **컨테이너 수준 공유 액세스 시그니처 URI 생성** 확인란을 선택합니다.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="공유 액세스 서명 대화 상자 를 보여 줍니다.":::

7. 이 VHD에 대해 연결된 SAS URI를 만들려면 **을**선택합니다. 대화 상자가 새로 고쳐지고 이 작업에 대한 세부 정보가 표시됩니다.
8. **URI를** 복사하여 안전한 위치에 있는 텍스트 파일에 저장합니다.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="공유 액세스 서명 세부 정보 상자 설명":::

    이 생성된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 구체적으로 만들려면 텍스트 파일을 편집하여 VHD 이름(다음 단계)을 추가합니다.

9. SAS URI의 vhds 문자열 에 VHD 이름을 삽입합니다(정방향 슬래시 포함). 최종 SAS URI는 다음과 같아야 합니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`예를 들어 VDH의 이름이 `TestRGVM2.vhd`다음과 같은 경우 결과 SAS URI는 다음과 같이 됩니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 게시할 계획에서 각 VHD에 대해 다음 단계를 반복합니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

1. 다운로드 및 [마이크로 소프트 Azure CLI를 설치합니다.](https://azure.microsoft.com/documentation/articles/xplat-cli-install/) 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일(.ps1 파일 확장자)을 만들고 다음 코드에서 복사한 다음 로컬로 저장합니다.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 다음 매개 변수 값을 사용하도록 파일을 편집합니다. 와 같은 `2020-04-01T00:00:00Z`UTC 날짜 시간 형식으로 날짜를 제공합니다.

    * `<account-name>`– Azure 저장소 계정 이름
    * `<account-key>`– Azure 저장소 계정 키
    * `<vhd-name>`– 당신의 VHD 이름
    * `<start-date>`– VHD 액세스에 대한 권한 시작 날짜. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    * `<expiry-date>`– VHD 액세스에 대한 권한 만료 날짜. 현재 날짜로부터 최소 3주 후에 날짜를 제공합니다.

    이 예제에서는 적절한 매개 변수 값(이 작성 시)을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 수있습니다.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 변경 내용을 저장합니다.
5. 다음 방법 중 하나를 사용하여 관리 권한으로 이 스크립트를 실행하여 컨테이너 수준 액세스에 대한 **SAS 연결 문자열을** 만듭니다.

    * 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭하고 **관리자로 실행을**선택합니다.
    * [Windows PowerShell ISE와](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)같은 PowerShell 스크립트 편집기에서 스크립트를 실행합니다. 이 화면에는 이 편집기 내에서 SAS 연결 문자열이 생성되는 것을 보여 주며, 이 화면은 다음과 같은 것입니다.

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Windows PowerShell ISE를 사용하여 SAS 연결 문자열을 만드는 것을 보여 줍니다.":::

6. SAS 연결 문자열을 복사하여 안전한 위치에 있는 텍스트 파일에 저장합니다. 이 문자열을 편집하여 VHD 위치 정보를 추가하여 최종 SAS URI를 만듭니다.
7. Azure 포털에서 새 URI와 연결된 VHD를 포함하는 Blob 저장소로 이동합니다.
8. 다음 스크린샷과 같이 **Blob 서비스 끝점의**URL을 복사합니다.

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Blob 서비스 끝점을 보여 줍니다.":::

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용하여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    예를 들어 VHD의 `TestRGVM2.vhd`이름이 있는 경우 SAS URI는 다음과 같이 됩니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

게시하려는 SKU의 각 VHD에 대해 이러한 단계를 반복합니다.

## <a name="verify-the-sas-uri"></a>SAS URI 확인

다음 검사 목록을 사용하여 생성된 각 SAS URI를 검토하여 다음을 확인합니다.

* URI는 다음과 같습니다.`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI에는 파일 이름 확장자 ".vhd"를 포함하여 VHD 이미지 파일 이름이 포함됩니다.
* `sp=rl`URI의 중간 근처에 나타납니다. 이 문자열은 `Read` `List` 해당 문자열과 액세스가 지정되어 있음을 보여 주며 이를 보여 주며
* 이 `sr=c` 경우 컨테이너 수준 액세스가 지정됩니다.
* URI를 복사하여 브라우저에 붙여넣으면 Blob을 테스트 다운로드할 수 있습니다(다운로드가 완료되기 전에 작업을 취소할 수 있음).

## <a name="next-step"></a>다음 단계

SAS URI를 만드는 데 어려움이 있는 경우 [공통 SAS URL 문제를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)참조하십시오. 그렇지 않으면 나중에 사용할 수 있도록 안전한 위치에 SAS URI를 저장합니다. 파트너 센터에 VM 오퍼를 게시하려면 VM 오퍼가 필요합니다.

* [Azure Virtual Machine 제품 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
