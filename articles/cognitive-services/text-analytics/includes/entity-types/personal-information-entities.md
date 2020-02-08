---
title: 개인 정보에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086869"
---
## <a name="personal-information-entity-types"></a>개인 정보 엔터티 형식:

### <a name="person"></a>Person
텍스트의 사람 이름을 인식 합니다.

언어:
* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                               | 모델 버전부터 사용 가능 |
|--------------|-----------------------------------------------------------|----------------------------------------|
| 해당 없음          | 인식할 수 있는 이름 (예: `Bill Gates``Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>조직  

조직, 회사, 기관, 회사, 클럽 및 기타 사용자 그룹을 인식 합니다.

언어: 

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                                                                       | 모델 버전부터 사용 가능|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| 해당 없음          | 조직 (예: `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>전화 번호

전화 번호 (미국 전화 번호만). 

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                    | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------|----------------------------------------|
| 해당 없음          | 미국 전화 번호 (예: `(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

메일 주소입니다. 

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                      | 모델 버전부터 사용 가능 |
|--------------|--------------------------------------------------|----------------------------------------|
| 해당 없음          | 전자 메일 주소 (예: `support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

인터넷 Url.

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                          | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------------|----------------------------------------|
| 해당 없음          | 웹 사이트에 대 한 Url (예: `https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP 주소

인터넷 프로토콜 주소

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                              | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------|----------------------------------------|
| 해당 없음          | 예 `10.0.0.101` 네트워크 주소 | `2020-02-01`                           |

### <a name="quantity"></a>수량 

숫자 수량

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                   | 모델 버전부터 사용 가능 |
|--------------|-------------------------------|----------------------------------------|
| Age          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

날짜 및 시간 엔터티

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                   | 모델 버전부터 사용 가능 |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU GPS 좌표

 유럽 연합 내의 위치에 대 한 GPS 좌표입니다. 

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                               | 모델 버전부터 사용 가능 |
|--------------|-------------------------------------------|----------------------------------------|
| 해당 없음          | 유럽 연합 내의 GPS 좌표 | `2019-10-01`                           |

### <a name="azure-information"></a>Azure 정보

인증 정보 및 연결 문자열을 포함 하는 식별 가능한 Azure 정보 

* 모델 버전 `2019-10-01`부터 사용 가능 합니다.

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름                          | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 인증 키             | Azure DocumentDB 서버용 인증 키입니다.                           |
| Azure IAAS 데이터베이스 연결 문자열 | Azure IaaS (Infrastructure as a service) 데이터베이스에 대 한 연결 문자열입니다. |
| Azure SQL 연결 문자열           | Azure SQL database에 대 한 연결 문자열입니다.                                |
| Azure IoT 연결 문자열           | Azure IoT (사물 인터넷)에 대 한 연결 문자열입니다.                        |
| Azure 게시 설정 암호        | Azure 게시 설정에 대 한 암호입니다.                                        |
| 연결 문자열 Azure Redis Cache   | Redis 용 Azure 캐시에 대 한 연결 문자열입니다.                             |
| Azure SAS                             | Azure SAS (Software as a Service)에 대 한 연결 문자열입니다.                     |
| 연결 문자열 Azure Service Bus   | Azure 서비스 버스에 대 한 연결 문자열입니다.                                |
| 계정 키 Azure Storage             | Azure storage 계정에 대 한 계정 키입니다.                                   |
| Azure Storage 계정 키 (일반)   | Azure 저장소 계정에 대 한 일반 계정 키입니다.                           |
| 연결 문자열 SQL Server          | SQL server에 대 한 연결 문자열입니다.                                         |

### <a name="identification"></a>Identification(식별)

* 모델 버전 `2019-10-01`부터 사용 가능 합니다.

언어:

* 공개 미리 보기: `English`

#### <a name="financial-account-identification"></a>금융 계정 식별

| 하위 형식 이름               | Description                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA 라우팅 번호        | ABA (미국식 은행원 Association) 전송 라우팅 번호입니다.                  |
| SWIFT 코드                 | 지불 지침 정보에 대 한 코드를 SWIFT.                           |
| 신용 카드                | 신용 카드 번호.                                                       |
| IBAN 코드                  | 지불 지침 정보에 대 한 IBAN 코드입니다.                            |

#### <a name="government-and-country-specific-identification"></a>정부 및 국가 관련 식별

아래 엔터티는 country로 그룹화 되 고 나열 됩니다.

아르헨티나
* DNI (국가 Id) 번호

오스트레일리아
* 세금 파일 번호 
* 드라이버의 라이선스 ID
* Passport ID
* 의료 계정 번호
* 은행 계좌 번호 (예: 확인, 절약 및 직불 계정)

벨기에
* 국가 번호

브라질
* 법적 엔터티 번호 (CNPJ)
* CPF 번호
* 국가 ID 카드 (RG)

Canada
* Passport ID
* 드라이버의 라이선스 ID
* 의료 보험 수
* 개인 health ID 번호 (PHIN)
* 주민 등록 번호
* 은행 계좌 번호 (예: 확인, 절약 및 직불 계정)

칠레
* Id 카드 번호 

중국
* Id 카드 번호
* 중국 (상주 ID 카드) 번호

크로아티아
* ID 카드 번호
* 있는 OIB (개인 ID) 번호

체코
* 국가 ID 카드 번호

덴마크
* 개인 ID 번호

EU (유럽 연합)
* 국가 ID 번호
* Passport ID
* 드라이버의 라이선스 ID
* SSN (사회 보장 번호) 또는 동등한 ID
* EU 세금 식별 번호 (TIN)
* EU 직불 카드 번호

핀란드
* 국가 ID 번호
* Passport ID

프랑스
* CNI (국내 ID card)
* 주민 등록 번호 (INSEE)
* Passport ID
* 드라이버의 라이선스 ID

독일
* ID 카드 번호
* Passport ID
* 드라이버의 라이선스 ID

그리스 
* 국가 ID 카드 번호

홍콩
* ID 카드 (HKID) 번호

인도
* PAN (영구 계정 번호)
* Aadhaar (고유 ID) 번호

인도네시아
* KTP (ID 카드 번호)

아일랜드
* PPS (Personal Public Service) 번호

이스라엘
* 국가 ID
* 은행 계좌 번호 (예: 확인, 절약 및 직불 계정)

이탈리아
* 드라이버의 라이선스 ID

일본
* 상주 등록 번호
* 거주지 카드 번호
* 드라이버의 라이선스 ID
* 주민 등록 번호 (SIN)
* Passport ID
* 은행 계좌 번호 (예: 확인, 절약 및 직불 계정)

말레이시아
* ID 카드 번호

네덜란드
* 시민 서비스 (BSN) 번호

뉴질랜드
* 경제 산업성

노르웨이
* ID 카드 번호

필리핀
* 통합 된 다목적 ID 번호

폴란드
* ID 카드 번호
* 국가 ID (PESEL)
* Passport ID

포르투갈 
* 시민 카드 번호

사우디아라비아
* 국가 ID

싱가포르
* 국가별 등록 ID 카드 (NRIC) 번호

남아프리카
* ID 번호
* 상주 등록 번호

대한민국
* 상주 등록 번호

스페인 
* SSN(사회 보장 번호)

스웨덴
* 국가 ID
* Passport ID

대만 
* 국가 ID
* 상주 인증서 (ARC/TARC) 번호
* Passport ID

태국
* 모집단 식별 코드

United Kingdom
* Passport ID
* 드라이버의 라이선스 ID
* 국가 보험 번호 (NINO)
* 국가 상태 관리 서비스 (NHS) 번호

미국
* SSN(사회 보장 번호)
* 드라이버의 라이선스 ID
* Passport ID
* 사용한 선거 지도로 roll 번호
* 개별 세금 ID 번호 (스냅숏입니다)
* DEA (약품 적용 에이전시) 번호
* 은행 계좌 번호 (예: 확인, 절약 및 직불 계정)
