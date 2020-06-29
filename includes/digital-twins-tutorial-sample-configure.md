---
author: baanders
description: Azure Digital Twins 자습서에 대한 파일 포함 - 샘플 프로젝트 구성
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 463fb7860eb35a7ff45d3794d200c69685825827
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737202"
---
## <a name="configure-the-sample-project"></a>샘플 프로젝트 구성

다음으로, Azure Digital Twins 인스턴스와 상호 작용하는 샘플 클라이언트 애플리케이션을 설정합니다. 샘플 프로젝트를 아직 다운로드하지 않았다면 지금 [Azure Digital Twins 샘플 리포지토리를 ZIP 파일로 다운로드](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip)합니다. 

컴퓨터에서 다운로드된 파일을 찾아 압축을 풉니다.

압축을 푼 폴더 내에서 _digital-twins-samples-master/AdtSampleApp/_ 으로 이동합니다. Visual Studio 2019에서 _**AdtE2ESample.sln**_ 을 엽니다. 

Visual Studio에서 *솔루션 탐색기* 창을 사용하여 _SampleClientApp > **serviceConfig.json.TEMPLATE**_ 파일의 복사본을 만듭니다(마우스 오른쪽 단추 선택 메뉴를 사용하여 복사 및 붙여넣기 가능). 복사본의 이름을 *serviceConfig.json*으로 바꿉니다. 이 파일은 프로젝트를 실행하는 데 필요한 구성 변수와 함께 미리 설정된 JSON 파일 역할을 합니다.

*serviceConfig.json* 파일을 선택하여 편집 창에서 엽니다. `tenantId`를 *디렉터리 ID*로, `clientId`를 *애플리케이션 ID*로, `instanceUrl`을 Azure Digital Twins 인스턴스 *hostName* URL로 바꿉니다(아래에서 볼 수 있듯이 앞에 *https://* 를 붙임).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

파일을 저장하고 닫습니다. 

다음으로, *serviceConfig.json* 파일을 구성하여 *SampleClientApp*을 빌드할 때 출력 디렉터리로 복사되도록 합니다. 이렇게 하려면 *serviceConfig.json* 파일을 마우스 오른쪽 단추로 선택하고 *속성*을 선택합니다. *속성* 검사기에서 *출력 디렉터리로 복사* 속성의 값을 *변경된 내용만 복사*로 변경합니다.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="솔루션 탐색기 창에서 serviceConfig.json이 강조 표시되고 속성 창의 '출력 디렉터리로 복사' 속성이 '변경된 내용만 복사'로 설정된 Visual Studio 창" border="false":::

_**AdtE2ESample**_ 프로젝트를 자습서에서 계속 사용해야 하므로 Visual Studio에서 열어 둡니다.

