---
title: Azure Data Factory에서 데이터 복사 도구 문제 해결
description: Azure Data Factory에서 데이터 복사 도구 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388405"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Azure Data Factory에서 데이터 복사 도구 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 데이터 복사 도구에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>오류 코드: 데이터 복사 도구의 유효성을 검사할 수 없습니다.

- **증상**: 데이터 복사 도구의 첫 번째 단계에서 "유효성을 검사할 수 없습니다." 라는 경고 메시지가 표시 됩니다.
- **원인**: 모든 타사 쿠키를 사용 하지 않도록 설정 하는 경우 발생할 수 있습니다.
- **해결 방법**: 
    - Internet Explorer 또는 Microsoft Edge 브라우저를 사용 합니다.
    - Chrome 브라우저를 사용 하는 경우 아래 지침에 따라 *microsoftonline.com* 및 *windows.net*에 대 한 쿠키 예외를 추가 합니다.
        1.  Chrome 브라우저를 엽니다.
        2.  오른쪽의 렌치 또는 세 줄을 클릭 합니다 (Google Chrome 사용자 지정 및 제어).
        3.  **설정**을 클릭합니다.
        4.  **쿠키** 를 검색 하거나 고급 설정에서 **개인 정보** 로 이동 합니다.
        5.  **콘텐츠 설정**을 선택 합니다.    
        6.  **로컬 데이터를 설정할 수 있도록**쿠키를 설정 해야 합니다 (권장).
        7.  **예외 관리**를 클릭 합니다. **호스트 이름 패턴** 아래에 다음을 입력 하 고 **허용** 이 동작 집합 인지 확인 합니다.
            - login.microsoftonline.com
            - login.windows.net
        8.  브라우저를 닫고 다시 합니다.
    - Firefox 브라우저를 사용 하는 경우 아래 지침에 따라 쿠키 예외를 추가 합니다.
        1. Firefox 메뉴에서 **도구**  >  **옵션**으로 이동 합니다.
        2. **개인정보 처리 방침**에서  >  현재 설정이 **기록에 사용자 지정 설정을 사용**하는**것을**볼 수 있습니다.
        3. **타사 쿠키 수락**에서 현재 설정이 **Never**인 경우 오른쪽의 **예외** 를 클릭 하 여 다음 사이트를 추가 해야 합니다.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  브라우저를 닫고 다시 합니다. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>오류 코드: 로그인 페이지를 열고 암호를 입력할 수 없습니다.

- **증상**: 데이터 복사 도구는 로그인 페이지로 리디렉션되고 로그인 페이지가 성공적으로 표시 되지 않습니다.
- **원인**: 네트워크 환경을 office 네트워크에서 홈 네트워크로 변경한 경우이 문제가 발생할 수 있습니다. 브라우저에는 몇 가지 캐시가 있습니다. 
- **해결 방법**: 
    1.  브라우저를 닫고 다시 시도 하세요. 문제가 여전히 있는 경우 다음 단계로 이동 합니다.   
    2.  Internet Explorer 브라우저를 사용 하는 경우 "Ctrl" + "Shift" + "P"를 눌러 개인 모드로 엽니다. Chrome 브라우저를 사용 하는 경우 incognito 모드에서 엽니다. ("Ctrl" + "shift" + "N")를 누릅니다. 문제가 여전히 있는 경우 다음 단계로 이동 합니다. 
    3.  다른 브라우저를 사용 하십시오. 


## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
