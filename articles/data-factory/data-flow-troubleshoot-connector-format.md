---
title: 매핑 데이터 흐름의 커넥터 및 형식 문제 해결하기
description: Azure Data Factory에서 커넥터 및 형식과 관련된 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739479"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory에서 매핑 데이터 흐름의 커넥터 및 형식 문제 해결하기


본 문서에서는 ADF(Azure Data Factory)에서의 매핑 데이터 흐름의 커넥터 및 형식에 대한 문제 해결 방법을 살펴봅니다.


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>소스의 사용자 지정 스키마 지원하기

#### <a name="symptoms"></a>증상
Cosmos DB/JSON에서 다른 데이터 저장소로 데이터를 이동하거나 전송하기 위해 ADF 데이터 흐름을 사용하려는 경우, 원본 데이터의 열이 일부 누락될 수 있습니다. 

#### <a name="cause"></a>원인 
스키마가 없는 커넥터(열 번호, 열 이름, 행별 열 데이터 형식은 다른 것들과 비교해 달라질 수 있습니다)의 경우, 기본적으로 ADF는 샘플 행(예: 최상위 100행 또는 1,000행 데이터)을 사용해 스키마를 유추하고, 유추 결과를 스키마로 사용해 데이터를 읽습니다. 따라서 샘플 행에 표시되지 않는 추가적인 열이 자신의 데이터 저장소에 있다면 해당 열의 데이터는 읽거나 싱크 데이터 저장소로 이동 또는 전송하지 않습니다.

#### <a name="recommendation"></a>권장
기본 동작을 덮어쓰고 추가 필드를 가져오기 위해 ADF에서는 원본 스키마를 사용자 지정할 수 있는 옵션을 제공합니다. 데이터를 읽기 위해 데이터 흐름 원본 프로젝션의 스키마 유추 결과에서 제외될 수 있는 추가/누락 열을 지정할 수 있으며, 다음 옵션 중 하나를 적용해 사용자 지정된 스키마를 설정할 수 있습니다. 일반적으로는 **옵션-1** 이 더 좋습니다.

- **옵션-1**: 복잡한 스키마로 이루어진 수백만 행이 들어 있는 하나의 대형 파일이나 테이블 또는 컨테이너일 수 있는 원본 소스 데이터를 비교해, 읽으려는 열이 모두 들어 있는 소수의 행으로 이루어진 임시 테이블/컨테이너를 만든 뒤에 다음의 작업으로 넘어갈 수 있습니다. 

    1. 해당 데이터 흐름 소스 **디버그 설정** 을 사용해 샘플 파일/테이블을 갖춘 **프로젝션 가져오기** 가 전체 스키마를 가져오도록 합니다. 다음 그림의 단계를 따를 수 있습니다.<br/>

        ![원본 스키마를 사용자 지정하는 첫 번째 옵션의 첫 번째 부분을 보여 주는 스크린샷](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. 데이터 흐름 캔버스에서 **디버그 설정** 을 선택합니다.
         1. 팝업 창에서 **cosmosSource** 탭에 **샘플 테이블** 을 선택하고 **테이블** 블록에 자신의 테이블 이름을 입력합니다.
         1. **저장** 을 선택하여 설정을 저장합니다.
         1. **프로젝션 가져오기** 를 선택합니다.<br/>  
    
    1. 나머지 데이터 이동/전송에 해당 원본 데이터 세트를 사용하도록 **디버그 세팅** 을 되돌립니다. 다음 그림의 단계로 진행할 수 있습니다.<br/>

        ![원본 스키마를 사용자 지정하는 첫 번째 옵션의 두 번째 부분을 보여 주는 스크린샷](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. 데이터 흐름 캔버스에서 **디버그 설정** 을 선택합니다.
         1. 팝업 창에서 **cosmosSource** 탭에 **원본 데이터 세트** 를 선택합니다.
         1. **저장** 을 선택하여 설정을 저장합니다.<br/>
    
    이후, ADF 데이터 흐름 루틴은 원본 데이터 저장소의 데이터를 읽기 위해 사용자 지정 스키마를 인식 후 사용합니다. <br/>

- **옵션-2**: 해당 스키마 및 원본 데이터의 DSL 언어에 익숙한 경우, 해당 데이터를 읽기 위해 추가/누락 열을 추가하여 수동으로 데이터 흐름 원본 스크립트를 업데이트할 수도 있습니다. 다음 그림에서 그 예시를 보여 주고 있습니다. 

    ![원본 스키마를 사용자 지정하는 두 번째 옵션을 보여 주는 스크린샷](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>다음 단계
문제 해결에 관한 자세한 내용은 다음 리소스를 참조하세요.

*  [Azure Data Factory의 매핑 데이터 흐름 문제 해결](data-flow-troubleshoot-guide.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)