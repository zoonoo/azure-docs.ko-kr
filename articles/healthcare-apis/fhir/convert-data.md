---
title: Azure API for FHIR을 위한 데이터 변환
description: $convert-data 엔드포인트 및 customize-converter 템플릿을 사용하여 Azure API for FHIR에서 데이터를 변환합니다.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 2a34cfee57ecc1870c420c4c0f3c9261aa02f192
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490928"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>데이터를 FHIR로 변환하는 방법(미리 보기)

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure API for FHIR의 $convert-data 사용자 지정 엔드포인트는 다른 형식에서 FHIR로 데이터 변환을 위한 것입니다. Liquid 템플릿 엔진과 [FHIR 변환기](https://github.com/microsoft/FHIR-Converter) 프로젝트의 템플릿을 기본 템플릿으로 사용합니다. 필요에 따라 이러한 변환 템플릿을 사용자 지정할 수 있습니다. 현재 HL7v2에서 FHIR로의 변환을 지원합니다.

## <a name="use-the-convert-data-endpoint"></a>$convert-data 엔드포인트 사용

`https://<<FHIR service base URL>>/$convert-data`

$convert-data는 아래에 설명된 것처럼 요청 본문에서 [매개 변수](http://hl7.org/fhir/parameters.html) 리소스를 사용합니다.

**매개 변수 리소스:**

| 매개 변수 이름      | 설명 | 허용되는 값 |
| ----------- | ----------- | ----------- |
| inputData      | 변환할 데이터입니다. | JSON 문자열 데이터 유형의 유효한 값|
| inputDataType   | 입력 데이터 형식입니다. | ```HL7v2``` |
| templateCollectionReference | 템플릿 컬렉션에 대한 참조입니다. **기본 템플릿** 에 대한 참조이거나 Azure API for FHIR에 등록된 사용자 지정 템플릿 이미지일 수 있습니다. 템플릿을 사용자 지정하고, ACR에서 호스팅하고, Azure API for FHIR에 등록하는 방법에 대한 자세한 내용은 아래를 참조하세요.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | 데이터를 변환하는 동안 사용할 루트 템플릿입니다. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> 기본 템플릿을 사용하면 빠르게 시작할 수 있습니다. 그러나 Azure API for FHIR을 업그레이드할 때 업데이트될 수 있습니다. Azure API for FHIR의 여러 버전에서 데이터 변환 동작을 일관되게 유지하려면 Azure Container Registry에서 고유한 템플릿 복사본을 호스팅하고 이를 Azure API for FHIR에 등록하고 나중에 설명한 대로 API 호출에 사용해야 합니다.

**샘플 요청:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**샘플 응답:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>템플릿 사용자 지정

Visual Studio Code용 [FHIR Converter 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)을 사용하여 필요에 따라 템플릿을 사용자 지정할 수 있습니다. 이 확장은 대화형 편집 환경을 제공하며 Microsoft에서 게시한 템플릿 및 샘플 데이터를 쉽게 다운로드할 수 있게 해줍니다. 자세한 내용은 확장의 설명서를 참조하세요.

## <a name="host-and-use-templates"></a>템플릿 호스트 및 사용

ACR에서 고유한 템플릿 복사본을 호스트하는 것이 좋습니다. 사용자의 템플릿 사본을 호스팅하고 $convert-data 작업에서 이를 사용하는 데는 네 가지 단계가 있습니다.

1. Azure Container Registry로 템플릿을 푸시합니다.
1. Azure API for FHIR 인스턴스에서 관리 ID를 사용합니다.
1. Azure API for FHIR 관리 ID에 ACR 액세스를 제공합니다.
1. Azure API for FHIR에서 ACR 서버 등록

### <a name="push-templates-to-azure-container-registry"></a>Azure Container Registry로 템플릿 푸시

ACR 인스턴스를 만든 후에 _FHIR 변환기: 템플릿 푸시_([FHIR 변환기 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)에 있음)를 사용하여 사용자 지정된 템플릿을 ACR로 푸시합니다. 또는 이를 위해 [템플릿 관리 CLI 도구](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md)를 사용할 수 있습니다.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR에서 관리 ID 사용

Azure Portal에서 Azure API for FHIR 서비스의 인스턴스로 이동하여 **ID** 블레이드를 선택합니다.
Azure API for FHIR에서 관리 ID를 사용하려면 상태를 **켜기** 로 변경합니다.

![관리 ID 사용](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Azure API for FHIR에 ACR 액세스 제공

ACR 인스턴스에서 액세스 제어(IAM) 블레이드로 이동하여 _역할 할당 추가_ 를 선택합니다.

![ACR 역할 할당](media/convert-data/fhir-acr-role-assignment.png)

Azure API for FHIR 서비스 인스턴스에 AcrPull 역할을 부여합니다.

![Add Role](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Azure API for FHIR에서 ACR 서버 등록

Azure Portal를 사용 하거나 CLI를 사용 하 여 ACR 서버를 등록할 수 있습니다.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Azure Portal를 사용 하 여 ACR 서버 등록
FHIR 인스턴스의 Azure API에서 _데이터 변환_ 아래의 _아티팩트_ 블레이드로 이동 합니다. 현재 등록 된 ACR 서버 목록이 표시 됩니다. _추가_ 를 클릭 하 고 드롭다운에서 레지스트리 서버를 선택 합니다. 등록을 적용 하려면 [ _저장_ ]을 클릭 해야 합니다. 변경 내용을 적용 하 고 인스턴스를 다시 시작 하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="registering-the-acr-server-using-cli"></a>CLI를 사용 하 여 ACR 서버 등록
Azure API for FHIR에 최대 20개의 ACR 서버를 등록할 수 있습니다.

필요한 경우 Azure PowerShell에서 Healthcareapis CLI를 설치합니다.

```powershell
az extension add -n healthcareapis
```

아래 예제를 수행하여 Azure API for FHIR에 ACR 서버를 등록합니다.

##### <a name="register-a-single-acr-server"></a>단일 ACR 서비스 등록

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>여러 ACR 서비스 등록

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>확인

templateCollectionReference 매개 변수에서 템플릿 참조를 지정하여 $convert-data API로 호출합니다.

`<RegistryServer>/<imageName>@<imageDigest>`
