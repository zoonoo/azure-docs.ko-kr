---
title: 개인 정보에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751081"
---
> [!NOTE]
> 보호 된 상태 정보 (화)를 검색 하려면 `domain=phi` 매개 변수 및 모델 버전 이상을 사용 `2020-04-01` 합니다.
>
> 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
끝점에 요청을 보낼 때 다음 엔터티 범주가 반환 됩니다 `/v3.1-preview.3/entities/recognition/pii` .


| 범주   |  Description                          |
|------------|-------------|
| [Person](#category-person)      |  사람의 이름입니다.  |
| [PersonType](#category-persontype) | 사용자가 보유 한 작업 유형 또는 역할 |
| [전화 번호](#category-phonenumber) |전화 번호 (미국과 EU 전화 번호만 해당). |
| [조직](#category-organization) |  회사, 그룹, 정부 기관 및 기타 조직  |
| [주소](#category-address) | 전체 우편 주소  |
| [Email](#category-email) | 전자 메일 주소.   |
| [URL](#category-url) | 웹 사이트에 대 한 Url입니다.  |
| [TCP/IP](#category-ip) | 네트워크 IP 주소.  |
| [DateTime](#category-datetime) | 날짜 및 시간입니다. | 
| [수량](#category-quantity) | 숫자 및 숫자 수량.  |
| [Azure 정보](#azure-information) | 식별 가능한 Azure 정보 (예: 인증 정보).  |
| [ID](#identification) | 재무 및 국가 관련 id입니다.  |

### <a name="category-person"></a>범주: Person

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        사람

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사람의 이름입니다. 또한로 반환 `domain=phi` 됩니다.
      
    :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>범주: PersonType

이 범주에는 다음 엔터티가 포함 됩니다.


:::row:::
    :::column span="":::
        **엔터티**

        PersonType

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자가 보유 한 작업 유형 또는 역할
      
    :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>범주: PhoneNumber

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전화 번호 (미국과 EU 전화 번호만 해당). 또한로 반환 `domain=phi` 됩니다.
      
    :::column-end:::
:::row-end:::


### <a name="category-organization"></a>범주: 조직

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        조직

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        의료

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        의료 회사 및 그룹.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        재고 교환

    :::column-end:::
    :::column span="2":::

        재고 교환 그룹. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 관련 조직.
      
    :::column-end:::

:::row-end:::


### <a name="category-address"></a>범주: 주소

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        주소

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전체 우편 주소입니다.
      
    :::column-end:::
:::row-end:::

### <a name="category-email"></a>범주: 전자 메일

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        Email

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전자 메일 주소.
      
    :::column-end:::
:::row-end:::

### <a name="category-url"></a>범주: URL

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        URL

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        웹 사이트에 대 한 Url입니다. 
      
    :::column-end:::
:::row-end:::

### <a name="category-ip"></a>범주: IP

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        IP

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        네트워크 IP 주소. 
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>범주: DateTime

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        DateTime

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        날짜 및 시간입니다. 
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        Date

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        일정 날짜
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>범주: 수량

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        수량

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자 및 숫자 수량.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        나이

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        에이징을.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure 정보

이러한 엔터티 범주는 인증 정보 및 연결 문자열을 포함 하 여 식별 가능한 Azure 정보를 포함 합니다. 매개 변수를 사용 하 여 반환 되지 않습니다 `domain=phi` .

:::row:::
    :::column span="":::
        **엔터티**

        Azure DocumentDB 인증 키 

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        Azure Cosmos DB 서버에 대 한 권한 부여 키입니다.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IAAS 데이터베이스 연결 문자열 및 Azure SQL 연결 문자열

    :::column-end:::
    :::column span="2":::

        Azure IaaS (infrastructure as a service) 데이터베이스에 대 한 연결 문자열 및 SQL 연결 문자열입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SQL 연결 문자열

    :::column-end:::
    :::column span="2":::

        Azure SQL Database 데이터베이스에 대 한 연결 문자열입니다.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT 연결 문자열  

    :::column-end:::
    :::column span="2":::

        Azure IoT에 대 한 연결 문자열입니다. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 게시 설정 암호  

    :::column-end:::
    :::column span="2":::

        Azure 게시 설정에 대 한 암호입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache 연결 문자열 

    :::column-end:::
    :::column span="2":::

        Redis cache에 대 한 연결 문자열입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Azure SaaS (software as a service)에 대 한 연결 문자열입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus 연결 문자열

    :::column-end:::
    :::column span="2":::

        Azure 서비스 버스에 대 한 연결 문자열입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        계정 키 Azure Storage 

    :::column-end:::
    :::column span="2":::

       Azure storage 계정에 대 한 계정 키입니다. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage 계정 키(일반)

    :::column-end:::
    :::column span="2":::

       Azure 저장소 계정에 대 한 일반 계정 키입니다.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server 연결 문자열 

    :::column-end:::
    :::column span="2":::

       SQL Server를 실행 하는 컴퓨터에 대 한 연결 문자열입니다.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification(식별)

[!INCLUDE [supported identification entities](./identification-entities.md)]
