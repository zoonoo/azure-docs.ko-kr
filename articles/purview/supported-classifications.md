---
title: 지원 되는 분류 목록
description: 이 페이지에는 Azure 부서의 범위에서 지원 되는 시스템 분류가 나열 됩니다.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 7458b027add8be86d9491c674c2f1a0bc9fbc68c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554235"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure 부서의 범위에서 지원 되는 분류

이 문서에서는 Azure 부서의 범위 (미리 보기)에서 지원 되 고 정의 된 시스템 분류를 나열 합니다.

## <a name="defined-system-classifications"></a>정의 된 시스템 분류

Azure 부서의 범위는 [RegEx](https://wikipedia.org/wiki/Regular_expression) 및 [블 룸 필터](https://wikipedia.org/wiki/Bloom_filter)를 기준으로 데이터를 분류 합니다. 다음 목록에서는 Azure 부서의 범위 정의 시스템 분류의 형식, 패턴 및 키워드에 대해 설명 합니다.

각 분류 이름에는 MICROSOFT 접두사가 붙습니다.

## <a name="bloom-filter-classifications"></a>블 룸 필터 분류

## <a name="city-country-and-place"></a>도시, 국가 및 장소

도시, 국가 및 장소 필터는 데이터를 준비 하는 데 사용할 수 있는 최상의 데이터 집합을 사용 하 여 준비 되었습니다.

## <a name="person"></a>Person

Person 블 룸 필터는 아래 두 데이터 집합을 사용 하 여 준비 되었습니다.

- [2010 미국 인구 조사 Last Name (162-K 항목)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [모든 연도 1880-2019 (98-K 개 항목)을 사용 하는 인기 있는 아기 이름 (SSN)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 분류

## <a name="aba-routing"></a>ABA 라우팅

### <a name="format"></a>서식

서식이 지정 되거나 지정 되지 않은 패턴 일 수 있는 9 자리 숫자

### <a name="pattern"></a>무늬

설정한

- 0, 1, 2, 3, 6, 7 또는 8로 시작 하는 4 자리 숫자
- 하이픈
- 4 자리
- 하이픈
- 서식이 지정 되지 않은 숫자: 0, 1, 2, 3, 6, 7 또는 8로 시작 하는 연속 된 숫자 9 개

### <a name="keywords"></a>키워드

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>DNI (아르헨티나 국내 id) 번호

### <a name="format"></a>서식

마침표가 있거나 없는 8 자리

### <a name="pattern"></a>무늬

8 자리:

- 두 자리 숫자
- 선택적 기간
- 3 자리 숫자
- 선택적 기간
- 3 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>오스트레일리아 은행 계좌 번호

### <a name="format"></a>서식

뱅크 상태 분기 번호를 사용 하거나 사용 하지 않는 6 ~ 10 자리

### <a name="pattern"></a>무늬

계정 번호는 6 ~ 10 자리 수입니다.

오스트레일리아 은행 주 분기 번호:

- 3 자리 숫자
- 하이픈
- 3 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>오스트레일리아 운전 면허 번호

### <a name="format"></a>서식
9 자 및 숫자

### <a name="pattern"></a>무늬
9 자 및 숫자:

- 두 자리 또는 문자 (대/소문자 구분 안 함)
- 두 자리 숫자
- 5 자리 또는 문자 (대/소문자 구분 안 함)

또는

- 1 ~ 2 개의 선택적 문자 (대/소문자 구분 안 함)
- 4 ~ 9 자리

또는

- 9 자리 또는 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australia-medical-account-number"></a>오스트레일리아 의료 계정 번호

### <a name="format"></a>서식

10-11 자릿수

### <a name="pattern"></a>무늬

10-11 숫자:

- 첫 번째 숫자는 2-6 범위에 있습니다.
- 아홉 번째 숫자는 확인 숫자입니다.
- 열 번째 숫자는 문제 숫자입니다.
- 11 번째 숫자 (선택 사항)는 개별 숫자입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>오스트레일리아 여권 번호

### <a name="format"></a>서식

문자 뒤에 7 자리가 있습니다.

### <a name="pattern"></a>무늬

문자 (대/소문자 구분 안 함)와 일곱 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>\_Passport 키워드

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>키워드 \_ 오스트레일리아 \_ 여권 \_ 번호

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>오스트레일리아 세금 파일 번호

### <a name="format"></a>서식

8 ~ 9 자리

### <a name="pattern"></a>무늬

일반적으로 다음과 같이 공백을 사용 하 여 8 ~ 9 자리 숫자를 표시 합니다.

- 3 자리 숫자
- 선택적 공백
- 3 자리 숫자
- 선택적 공백
- 마지막 자릿수가 확인 숫자인 경우 2 ~ 3 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_tax_file_number"></a>키워드 \_ 오스트레일리아 \_ 세금 \_ 파일 \_ 번호

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>벨기에 국가 번호

### <a name="format"></a>서식

11 자리 및 선택적 구분 기호

### <a name="pattern"></a>무늬

11 자리 및 구분 기호:

- YY 형식의 6 자리 및 두 개의 선택적 마침표 생년월일에 대 한 MM.DD
- 점, 대시, 공백 등의 선택적 구분 기호
- 세 개의 순차적 숫자 (세임의 경우 홀수,은 여성 많으며의 경우에도)
- 점, 대시, 공백 등의 선택적 구분 기호
- 두 개의 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_belgium_national_number"></a>키워드 \_ 벨기에 \_ 국가 \_ 번호

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>브라질 CPF 번호

### <a name="format"></a>서식

확인 숫자가 포함 되 고 서식이 지정 되거나 지정 되지 않을 수 있는 11 자리 숫자

### <a name="pattern"></a>무늬

설정한

- 3 자리 숫자
- 마침표
- 3 자리 숫자
- 마침표
- 3 자리 숫자
- 하이픈
- 확인 숫자를 나타내는 두 자리 숫자

서식 없음:

- 마지막 두 자리가 확인 하는 11 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cpf"></a>\_브라질 \_ cpf 키워드

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>브라질 법률 엔터티 번호 (CNPJ)

### <a name="format"></a>서식

등록 번호, 분기 번호 및 확인 숫자와 구분 기호를 포함 하는 14 자리

### <a name="pattern"></a>무늬

14 자리 및 구분 기호:

- 두 자리 숫자
- 마침표
- 3 자리 숫자
- 마침표
- 3 자리 숫자 (처음 8 자리는 등록 번호)
- 슬래시
- 네 자리 분기 번호
- 하이픈
- 확인 숫자를 나타내는 두 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cnpj"></a>\_브라질 \_ cnpj 키워드

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>브라질 국제 식별 카드 (RG)

### <a name="format"></a>서식

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>무늬

:::no-loc text="Registro Geral (old format):":::

- 두 자리 숫자
- 마침표
- 3 자리 숫자
- 마침표
- 3 자리 숫자
- 하이픈
- 1 자리 숫자 (확인 숫자)

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10개의 자릿수
- 하이픈
- 1 자리 숫자 (확인 숫자)

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_rg"></a>\_브라질 \_ rg 키워드

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>캐나다 은행 계좌 번호

### <a name="format"></a>서식

7 또는 12 자리

### <a name="pattern"></a>무늬

캐나다 은행 계좌 번호는 7 자리 또는 12 자리입니다.

캐나다 은행 계좌 운송 번호는 다음과 같습니다.

- 5 자리
- 하이픈
- 3 자리 또는
- 0 &quot; 0&quot;
- 8 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_bank_account_number"></a>키워드 \_ 캐나다 \_ 은행 \_ 계좌 \_ 번호

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>캐나다 운전 면허 번호

### <a name="format"></a>서식

시 마다 다름

### <a name="pattern"></a>무늬

앨버타, 영국령 특별구, 매니토바, New 뉴브런즈윅, 뉴펀들랜드/래브라도, 노바 노바스코샤, Ontario, 프린스 Edward 섬, 퀘벡 및 서스캐처원를 다루는 다양 한 패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_province_name_drivers_license_name"></a>키워드 \_ [시 \_ 이름] \_ 드라이버 \_ 라이선스 \_ 이름

- 시 약어 (예: AB)
- 시/도 이름 (예: 앨버타

#### <a name="keyword_canada_drivers_license"></a>키워드 \_ 캐나다 \_ 드라이버 \_ 라이선스

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>캐나다 health service 번호

### <a name="format"></a>서식

10개의 자릿수

### <a name="pattern"></a>무늬

10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_health_service_number"></a>키워드 \_ 캐나다 \_ health \_ service \_ number

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>캐나다 여권 번호

### <a name="format"></a>서식

대문자 2 개와 6 자리 숫자

### <a name="pattern"></a>무늬

대문자 2 개와 6 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_passport_number"></a>키워드 \_ 캐나다 \_ 여권 \_ 번호

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>\_Passport 키워드

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>캐나다 PHIN (개인 상태 식별 번호)

### <a name="format"></a>서식

9 자리

### <a name="pattern"></a>무늬

9 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_phin"></a>\_캐나다 \_ phin 키워드

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>캐나다도 (시) 키워드 \_ \_

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>캐나다 사회 보험 번호

### <a name="format"></a>서식

하이픈 또는 공백을 선택적으로 사용 하는 9 자리

### <a name="pattern"></a>무늬

설정한

- 3 자리 숫자
- 하이픈 또는 공백
- 3 자리 숫자
- 하이픈 또는 공백
- 3 자리 숫자

형식이 지정 되지 않음: 아홉 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_sin"></a>키워드 \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>키워드 \_ sin \_ 공동 작업

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>칠레 id 카드 번호

### <a name="format"></a>서식

7 ~ 8 자리 및 확인 숫자 또는 문자의 구분 기호

### <a name="pattern"></a>무늬

7 ~ 8 자리 및 구분 기호:

- 1 ~ 2 자리
- 선택적 기간
- 3 자리 숫자
- 선택적 기간
- 3 자리 숫자
- 대시
- 확인 숫자인 1 자리 또는 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_chile_id_card"></a>키워드 \_ 칠레 \_ id \_ 카드

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>중국-상주 id 카드 (PRC) 번호

### <a name="format"></a>서식

18 자리

### <a name="pattern"></a>무늬

18 자리:

- 주소 코드 인 6 자리 숫자
- YYYYMMDD 형식의 8 자리 (출생 날짜)
- 3 자리 숫자 (주문 코드)
- 1 자리 숫자 (확인 숫자)

### <a name="keywords"></a>키워드

#### <a name="keyword_china_resident_id"></a>키워드 \_ 중국 \_ 상주 \_ id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>신용 카드 번호

### <a name="format"></a>서식

14-16 자리로, 형식 지정 또는 서식 지정 가능 (dddddddddddddddddddddddddddddddddddddddddddddddddddddd

### <a name="pattern"></a>무늬

전 세계 모든 주요 브랜드에서 카드를 검색 하는 복잡 하 고 강력한 패턴으로, MasterCard, 검색 카드, JCB, 미국 diner 카드를 포함 합니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_cc_verification"></a>키워드 \_ 참조 \_ 확인

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>키워드 \_ 참조 \_ 이름

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>크로아티아 드라이버의 라이선스 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 유형 에서만 사용할 수 있습니다.

### <a name="format"></a>서식

공백 및 구분 기호가 없는 8 자리

### <a name="pattern"></a>무늬

8 자리

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>키워드 \_ eu \_ 운전 \_ 면허 \_ 번호

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>키워드 \_ 크로아티아 \_ eu \_ 운전 \_ 면허 \_ 번호

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>크로아티아 id 카드 번호

이 중요 한 정보 형식 엔터티는 EU 국가 Id의 중요 한 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

9 자리

### <a name="pattern"></a>무늬

9 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_id_card"></a>키워드 \_ 크로아티아 \_ id \_ 카드

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>크로아티아 개인 정보 (있는 OIB) 번호

### <a name="format"></a>서식

11 자리

### <a name="pattern"></a>무늬

11 자리 숫자:

- 10개의 자릿수
- 마지막 숫자는 확인 숫자입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_oib_number"></a>\_크로아티아 \_ 있는 oib \_ number 키워드

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>덴마크 개인 식별 번호

### <a name="format"></a>서식

하이픈을 포함 하는 10 자리

### <a name="pattern"></a>무늬

10 자리:

- 출생 날짜인 DDMMYY 형식의 6 자리 숫자
- 하이픈
- 마지막 자릿수가 확인 숫자인 네 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_denmark_id"></a>키워드 \_ 덴마크 \_ id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>EU 직불 카드 번호

### <a name="format"></a>서식

16 자리

### <a name="pattern"></a>무늬

복합 패턴과 강력한 패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_eu_debit_card"></a>키워드 \_ eu \_ 직불 \_ 카드

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>키워드 \_ 카드 사용 \_ 약관 \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>키워드 \_ 카드 \_ 보안 \_ 용어 \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>키워드 \_ 카드 \_ 만료 \_ 조건 \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>EU 운전 면허 번호

이러한 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 유형입니다.

- 오스트리아
- 벨기에
- 불가리아
- 크로아티아
- 키프로스
- 체코어
- 덴마크
- 에스토니아
- 핀란드
- 프랑스
- 독일
- 그리스
- 헝가리
- 아일랜드
- 이탈리아
- 라트비아
- 리투아니아
- 룩셈부르크
- 몰타
- 네덜란드
- 폴란드
- 포르투갈
- 루마니아
- 슬로바키아
- 슬로베니아
- 스페인
- 스웨덴
- 영국의

## <a name="eu-national-identification-number"></a>EU 국가 식별 번호

이러한 엔터티는 EU 국가 Id의 중요 정보 유형입니다.

- 오스트리아
- 벨기에
- 불가리아
- 크로아티아
- 키프로스
- 체코어
- 덴마크
- 에스토니아
- 핀란드
- 프랑스
- 독일
- 그리스
- 헝가리
- 아일랜드
- 이탈리아
- 라트비아
- 리투아니아
- 룩셈부르크
- 몰타
- 네덜란드
- 폴란드
- 포르투갈
- 루마니아
- 슬로바키아
- 슬로베니아
- 스페인
- 영국의

## <a name="eu-passport-number"></a>EU 여권 번호

이러한 엔터티는 EU passport 번호 구분 정보 typeThese 이러한 엔터티는 EU passport number 번들의 엔터티입니다.

- 오스트리아
- 벨기에
- 불가리아
- 크로아티아
- 키프로스
- 체코어
- 덴마크
- 에스토니아
- 핀란드
- 프랑스
- 독일
- 그리스
- 헝가리
- 아일랜드
- 이탈리아
- 라트비아
- 리투아니아
- 룩셈부르크
- 몰타
- 네덜란드
- 폴란드
- 포르투갈
- 루마니아
- 슬로바키아
- 슬로베니아
- 스페인
- 스웨덴
- 영국의

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU 주민 등록 번호 또는 동등한 id

이러한 엔터티는 EU 주민 등록 번호 또는 동등한 id 구분 정보 유형입니다.

- 오스트리아
- 벨기에
- 크로아티아
- 체코어
- 덴마크
- 핀란드
- 프랑스
- 독일
- 그리스
- 헝가리
- 포르투갈
- 스페인
- 스웨덴

## <a name="eu-tax-identification-number"></a>EU 세금 식별 번호

이러한 엔터티는 EU 세금 식별 번호 중요 정보 유형입니다.

- 오스트리아
- 벨기에
- 불가리아
- 크로아티아
- 키프로스
- 체코어
- 덴마크
- 에스토니아
- 핀란드
- 프랑스
- 독일
- 그리스
- 헝가리
- 아일랜드
- 이탈리아
- 라트비아
- 리투아니아
- 룩셈부르크
- 몰타
- 네덜란드
- 폴란드
- 포르투갈
- 루마니아
- 슬로바키아
- 슬로베니아
- 스페인
- 스웨덴
- 영국의



## <a name="finland-national-id"></a>핀란드 국가 ID

### <a name="format"></a>서식

6 자리 + 세기와 세 자리 + 확인 숫자를 나타내는 문자

### <a name="pattern"></a>무늬

패턴은 다음을 모두 포함 해야 합니다.

- 생년월일 인 DDMMYY 형식의 6 자리 숫자
- 세기 표식 ('-', ' + ' 또는 ' a ')
- 3 자리 개인 식별 번호
- 확인 숫자인 숫자나 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>핀란드 여권 번호

이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식으로 사용할 수 있으며 독립 실행형 중요 한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

9 개의 문자와 숫자의 조합입니다.

### <a name="pattern"></a>무늬

9 자의 문자와 숫자의 조합입니다.

- 두 문자 (대/소문자 구분 안 함)
- 7 자리

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>키워드 \_ eu \_ passport \_ number \_ common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>키워드 \_ 핀란드 \_ 여권 \_ 번호

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>프랑스 드라이버의 라이선스 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 유형에 서 사용할 수 있으며 독립 실행형 중요 한 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

12자리

### <a name="pattern"></a>무늬

프랑스어 전화 번호와 같은 비슷한 패턴의 할인율을 확인 하는 12 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_french_drivers_license"></a>키워드 \_ 프랑스어 \_ 드라이버 \_ 라이선스

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>프랑스 국가 ID 카드 (CNI)

### <a name="format"></a>서식

12자리

### <a name="pattern"></a>무늬

12자리

### <a name="keywords"></a>키워드

#### <a name="keywords_france_eu_national_id_card"></a>키워드 \_ 프랑스 \_ eu \_ 국가 \_ id \_ 카드

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>프랑스 여권 번호

이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식으로 사용할 수 있으며 독립 실행형 중요 한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

아홉 자리 숫자 및 문자

### <a name="pattern"></a>무늬

9 자리 숫자 및 문자:

- 두 자리 숫자
- 두 문자 (대/소문자 구분 안 함)
- 5 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>\_Passport 키워드

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>INSEE (프랑스 주민 등록 번호) 또는 동등한 id

이 중요 한 정보 형식 엔터티는 EU 사회 보장 번호 및 이와 동등한 ID 구분 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

15개의 자릿수

### <a name="pattern"></a>무늬

다음 두 패턴 중 하 나와 일치 해야 합니다.

- 13 자리 뒤에 공백이 오고 그 뒤에 두 개의 숫자가 오고
- 15 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_fr_insee"></a>키워드 \_ fr \_ insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>독일 운전 면허 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

11 자리 및 문자 조합

### <a name="pattern"></a>무늬

11 자리 숫자 및 문자 (대/소문자 구분 안 함):

- 숫자 또는 문자
- 두 자리 숫자
- 6 자리 숫자 또는 문자
- 숫자
- 숫자 또는 문자

### <a name="keywords"></a>키워드

#### <a name="keyword_german_drivers_license_number"></a>키워드 \_ 독일어 \_ 드라이버 \_ 라이선스 \_ 번호

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>독일 id 카드 번호

### <a name="format"></a>서식

1 월 1 일 이후 2010:9 자 및 숫자

1987 년 10 월 31 일까 지 2010:10 자리

### <a name="pattern"></a>무늬

1 월 2010 일 이후:

- 한 문자 (대/소문자 구분 안 함)
- 8 자리

1 월 1987 2010 년 10 월 31 일까 지:

- 10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keyword_germany_id_card"></a>키워드 \_ 독일 \_ id \_ 카드

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>독일 여권 번호

이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

10 자리 또는 문자

### <a name="pattern"></a>무늬

패턴은 다음을 모두 포함 해야 합니다.

- 첫 번째 문자는이 집합의 숫자 또는 문자입니다 (C, F, G, H, J, K).
- 3 자리 숫자
- 이 집합에서 5 자리 숫자 또는 문자 (C,-H, J-N, P, R, T, V-Z)
- 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_german_passport"></a>키워드 \_ 독일어 \_ passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>그리스 국가 ID 카드

### <a name="format"></a>서식

7-8 문자와 숫자의 조합에 대시 추가

### <a name="pattern"></a>무늬

7 개 문자 및 숫자 (이전 형식):

- 한 문자 (그리스어 알파벳의 모든 문자)
- 대시
- 6 자리

8 자 및 숫자 (새 형식):

- 그리스어와 라틴어 알파벳 (ABEZHIKMNOPTYX) 모두에서 대문자 문자를 두 개 포함 하는 문자
- 대시
- 6 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_greece_id_card"></a>키워드 \_ 그리스 \_ id \_ 카드

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>HKID (홍콩 id card) 번호

### <a name="format"></a>서식

8-9 문자와 숫자의 조합 및 마지막 문자에 대 한 선택적 괄호

### <a name="pattern"></a>무늬

8-9 문자 조합:

- 1-2 문자 (대/소문자 구분 안 함)
- 6 자리
- 확인 숫자 이며 선택적으로 괄호 안에 포함 되는 마지막 문자 (임의의 숫자 또는 문자 A)입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_hong_kong_id_card"></a>Keyword \_ 홍콩 \_ \_ id \_ 카드

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP 주소

### <a name="format"></a>서식

#### <a name="ipv4"></a>IPv4

형식이 지정 된 (기간) 및 포맷 되지 않은 (기간 없음) 버전의 IPv4 주소에 대 한 계정을 지정 하는 복합 패턴

#### <a name="ipv6"></a>Ipv6)

형식이 지정 된 IPv6 번호 (콜론 포함)를 계정으로 지정 하는 복합 패턴

### <a name="pattern"></a>무늬

### <a name="keywords"></a>키워드

#### <a name="keyword_ipaddress"></a>키워드 \_ ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>인도 (영구 계정 번호)

### <a name="format"></a>서식

10 개의 문자 또는 숫자

### <a name="pattern"></a>무늬

10 자 또는 숫자:

- 세 문자 (대/소문자 구분 안 함)
- C, P, H, F, A, T, B, L, J, G의 문자 (대/소문자 구분 안 함)
- 문자
- 4자리 숫자
- 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_india_permanent_account_number"></a>키워드 \_ 인도 \_ 영구 \_ 계정 \_ 번호

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>인도 Aadhaar (고유 id) 번호

### <a name="format"></a>서식

선택적 공백이 나 대시를 포함 하는 12 자리

### <a name="pattern"></a>무늬

12 자리:

- 0 또는 1이 아닌 숫자입니다.
- 3 자리 숫자
- 선택적 공백 또는 대시
- 4자리 숫자
- 선택적 공백 또는 대시
- 확인 숫자인 최종 숫자입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_india_aadhar"></a>\_인도 \_ aadhar 키워드

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>인도네시아 id 카드 (KTP) 번호

### <a name="format"></a>서식

선택적 마침표를 포함 하는 16 자리

### <a name="pattern"></a>무늬

16 자리:

- 두 자리도 코드
- 마침표 (옵션)
- 두 자리 regency 또는 도시 코드
- 2 자리 subdistrict 코드
- 마침표 (옵션)
- 출생 날짜인 DDMMYY 형식의 6 자리 숫자
- 마침표 (옵션)
- 4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_indonesia_id_card"></a>키워드 \_ 인도네시아 \_ id \_ 카드

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN (국제 은행 계좌 번호)

### <a name="format"></a>서식

국가 코드 (두 문자) + 확인 숫자 (두 자릿수)와 :::no-loc text="bban"::: 숫자 (최대 30 자)

### <a name="pattern"></a>무늬

패턴은 다음을 모두 포함 해야 합니다.

- 2 자리 국가 코드
- 두 확인 숫자 (선택적인 공백)
- 1-7 개의 문자 또는 숫자로 이루어진 그룹 (공백으로 구분 될 수 있음)
- 1-3 문자 또는 숫자

각 국가의 형식이 약간 다릅니다. IBAN 중요 한 정보 형식은 다음 60 국가를 포함 합니다.

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>키워드

없음

## <a name="ireland-personal-public-service-pps-number"></a>아일랜드 PPS (personal public service) 번호

### <a name="format"></a>서식

이전 형식 (12 월 31 일까 지 2012까지):

- 7 자리 숫자 뒤 1-2 문자

새 형식 (1 월 2013 일 이후):

- 뒤에 두 개의 문자가 오는 7 자리

### <a name="pattern"></a>무늬

이전 형식 (12 월 31 일까 지 2012까지):

- 7 자리
- 1 ~ 2 개 문자 (대/소문자 구분 안 함)

새 형식 (1 월 2013 일 이후):

- 7 자리
- 영문자 확인 문자인 문자 (대/소문자 구분 안 함)
- A-I 또는 W 범위의 선택적 문자 &quot;&quot;

### <a name="keywords"></a>키워드

#### <a name="keywords_ireland_eu_national_id_card"></a>키워드 \_ 아일랜드 \_ eu \_ 국가 \_ id \_ 카드

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>이스라엘 은행 계좌 번호

### <a name="format"></a>서식

13 자리

### <a name="pattern"></a>무늬

설정한

- 두 자리 숫자
- 대시
- 3 자리 숫자
- 대시
- 8 자리

서식 없음:

- 13 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_bank_account_number"></a>키워드 \_ 이스라엘 \_ 은행 \_ 계좌 \_ 번호

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>이스라엘 국가 식별 번호

### <a name="format"></a>서식

9 자리

### <a name="pattern"></a>무늬

9 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_national_id"></a>키워드 \_ 이스라엘 \_ 국가 \_ ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>이탈리아 드라이버의 라이선스 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

10 개의 문자와 숫자의 조합입니다.

### <a name="pattern"></a>무늬

10 개의 문자와 숫자의 조합입니다.

- 한 문자 (대/소문자 구분 안 함)
- 문자 &quot; A &quot; 또는 &quot; V &quot; (대/소문자 구분 안 함)
- 7 자리
- 한 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_italy_drivers_license_number"></a>키워드 \_ 이탈리아 \_ 드라이버 \_ 라이선스 \_ 번호

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>일본 은행 계좌 번호

### <a name="format"></a>서식

7 자리 또는 8 자리 숫자

### <a name="pattern"></a>무늬

은행 계좌 번호:

- 7 자리 또는 8 자리 숫자
- 은행 계좌 분기 코드:
- 4 자리
- 공백 또는 대시 (선택 사항)
- 3 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_bank_account"></a>키워드 \_ jp \_ 은행 \_ 계정

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>키워드 \_ jp \_ 은행 \_ 분기 \_ 코드

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>일본 드라이버의 라이선스 번호

### <a name="format"></a>서식

12자리

### <a name="pattern"></a>무늬

12 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_drivers_license_number"></a>키워드 \_ jp \_ 드라이버 \_ 라이선스 \_ 번호

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>일본 여권 번호

### <a name="format"></a>서식

두 문자 다음에 7 자리 숫자

### <a name="pattern"></a>무늬

두 문자 (대/소문자 구분 안 함)와 일곱 자리 숫자

#### <a name="keyword_jp_passport"></a>키워드 \_ jp \_ passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>일본 거주지 카드 번호

### <a name="format"></a>서식

12 자 및 숫자

### <a name="pattern"></a>무늬

12 자 및 숫자:

- 두 문자 (대/소문자 구분 안 함)
- 8 자리
- 두 문자 (대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_residence_card_number"></a>키워드 \_ jp \_ 거주지 \_ 카드 \_ 번호

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>일본 상주 등록 번호

### <a name="format"></a>서식

11 자리

### <a name="pattern"></a>무늬

11 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_resident_registration_number"></a>키워드 \_ jp \_ 상주 \_ 등록 \_ 번호

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>일본 사회 보험 번호 (SIN)

### <a name="format"></a>서식

7-12 자릿수

### <a name="pattern"></a>무늬

7-12 숫자:

- 4 자리
- 하이픈 (선택 사항)
- 6 자리 또는
- 7-12 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_sin"></a>키워드 \_ jp \_ 사인

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>말레이시아 식별 카드 번호

### <a name="format"></a>서식

선택적 하이픈을 포함 하는 12 자리

### <a name="pattern"></a>무늬

12 자리:

- YYMMDD 형식의 6 자리 숫자 (생년월일)
- 대시 (선택 사항)
- 두 문자로 된 생년월일 코드
- 대시 (선택 사항)
- 임의의 숫자 3 개
- 1 자리 성별 코드

### <a name="keywords"></a>키워드

#### <a name="keyword_malaysia_id_card_number"></a>키워드 \_ 말레이시아 \_ id \_ 카드 \_ 번호

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>네덜란드 시민 서비스 (BSN) 번호

### <a name="format"></a>서식

8-9 자리 (선택적 공백 포함)

### <a name="pattern"></a>무늬

8-9 자리 숫자:

- 3 자리 숫자
- 공백 (옵션)
- 3 자리 숫자
- 공백 (옵션)
- 2-3 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_netherlands_eu_national_id_card"></a>키워드 \_ 네덜란드령 \_ eu \_ 국가 \_ id \_ 카드

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>새 뉴질랜드 경제 산업성 상태 번호

### <a name="format"></a>서식

3 개의 문자, 공백 (옵션) 및 4 자리

### <a name="pattern"></a>무늬

- ' I ' 및 ' O '를 제외한 세 문자 (대/소문자 구분 안 함)
- 공백 (옵션)
- 4 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_nz_terms"></a>키워드 \_ nz \_ 용어

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>노르웨이 식별 번호

### <a name="format"></a>서식

11 자리

### <a name="pattern"></a>무늬

11 자리 숫자:

- 출생 날짜인 DDMMYY 형식의 6 자리 숫자
- 3 자리 개별 숫자
- 두 개의 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_norway_id_number"></a>키워드 \_ 노르웨이 \_ id \_ 번호

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>필리핀 통합 다목적 id 번호

### <a name="format"></a>서식

하이픈으로 구분 된 12 자리

### <a name="pattern"></a>무늬

12 자리:

- 4 자리
- 하이픈
- 7 자리
- 하이픈
- 한 자릿수

### <a name="keywords"></a>키워드

#### <a name="keyword_philippines_id"></a>키워드 \_ 필리핀 \_ id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>폴란드 id 카드

### <a name="format"></a>서식

3 자 및 6 자리

### <a name="pattern"></a>무늬

세 문자 (대/소문자 구분 안 함)와 6 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_poland_national_id_passport_number"></a>키워드 \_ 폴란드 \_ 국가 \_ id \_ 여권 \_ 번호

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>폴란드 국가 ID (PESEL)

### <a name="format"></a>서식

11 자리

### <a name="pattern"></a>무늬

- YYMMDD 형식의 생년월일을 나타내는 6 자리 숫자
- 4 자리
- 1 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_pesel_identification_number"></a>키워드 \_ pesel \_ id \_ 번호

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>폴란드 여권 번호

이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

두 문자 및 일곱 자리

### <a name="pattern"></a>무늬

두 문자 (대/소문자 구분 안 함)와 일곱 자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_poland_national_id_passport_number"></a>키워드 \_ 폴란드 \_ 국가 \_ id \_ 여권 \_ 번호

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>포르투갈 시민 카드 번호

### <a name="format"></a>서식

8 자리

### <a name="pattern"></a>무늬

8 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_portugal_citizen_card"></a>키워드 \_ 포르투갈 \_ 시민 \_ 카드

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>포르투갈 드라이버의 라이선스 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 유형 에서만 사용할 수 있습니다.

### <a name="format"></a>서식

두 가지 패턴-특수 문자를 포함 하는 5-8 자리 다음에 오는 두 개의 문자

### <a name="pattern"></a>무늬

패턴 1: 특수 문자를 사용 하 여 두 문자 다음에 5/6을 입력 합니다.

- 두 문자 (대/소문자 구분 안 함)
- 하이픈
- 5 개 또는 6 자리 숫자
- 공백
- 한 자릿수

패턴 2: 특수 문자를 사용 하 여 한 문자 다음에 6/8 숫자가 옵니다.

- 한 문자 (대/소문자 구분 안 함)
- 하이픈
- 6 자리 또는 8 자리 숫자
- 공백
- 한 자릿수

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>키워드 \_ eu \_ 운전 \_ 면허 \_ 번호

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>키워드 \_ 포르투갈 \_ eu \_ 운전 \_ 면허 \_ 번호

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>사우디아라비아 국가 ID

### <a name="format"></a>서식

10개의 자릿수

### <a name="pattern"></a>무늬

10 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_saudi_arabia_national_id"></a>- \_ 사우디아라비아 \_ \_ 국가 \_ id 키워드

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>싱가포르 NRIC (국가별 등록 id 카드) 번호

### <a name="format"></a>서식

9 자 및 숫자

### <a name="pattern"></a>무늬

- 9 자 및 숫자:
- 문자 &quot; F &quot; , &quot; G &quot; , &quot; S &quot; 또는 &quot; T &quot; (대/소문자 구분 안 함)
- 7 자리
- 영문자 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_singapore_nric"></a>\_싱가포르 \_ nric 키워드

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>남아프리카 공화국 식별 번호

### <a name="format"></a>서식

공백을 포함할 수 있는 13 자리

### <a name="pattern"></a>무늬

13 자리:

- YYMMDD 형식의 6 자리 숫자 (생년월일)
- 4 자리
- 단일 자리 참여 표시기
- 숫자 &quot; 8 &quot; 또는 &quot; 9&quot;
- 하나는 체크섬을 나타내는 숫자입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_south_africa_identification_number"></a>키워드 \_ 남아프리카 \_ 공화국 \_ 식별 \_ 번호

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>남부 대한민국 등록 번호

### <a name="format"></a>서식

하이픈을 포함 하는 13 자리

### <a name="pattern"></a>무늬

13 자리:

- YYMMDD 형식의 6 자리 숫자 (생년월일)
- 하이픈
- 세기와 성별에 의해 결정 되는 1 자리 숫자
- 네 자리 생일 지역 코드
- 이전 숫자가 동일한 사용자를 구분 하는 데 사용 되는 한 자리
- 확인 숫자입니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_south_korea_resident_number"></a>키워드 \_ 남부 \_ 대한민국 \_ \_ 번호

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>스페인 사회 보장 번호 (SSN)

이 중요 한 정보 형식 엔터티는 EU 주민 등록 번호 또는 이와 동등한 ID 구분 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

11-12 자릿수

### <a name="pattern"></a>무늬

11-12 숫자:

- 두 자리 숫자
- 슬래시 (선택 사항)
- 7 ~ 8 자리
- 슬래시 (선택 사항)
- 두 자리 숫자

### <a name="keywords"></a>키워드

없음

## <a name="sweden-national-id"></a>스웨덴 국가 ID

### <a name="format"></a>서식

10 또는 12 자리 및 선택적 구분 기호

### <a name="pattern"></a>무늬

10 또는 12 자리 및 선택적 구분 기호:

- 두 자리 숫자 (옵션)
- 날짜 형식의 6 자리 숫자 YYMMDD
- 또는의 구분 기호 &quot; - &quot; &quot; + &quot; (선택 사항)
- 4 자리

### <a name="keywords"></a>키워드

#### <a name="keywords_swedish_national_identifier"></a>키워드 \_ 스웨덴어 \_ 국가 \_ 식별자

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>스웨덴 여권 번호

이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

8 자리

### <a name="pattern"></a>무늬

8 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_sweden_passport"></a>\_스웨덴어 \_ passport 키워드

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>\_Passport 키워드

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT 코드

### <a name="format"></a>서식

4 자 뒤에 5-31 문자 또는 숫자

### <a name="pattern"></a>무늬

다음 4 개의 문자로 이루어진 문자 또는 숫자 5-31:

- 4 자 은행 코드 (대/소문자 구분 안 함)
- 선택적 공백
- 4-28 문자 또는 숫자 (BBAN 기본 은행 계좌 번호)
- 선택적 공백
- 1 ~ 3 자의 문자 또는 숫자 (BBAN의 나머지)

### <a name="keywords"></a>키워드

#### <a name="keyword_swift"></a>키워드 \_ swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>대만 국가 식별 번호

### <a name="format"></a>서식

한 문자 (영어)와 9 자리 숫자

### <a name="pattern"></a>무늬

한 문자 (영어)와 9 자리 숫자:

- 한 문자 (영어로는 대/소문자 구분 안 함)
- 숫자 &quot; 1 &quot; 또는 &quot; 2&quot;
- 8 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_national_id"></a>키워드 \_ 대만 \_ 국가 \_ id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>대만 passport 번호

### <a name="format"></a>서식

- 생체 인식 passport 번호: 9 자리
- 비 생체 인식 passport 번호: 9 자리

### <a name="pattern"></a>무늬

생체 인식 passport 번호:

- 문자 &quot; 3&quot;
- 8 자리

비 생체 인식 passport 번호:

- 9 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_passport"></a>키워드 \_ 대만 \_ passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>대만-상주 인증서 (ARC/TARC) 번호

### <a name="format"></a>서식

10 자 및 숫자

### <a name="pattern"></a>무늬

10 자 및 숫자:

- 두 문자 (대/소문자 구분 안 함)
- 8 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_resident_certificate"></a>키워드 \_ 대만 \_ 상주 \_ 인증서

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>영국의 드라이버의 라이선스 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

지정 된 형식의 18 자 및 숫자 조합입니다.

### <a name="pattern"></a>무늬

18 자 및 숫자:

- 문자 대신 5 개 문자 (대/소문자 구분 안 함) 또는 숫자 &quot; 9 &quot;
- 한 자릿수
- MMDDY 날짜 형식에서 5 자리 숫자 (드라이버가 여성 인 경우 7 번째 문자는 50로 증가 하 고, 즉 51은 01 ~ 12 대신 62로 증가)
- 문자 대신 두 문자 (대/소문자 구분 안 함) 또는 숫자 &quot; 9 &quot;
- 5 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_drivers_license"></a>키워드 \_ 영국 \_ 드라이버 \_ 라이선스

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>영국의 사용한 선거 지도로 roll 번호

### <a name="format"></a>서식

두 문자 다음에 1-4 숫자가 옴

### <a name="pattern"></a>무늬

두 문자 (대/소문자 구분 안 함)와 1-4 번호

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_electoral"></a>키워드 \_ uk \_ 사용한 선거 지도로

- council 추천
- 추천 양식
- 사용한 선거 지도로 레지스터
- 사용한 선거 지도로 roll

## <a name="uk-national-health-service-number"></a>영국의 국가별 상태 관리 서비스 번호

### <a name="format"></a>서식

10-17 숫자가 공백으로 구분 됩니다.

### <a name="pattern"></a>무늬

10-17 숫자:

- 3 자리 또는 10 자리
- 공백
- 3 자리 숫자
- 공백
- 4 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nhs_number"></a>키워드 \_ 영국 \_ nhs \_ number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>키워드 \_ 영국 \_ nhs \_ number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>키워드 \_ uk \_ nhs \_ number \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>영국의 국가 보험 번호 (NINO)

이 중요 한 정보 형식 엔터티는 EU 국가 Id의 중요 한 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

공백 또는 대시로 구분 된 7 자 또는 9 자

### <a name="pattern"></a>무늬

가능한 두 가지 패턴은 다음과 같습니다.

- 두 문자 (올바른 NINOs는이 접두사의 특정 문자만 사용 하 여이 패턴의 유효성을 검사 하며 대/소문자 구분 안 함)
- 6 자리
- ' A ', ' B ', ' C ' 또는 ' d ' (접두사와 같이 접미사는 특정 문자만 사용할 수 있음)는 대/소문자를 구분 하지 않습니다.

또는

- 두 문자
- 공백 또는 대시
- 두 자리 숫자
- 공백 또는 대시
- 두 자리 숫자
- 공백 또는 대시
- 두 자리 숫자
- 공백 또는 대시
- ' A ', ' B ', ' C ' 또는 ' d ' 중 하나입니다.


### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nino"></a>키워드 \_ uk \_ nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>영국의 고유 납세자 참조 번호

이 중요 한 정보 형식은 다음에만 사용할 수 있습니다.

- 데이터 손실 방지 정책
- 통신 준수 정책
- 정보 관리
- 레코드 관리
- Microsoft cloud app security

### <a name="format"></a>서식

공백 및 구분 기호가 없는 10 자리

### <a name="pattern"></a>무늬

10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keywords_uk_eu_tax_file_number"></a>키워드 \_ 영국 \_ 유럽 \_ 세금 \_ 파일 \_ 번호

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>미국 은행 계좌 번호

### <a name="format"></a>서식

6-17 자릿수

### <a name="pattern"></a>무늬

6-17 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_usa_bank_account"></a>키워드 \_ usa \_ 은행 \_ 계좌

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>미국 운전 면허 번호

### <a name="format"></a>서식

상태에 따라 달라 집니다.

### <a name="pattern"></a>무늬

상태에 따라 다릅니다 (예: 뉴욕).

- ddd와 같이 서식이 지정 된 9 자리 숫자가 일치 합니다.
- ddddddddd와 같은 9 자리 숫자가 일치 하지 않습니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_us_drivers_license_abbreviations"></a>키워드 \_ us \_ 드라이버 \_ 라이선스 \_ 약어

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>키워드 \_ us \_ 드라이버 \_ 라이선스

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>키워드 \_ [상태 \_ 이름] \_ 드라이버 \_ 라이선스 \_ 이름

- 상태 약어 (예: 등 &quot; &quot; )
- 상태 이름 (예: &quot; 뉴욕 &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>미국 스냅숏입니다 (개별 납세자 식별 번호)

### <a name="format"></a>서식

9 자리 숫자로 시작 하 &quot; &quot; 고 4 자리 숫자로 7 또는 8을 포함 하는 9 자리 숫자 ( &quot; &quot; &quot; 필요에 &quot; 따라 공백이 나 대시로 서식 지정)

### <a name="pattern"></a>무늬

설정한

- 숫자 &quot; 9&quot;
- 두 자리 숫자
- 공백 또는 대시
- &quot;7 &quot; 또는 &quot; 8&quot;
- 숫자
- 공백 또는 대시
- 4 자리

않았거나

- 숫자 &quot; 9&quot;
- 두 자리 숫자
- &quot;7 &quot; 또는 &quot; 8&quot;
- 5 자리

### <a name="keywords"></a>키워드

#### <a name="keyword_itin"></a>키워드 \_ 스냅숏입니다

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>미국 사회 보장 번호 (SSN)

### <a name="format"></a>서식

서식이 지정 되거나 지정 되지 않은 패턴 일 수 있는 9 자리 숫자

>[!Note]
> 중부-2011 이전에 실행 된 경우 SSN에는 숫자의 특정 부분이 유효 하 게 유지 되어야 하지만 체크섬이 없는 강력한 서식 지정이 있습니다.
>

### <a name="pattern"></a>무늬

네 가지 함수는 다음과 같은 네 가지 패턴에서 SSNs를 찾습니다.

- Func \_ ssn은 대시 또는 공백 (ddd-dd-DDDD 또는 ddd dd dddd)으로 서식이 지정 된 2011 이전의 강력한 형식을 사용 하 여 SSNs를 찾습니다.
- Func \_ 형식이 \_ 지정 되지 않은 ssn은 9 개의 연속 된 숫자 (ddddddddd)로 서식이 지정 되지 않은 미리 2011 강력한 서식의 ssns를 찾습니다.
- Func \_ 임의 \_ 형식 \_ ssn은 대시 또는 공백으로 포맷 된 2011 ssns (ddd-DD-dddd 또는 ddd dd dddd)를 찾습니다.
- Func \_ 임의로 \_ 형식이 지정 \_ 되지 않은 ssn은 9 개의 연속 자릿수로 형식이 지정 되지 않은 2011 ssns (ddddddddd)를 찾습니다.

### <a name="keywords"></a>키워드

#### <a name="keyword_ssn"></a>키워드 \_ ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>미국/영국 여권 번호

영국 passport의 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식으로 사용할 수 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

9 자리

### <a name="pattern"></a>무늬

9 개의 연속 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>\_Passport 키워드

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
