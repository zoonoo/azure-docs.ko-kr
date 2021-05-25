---
title: 지원되는 분류 목록
description: 이 페이지에는 Azure Purview에서 지원되는 시스템 분류가 나열되어 있습니다.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: e1d3d495d958465e966701aa7ce91bc2706b48e0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219589"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure Purview에서 지원되는 분류

이 문서에는 Azure Purview(미리 보기)에서 지원되고 정의된 시스템 분류가 나열되어 있습니다.


- **고유 일치 임계값**: 스캐너가 데이터 패턴을 실행하기 전에 열에서 찾아야 하는 총 개별 데이터 값 수입니다. 고유 일치 임계값은 패턴 일치와 무관하나 패턴 일치를 위한 필수 구성 요소입니다. 시스템 분류 규칙에 따라 각 열에는 분류를 위해 최소 8개의 고유 값이 있어야 합니다. 스캐너가 정확히 분류할 수 있을 만큼 충분한 데이터가 열에 포함되었는지 확인하기 위해, 시스템에서 이 값이 필요합니다. 예를 들어 값 1만 포함하는 여러 행이 있는 열은 분류되지 않습니다. 한 행에 값이 있고 나머지 행은 null 값인 열도 분류되지 않습니다. 여러 패턴을 지정할 경우 이 값이 각각의 패턴에 적용됩니다.

- **최소 일치 임계값**: 분류를 적용하기 위해 스캐너로 검색되어야 하는 열에서 일치하는 데이터 값의 최소 백분율입니다. 시스템 분류 값은 60%로 설정됩니다.


## <a name="defined-system-classifications"></a>정의된 시스템 분류

Azure Purview는 [RegEx](https://wikipedia.org/wiki/Regular_expression) 및 [블룸 필터](https://wikipedia.org/wiki/Bloom_filter)로 데이터를 분류합니다. 다음 목록에서는 Azure Purview 정의된 시스템 분류의 형식, 패턴 및 키워드에 대해 설명합니다.

각 분류 이름에는 MICROSOFT라는 접두사가 붙습니다.

## <a name="bloom-filter-classifications"></a>블룸 필터 분류

## <a name="city-country-and-place"></a>도시, 국가 및 장소

도시, 국가 및 장소 필터는 데이터 준비에 사용할 수 있는 최상의 데이터 세트를 사용하여 준비되었습니다.

## <a name="person"></a>사람

사람 블룸 필터는 아래 두 가지 데이터 세트를 사용하여 준비되었습니다.

- [성에 대한 2010 미국 인구 조사(162-K 항목)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [인기있는 아기 이름(SSN), 1880-2019년 모두 사용(98-K 항목)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 분류

## <a name="aba-routing"></a>ABA 라우팅

### <a name="format"></a>형식

9자리 숫자, 형식이 있거나 없는 패턴일 수 있음

### <a name="pattern"></a>패턴

형식 있음:

- 0, 1, 2, 3, 6, 7, 8로 시작하는 4자리 숫자
- 하이픈
- 4자리 숫자
- 하이픈
- 1자리 숫자

형식 없음: 0, 1, 2, 3, 6, 7 또는 8로 시작하는 연속된 숫자 9개

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

## <a name="argentina-national-identity-dni-number"></a>아르헨티나 신분증(DNI) 번호

### <a name="format"></a>형식

마침표가 있거나 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자:

- 2자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 점(선택 사항)
- 3자리 숫자

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

### <a name="format"></a>형식

BSB(Bank State Branch) 번호가 있거나 없는 6~10자리 숫자

### <a name="pattern"></a>패턴

계좌 번호는 6~10자리입니다.

오스트레일리아 BSB(Bank State Branch) 번호:

- 3자리 숫자
- 하이픈
- 3자리 숫자

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

### <a name="format"></a>형식
9개의 문자와 숫자

### <a name="pattern"></a>패턴
9개의 문자와 숫자:

- 2자리 숫자 또는 문자(대/소문자 구분 안 함)
- 2자리 숫자
- 5자리 숫자 또는 문자(대/소문자 구분 안 함)

또는

- 1~2개의 선택적 문자(대/소문자 구분 안 함)
- 4~9자리 숫자

또는

- 9자리 숫자 또는 문자(대/소문자 구분 안 함)

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

## <a name="australian-medicare-number"></a>오스트레일리아 건강 보험 번호

### <a name="format"></a>형식

10-11자리 숫자

### <a name="pattern"></a>패턴

10-11자리 숫자:

- 1번째 숫자의 범위는 2-6
- 9번째 숫자는 확인 숫자
- 10번째 숫자는 발행 숫자
- 11번째 숫자(선택 사항)는 개별 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

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

### <a name="format"></a>형식

문자 뒤에 7자리 숫자가 있음

### <a name="pattern"></a>패턴

문자(대/소문자 구분 안 함) 뒤에 7자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>Keyword\_passport

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

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

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

### <a name="format"></a>형식

8~9자리 숫자

### <a name="pattern"></a>패턴

공백과 함께 표시되는 8~9자리 숫자, 일반적으로 다음과 같이 표시됨:

- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2~3자리 숫자, 마지막 숫자는 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

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

## <a name="belgium-national-number"></a>벨기에 신원 번호

### <a name="format"></a>형식

11자리 숫자와 선택적 구분 기호

### <a name="pattern"></a>패턴

11자리 숫자와 구분 기호:

- 6자리 숫자와 선택적 마침표 2개, YY.MM.DD 형식의 생년월일
- 점, 대시, 공백 등의 선택적 구분 기호
- 연속 3자리 숫자(남성은 홀수, 여성은 짝수)
- 점, 대시, 공백 등의 선택적 구분 기호
- 2자리 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number

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

### <a name="format"></a>형식

11자리 숫자, 확인 숫자 포함, 형식이 있거나 없을 수 있음

### <a name="pattern"></a>패턴

형식 있음:

- 3자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자
- 하이픈
- 2자리 숫자, 확인 숫자임

서식 없음:

- 11자리 숫자, 마지막 2자리는 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf

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

## <a name="brazil-legal-entity-number-cnpj"></a>브라질 법인 번호(CNPJ)

### <a name="format"></a>형식

14자리 숫자, 등록 번호, 지점 번호 및 확인 번호와 구분 기호 포함

### <a name="pattern"></a>패턴

14자리 숫자 및 구분 기호:

- 2자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자(처음 8자리는 등록 번호)
- 슬래시(/)
- 4자리 분기 번호
- 하이픈
- 2자리 숫자, 확인 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj

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

## <a name="brazil-national-identification-card-rg"></a>브라질 국가 신분증(RG)

### <a name="format"></a>형식

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>패턴

:::no-loc text="Registro Geral (old format):":::

- 2자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자
- 하이픈
- 1자리 숫자, 확인 숫자임

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10개의 자릿수
- 하이픈
- 1자리 숫자, 확인 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg

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

### <a name="format"></a>형식

7 또는 12자리 숫자

### <a name="pattern"></a>패턴

캐나다 은행 계좌 번호는 7자리 또는 12자리입니다.

캐나다 은행 계좌 송금 코드 번호는 다음과 같습니다.

- 5자리 숫자
- 하이픈
- 3자리 숫자 또는
- &quot;0&quot;
- 8자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number

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

### <a name="format"></a>형식

지방 마다 다름

### <a name="pattern"></a>패턴

앨버타, 브리티시 컬럼비아, 매니토바, 뉴브런즈윅, 뉴펀들랜드/래브라도, 노바스코샤, 온타리오, 프린스에드워드섬, 퀘벡 및 서스캐처원을 포함하는 다양한 패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[province\_name]\_drivers\_license\_name

- 지방 약어(예: AB)
- 지방 이름(예: 앨버타)

#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

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

## <a name="canada-health-service-number"></a>캐나다 의료 서비스 번호

### <a name="format"></a>형식

10개의 자릿수

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

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

### <a name="format"></a>형식

대문자 2개 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

대문자 2개 뒤에 6자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

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

#### <a name="keyword_passport"></a>Keyword\_passport

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

## <a name="canada-personal-health-identification-number-phin"></a>캐나다 PHIN(개인 건강 식별 번호)

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

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

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

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

### <a name="format"></a>형식

하이픈 또는 공백을 선택적으로 사용하는 9 자리 숫자

### <a name="pattern"></a>패턴

형식 있음:

- 3자리 숫자
- 하이픈 또는 공백
- 3자리 숫자
- 하이픈 또는 공백
- 3자리 숫자

형식 없음: 9자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_sin"></a>Keyword\_sin

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

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

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

## <a name="chile-identity-card-number"></a>칠레 신분증 번호

### <a name="format"></a>형식

7~8자리 숫자와 구분 기호 확인 숫자 또는 문자

### <a name="pattern"></a>패턴

7~8자리 숫자와 구분 기호:

- 1~2자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 대시
- 1자리 숫자 또는 문자(대/소문자 구분 안 함), 확인 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

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

## <a name="china-resident-identity-card-prc-number"></a>중국 거주자 신분증(PRC) 번호

### <a name="format"></a>형식

18자리 숫자

### <a name="pattern"></a>패턴

18자리 숫자:

- 6자리 숫자, 주소 코드임
- 8자리 숫자(YYYYMMDD 형식의 생년월일)
- 3자리 숫자, 주문 코드
- 1자리 숫자, 확인 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

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

### <a name="format"></a>형식

14~16자리 숫자, 형식이 있거나 없을 수(dddddddddddddddd)있으며 Luhn 테스트를 통과해야 함

### <a name="pattern"></a>패턴

전 세계 모든 주요 브랜드의 카드(Visa, MasterCard, Discover Card, JCB, American Express, 기프트 카드 및 식당 카드 포함)를 감지하는 복잡하고 강력한 패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

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

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

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

## <a name="croatia-drivers-license-number"></a>크로아티아 운전 면허 번호

이 중요한 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에서만 사용할 수 있습니다.

### <a name="format"></a>형식

공백 및 구분 기호가 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

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

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>크로아티아 신분증 번호

이 중요한 정보 유형 엔터티는 EU 국가 식별 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

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

## <a name="croatia-personal-identification-oib-number"></a>크로아티아 개인 식별(OIB) 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자:

- 10개의 자릿수
- 마지막 숫자는 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

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

### <a name="format"></a>형식

10자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

10자리 숫자:

- 6자리 숫자(DDMMYY 형식의 생년월일)
- 하이픈
- 4자리 숫자, 마지막 숫자는 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

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

### <a name="format"></a>형식

16자리

### <a name="pattern"></a>패턴

복잡하고 견고한 패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

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

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

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

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

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

EU 운전 면허 번호 중요 정보 유형의 엔터티입니다.

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
- 영국

## <a name="eu-national-identification-number"></a>EU 국가 식별 번호

EU 국가 식별 번호 중요 정보 유형의 엔터티입니다.

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
- 영국

## <a name="eu-passport-number"></a>EU 여권 번호

EU 여권 번호 중요 정보 유형의 엔터티입니다. EU 여권 번호 번들에 있는 엔터티입니다.

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
- 영국

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU SSN(사회 보장 번호) 또는 동등한 ID

EU SSN(사회 보장 번호) 또는 동등한 ID 중요 정보 유형의 엔터티입니다.

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

## <a name="eu-tax-identification-number"></a>EU TIN(세금 식별 번호)

EU TIN(세금 식별 번호) 중요 정보 유형의 엔터티입니다.

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
- 영국



## <a name="finland-national-id"></a>핀란드 신분증

### <a name="format"></a>형식

6자리 숫자 + 세기를 나타내는 문자 + 3자리 숫자 + 확인 숫자

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.

- 6자리 숫자(DDMMYY 형식의 생년월일)
- 세기 표식('-', '+' 또는 'a')
- 3자리 개인 식별 번호
- 1자리 숫자 또는 문자(대/소문자 구분 안 함), 확인 숫자임

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

이 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에서 사용할 수 있으며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

9개 문자와 숫자의 조합

### <a name="pattern"></a>패턴

9개 문자와 숫자의 조합:

- 2개 문자(대/소문자 구분 안 함)
- 7자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

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

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>프랑스 운전 면허 번호

이 중요 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에서 사용할 수 있으며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

12자리, 프랑스 전화 번호와 같은 비슷한 패턴을 할인하는 유효성 검사 포함

### <a name="keywords"></a>키워드

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

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

## <a name="france-national-id-card-cni"></a>프랑스 CNI(National ID Card)

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

12자리

### <a name="keywords"></a>키워드

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

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

이 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에서 사용할 수 있으며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

9개의 숫자와 문자

### <a name="pattern"></a>패턴

9개의 숫자와 문자:

- 2자리 숫자
- 2개 문자(대/소문자 구분 안 함)
- 5자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>Keyword\_passport

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

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>프랑스 사회 보장 번호(INSEE) 또는 동등한 ID

이 중요 정보 유형 엔터티는 EU SSN(사회 보장 번호) 및 동등한 ID 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

15개의 자릿수

### <a name="pattern"></a>패턴

다음 두 패턴 중 하나와 일치해야 합니다.

- 13자리 숫자 뒤에 공백이 있고 그 뒤에 두 자리 숫자가 있거나
- 연속된 숫자 15개

### <a name="keywords"></a>키워드

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

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

이 중요 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

11개 문자와 숫자의 조합

### <a name="pattern"></a>패턴

11개의 숫자와 문자(대/소문자 구분 안 함):

- 1자리 숫자 또는 문자
- 2자리 숫자
- 6자리 숫자 또는 문자
- 1자리 숫자
- 1자리 숫자 또는 문자

### <a name="keywords"></a>키워드

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

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

## <a name="germany-identity-card-number"></a>독일 신분증 번호

### <a name="format"></a>형식

2010년 11월 1일 이후: 9개의 문자와 숫자

1987년 4월 1일부터 2010년 10월 31일까지: 10 자리 숫자

### <a name="pattern"></a>패턴

2010년 11월 1일 이후:

- 1개 문자(대/소문자 구분 안 함)
- 8자리 숫자

1987년 4월 1일부터 2010년 10월 31일까지:

- 10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

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

이 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

10자리 숫자 또는 문자

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.

- 첫 번째 문자는 이 세트(C, F, G, H, J, K)의 숫자 또는 문자
- 3자리 숫자
- 이 집합(C, -H, J-N, P, R, T, V-Z)의 5자리 숫자 또는 문자
- 1자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

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

## <a name="greece-national-id-card"></a>그리스 신분증 ID 카드

### <a name="format"></a>형식

7-8개 문자와 숫자의 조합에 대시 추가

### <a name="pattern"></a>패턴

7개 문자와 숫자(이전 형식):

- 1개 문자(그리스어 알파벳 임의의 문자)
- 대시
- 6자리 숫자

8개 문자와 숫자(새 형식):

- 2개 문자, 그리스 및 라틴 알파벳(ABEZHIKMNOPTYX) 모두의 대문자 포함
- 대시
- 6자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

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

## <a name="hong-kong-identity-card-hkid-number"></a>홍콩 신분증(HKID) 번호

### <a name="format"></a>형식

8-9개 문자와 숫자의 조합 및 마지막 문자를 괄호로 묶기(선택 사항)

### <a name="pattern"></a>패턴

8-9개 문자의 조합:

- 1-2개 문자(대/소문자 구분 안 함)
- 6자리 숫자
- 마지막 문자(임의의 숫자 또는 문자 A), 확인 숫자이며 선택적으로 괄호로 묶여 있음

### <a name="keywords"></a>키워드

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

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

### <a name="format"></a>형식

#### <a name="ipv4"></a>IPv4:

복잡한 패턴, IPv4 주소의 형식 있는(마침표) 버전 및 형식 없는(마침표 없음) 버전을 나타냄

#### <a name="ipv6"></a>IPv6:

복잡한 패턴, 형식화된 IPv6 번호(콜론 포함)를 나타냄

### <a name="pattern"></a>패턴

### <a name="keywords"></a>키워드

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>인도 PAN(영구 계좌 번호)

### <a name="format"></a>형식

10자리 문자 또는 숫자

### <a name="pattern"></a>패턴

10개 문자 또는 숫자:

- 3개 문자(대/소문자 구분 안 함)
- 문자 C, P, H, F, A, T, B, L, J, G(대/소문자 구분 안 함)
- 문자
- 4자리 숫자
- 문자(대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>인도 신분증(Aadhaar) 번호

### <a name="format"></a>형식

12자리 숫자, 선택적 공백 또는 대시 포함

### <a name="pattern"></a>패턴

12자리 숫자:

- 0 또는 1이 아닌 숫자 하나
- 3자리 숫자
- 선택적 공백 또는 대시
- 4자리 숫자
- 선택적 공백 또는 대시
- 마지막 숫자, 확인 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>인도네시아 신분증(KTP) 번호

### <a name="format"></a>형식

16자리 숫자, 선택적 마침표 포함

### <a name="pattern"></a>패턴

16자리 숫자:

- 2자리 지방 코드
- 마침표(선택 사항)
- 2자리 숫자 리젠시 또는 도시 코드
- 2 자리 숫자 하위 구역 코드
- 마침표(선택 사항)
- 6자리 숫자(DDMMYY 형식의 생년월일)
- 마침표(선택 사항)
- 4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN(국제 은행 계좌 번호)

### <a name="format"></a>형식

국가 번호(2개 문자) + 확인 숫자(2자리 숫자) + :::no-loc text="bban"::: 번호(최대 30자)

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.

- 2개 문자 국가 번호
- 2자리 확인 숫자(다음에 선택적인 공백)
- 4개 문자 또는 숫자로 구성된 1-7 그룹(공백으로 구분 가능)
- 1-3개 문자 또는 숫자

국가별 형식은 약간 다름 IBAN 중요 정보 유형에 다음 60개 국가 포함

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>키워드

없음

## <a name="ireland-personal-public-service-pps-number"></a>아일랜드 개별 공공 서비스(PPS) 번호

### <a name="format"></a>형식

이전 형식(2012년 12월 31일 까지):

- 7자리 숫자 뒤 1-2개 문자

새 형식(2013년 1월 1일 이후):

- 7자리 숫자 뒤 2개 문자

### <a name="pattern"></a>패턴

이전 형식(2012년 12월 31일 까지):

- 7자리 숫자
- 1~2개 문자(대/소문자 구분 안 함)

새 형식(2013년 1월 1일 이후):

- 7자리 숫자
- 문자 1개(대/소문자 구분 안 함), 알파벳 확인 숫자임
- A-I 범위의 선택적 문자 또는 &quot;W&quot;

### <a name="keywords"></a>키워드

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

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

### <a name="format"></a>형식

13자리

### <a name="pattern"></a>패턴

형식 있음:

- 2자리 숫자
- 대시
- 3자리 숫자
- 대시
- 8자리 숫자

서식 없음:

- 연속된 숫자 13개

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>이스라엘 국가 식별 번호

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

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

## <a name="italy-drivers-license-number"></a>이탈리아 운전 면허 번호

이 중요 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

10개 문자와 숫자의 조합

### <a name="pattern"></a>패턴

10개 문자와 숫자의 조합:

- 1개 문자(대/소문자 구분 안 함)
- 문자 &quot;A&quot; 또는 &quot;V&quot;(대/소문자 구분 안 함)
- 7자리 숫자
- 1개 문자(대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>일본 은행 계좌 번호

### <a name="format"></a>형식

7자리 또는 8자리 숫자

### <a name="pattern"></a>패턴

은행 계좌 번호:

- 7자리 또는 8자리 숫자
- 은행 계좌 지점 코드:
- 4자리 숫자
- 공백 또는 대시(선택 사항)
- 3자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

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

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>일본 운전 면허 번호

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

연속된 숫자 12개

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

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

### <a name="format"></a>형식

2개 문자 뒤에 7자리 숫자

### <a name="pattern"></a>패턴

2개 문자(대/소문자 구분 안 함) 뒤에 7자리 숫자

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

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

## <a name="japan-residence-card-number"></a>일본 거주증 번호

### <a name="format"></a>형식

12개의 문자와 숫자

### <a name="pattern"></a>패턴

12개의 문자와 숫자:

- 2개 문자(대/소문자 구분 안 함)
- 8자리 숫자
- 2개 문자(대/소문자 구분 안 함)

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>일본 주민 등록 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 11개

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

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

## <a name="japan-social-insurance-number-sin"></a>일본 SIN(사회 보장 번호)

### <a name="format"></a>형식

7-12자리 숫자

### <a name="pattern"></a>패턴

7-12자리 숫자:

- 4자리 숫자
- 하이픈(선택 사항)
- 6자리 숫자 또는
- 연속된 숫자 7-12개

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

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

## <a name="malaysia-identification-card-number"></a>말레이시아 신분증 번호

### <a name="format"></a>형식

12자리 숫자, 선택적 하이픈 포함

### <a name="pattern"></a>패턴

12자리 숫자:

- 6자리 숫자(YYMMDD 형식의 생년월일)
- 대시(선택 사항)
- 2개 문자 출생지 코드
- 대시(선택 사항)
- 임의의 3자리 숫자
- 1자리 숫자 성별 코드

### <a name="keywords"></a>키워드

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

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

## <a name="netherlands-citizens-service-bsn-number"></a>네덜란드 시민 서비스(BSN) 번호

### <a name="format"></a>형식

8-9자리 숫자, 선택적 공백 포함

### <a name="pattern"></a>패턴

8-9자리 숫자:

- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2-3자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

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

## <a name="new-zealand-ministry-of-health-number"></a>뉴질랜드 보건부 번호

### <a name="format"></a>형식

3개의 문자, 공백(선택 사항), 4자리 숫자

### <a name="pattern"></a>패턴

- 3개 문자(대/소문자 구분 안 함), 'I'와 'O'제외
- 공백(선택 사항)
- 4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

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

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자:

- 6자리 숫자(DDMMYY 형식의 생년월일)
- 3자리 숫자 개별 번호
- 2자리 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>필리핀 통합 다목적 식별 번호

### <a name="format"></a>형식

12자리 숫자, 하이픈으로 구분됨

### <a name="pattern"></a>패턴

12자리 숫자:

- 4자리 숫자
- 하이픈
- 7자리 숫자
- 하이픈
- 1자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>폴란드 신분증 카드

### <a name="format"></a>형식

3개의 문자 및 6자리 숫자

### <a name="pattern"></a>패턴

3개 문자(대/소문자 구분 안 함) 뒤에 6자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>폴란드 신분증(PESEL)

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

- 6자리 숫자, YYMMDD 형식의 생년월일
- 4자리 숫자
- 1자리 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

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

이 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

2개의 문자 및 7자리 숫자

### <a name="pattern"></a>패턴

2개 문자(대/소문자 구분 안 함) 뒤에 7자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>포르투갈 시민증 번호

### <a name="format"></a>형식

8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

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

## <a name="portugal-drivers-license-number"></a>포르투갈 운전 면허 번호

이 중요한 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에서만 사용할 수 있습니다.

### <a name="format"></a>형식

2가지 패턴 - 2개 문자 뒤에 특수 문자가 있는 5-8자리 숫자

### <a name="pattern"></a>패턴

패턴 1: 2개 문자 뒤에 특수 문자가 있는 5/6자:

- 2개 문자(대/소문자 구분 안 함)
- 하이픈
- 5자리 또는 6자리 숫자
- 공백
- 1자리 숫자

패턴 2: 1개 문자 뒤에 특수 문자가 있는 6/8자리 숫자:

- 1개 문자(대/소문자 구분 안 함)
- 하이픈
- 6자리 또는 8자리 숫자
- 공백
- 1자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

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

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

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

## <a name="saudi-arabia-national-id"></a>사우디아라비아 신분증 ID

### <a name="format"></a>형식

10개의 자릿수

### <a name="pattern"></a>패턴

연속된 숫자 10개

### <a name="keywords"></a>키워드

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>싱가포르 공민 신분증(NRIC) 번호

### <a name="format"></a>형식

9개의 문자와 숫자

### <a name="pattern"></a>패턴

- 9개의 문자와 숫자:
- 문자 &quot;F&quot;, &quot;G&quot;, &quot;S&quot; 또는 &quot;T&quot;(대/소문자 구분 안 함)
- 7자리 숫자
- 1개 영문자 확인 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

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

### <a name="format"></a>형식

13자리 숫자, 공백 포함 가능

### <a name="pattern"></a>패턴

13자리 숫자:

- 6자리 숫자(YYMMDD 형식의 생년월일)
- 4자리 숫자
- 1자리 숫자 시민권 표시기
- 숫자 &quot;8&quot; 또는 &quot;9&quot;
- 1자리 숫자, 체크섬 숫자임

### <a name="keywords"></a>키워드

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>대한민국 주민 등록 번호

### <a name="format"></a>형식

13자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

13자리 숫자:

- 6자리 숫자(YYMMDD 형식의 생년월일)
- 하이픈
- 1자리 숫자, 세기와 성별에 따라 결정
- 4자리 숫자 출생 지역 코드
- 1자리 숫자, 앞의 숫자가 동일한 사람들을 구분하는 데 사용
- 1자리 확인 숫자.

### <a name="keywords"></a>키워드

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>스페인 SSN(사회 보장 번호)

이 중요 정보 유형 엔터티는 EU SSN(사회 보장 번호) 또는 동등한 ID 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

11-12자리 숫자

### <a name="pattern"></a>패턴

11-12자리 숫자:

- 2자리 숫자
- 슬래시(선택 사항)
- 7~8자리 숫자
- 슬래시(선택 사항)
- 2자리 숫자

### <a name="keywords"></a>키워드

없음

## <a name="sweden-national-id"></a>스웨덴 신분증 ID

### <a name="format"></a>형식

10자리 또는 12자리 숫자와 선택적 구분 기호

### <a name="pattern"></a>패턴

10자리 또는 12자리 숫자와 선택적 구분 기호:

- 2자리 숫자(선택 사항)
- 6자리 숫자, 날짜 형식 YYMMDD
- 구분 기호 &quot;-&quot; 또는 &quot;+&quot;(선택 사항)
- 4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

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

이 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

8자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 8개

### <a name="keywords"></a>키워드

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

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

#### <a name="keyword_passport"></a>Keyword\_passport

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

### <a name="format"></a>형식

4개 문자 뒤에 5-31개 문자 또는 숫자

### <a name="pattern"></a>패턴

4개 문자 뒤에 5-31개 문자 또는 숫자:

- 4개 문자 은행 코드(대/소문자 구분 안 함)
- 공백(선택 사항)
- 4-28개 문자 또는 숫자(BBAN(Basic Bank Account Number))
- 공백(선택 사항)
- 1~3개 문자 또는 숫자(BBAN의 나머지)

### <a name="keywords"></a>키워드

#### <a name="keyword_swift"></a>Keyword\_swift

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

### <a name="format"></a>형식

1개 문자(영문) 뒤에 9자리 숫자

### <a name="pattern"></a>패턴

1개 문자(영문) 뒤에 9자리 숫자:

- 1개 문자(영문, 대/소문자 구분 안 함)
- 숫자 &quot;1&quot; 또는 &quot;2&quot;
- 8자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

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

## <a name="taiwan-passport-number"></a>대만 여권 번호

### <a name="format"></a>형식

- 생체 인식 여권 번호: 9자리 숫자
- 비 생체 인식 여권 번호: 9자리 숫자

### <a name="pattern"></a>패턴

생체 인식 여권 번호:

- 문자 &quot;3&quot;
- 8자리 숫자

비 생체 인식 여권 번호:

- 9자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

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

## <a name="taiwan-resident-certificate-arctarc-number"></a>대만 거류증(ARC/TARC) 번호

### <a name="format"></a>형식

10개의 문자와 숫자:

### <a name="pattern"></a>패턴

10개의 문자와 숫자:

- 2개 문자(대/소문자 구분 안 함)
- 8자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

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

## <a name="uk-drivers-license-number"></a>영국 운전 면허 번호

이 중요 정보 유형 엔터티는 EU 운전 면허 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

지정된 형식의 18개 문자와 숫자 조합

### <a name="pattern"></a>패턴

18개의 문자와 숫자:

- 5개 문자(대/소문자 구분 안 함) 또는 문자 대신 숫자 &quot;9&quot;
- 1자리 숫자
- 5자리 숫자, MMDDY 날짜 형식의 생년월일(운전자가 여성인 경우 7번째 문자는 50씩 증가함. 즉, 01에서 12가 아닌 51에서 62로 증가)
- 2개 문자(대/소문자 구분 안 함) 또는 문자 대신 숫자 &quot;9&quot;
- 5자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

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

## <a name="uk-electoral-roll-number"></a>영국 선거인 명부 번호

### <a name="format"></a>형식

2개 문자 뒤에 1-4자리 숫자

### <a name="pattern"></a>패턴

2개 문자(대/소문자 구분 안 함) 뒤에 1-4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- 협의회 지명
- 지명 양식
- 선거인 등록
- 선거인 명부

## <a name="uk-national-health-service-number"></a>영국 국민 건강 보험 번호

### <a name="format"></a>형식

10-17자리 숫자, 공백으로 구분

### <a name="pattern"></a>패턴

10-17자리 숫자:

- 3자리 또는 10자리 숫자
- 공백
- 3자리 숫자
- 공백
- 4자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>영국 NINO(국민 보험 번호)

이 중요한 정보 유형 엔터티는 EU 국가 식별 번호 중요 정보 유형에 포함되며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

7개 문자 또는 9개 문자, 공백 또는 대시로 구분

### <a name="pattern"></a>패턴

2가지 가능한 패턴:

- 2개 문자(유효한 NINO는 특정 문자만 접두사에 사용(패턴 유효성 검사용); 대/소문자 구분 안 함)
- 6자리 숫자
- 'A', 'B', 'C' 또는 'D'(접두사와 마찬가지로 특정 문자만 접미사에 사용 가능; 대/소문자 구분 안 함)

또는

- 2개 문자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 'A', 'B', 'C', 'D' 중 하나


### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

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

## <a name="uk-unique-taxpayer-reference-number"></a>영국 고유 납세자 참조 번호

이 중요 정보 형식은 다음에만 사용할 수 있습니다.

- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="keywords"></a>키워드

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

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

### <a name="format"></a>형식

6-17자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 6-17개

### <a name="keywords"></a>키워드

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

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

### <a name="format"></a>형식

주별로 다름

### <a name="pattern"></a>패턴

주(예 : 뉴욕)별로 다름

- 9자리 숫자, ddd와 같은 형식이 일치함
- 9자리 숫자, ddddddddd와 같은 형식은 일치하지 않음

### <a name="keywords"></a>키워드

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

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

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

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

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- 주 약어(예: &quot;NY&quot;)
- 주 이름(예: &quot;New York&quot;)

## <a name="us-individual-taxpayer-identification-number-itin"></a>미국 ITIN(개인 납세자 식별 번호)

### <a name="format"></a>형식

9자리 숫자, &quot;9&quot;로 시작하고 &quot;7&quot; 또는 &quot;8&quot;을 4번째 숫자로 포함, 필요에 따라 공백 또는 대시로 형식 지정

### <a name="pattern"></a>패턴

형식 있음:

- 숫자 &quot;9&quot;
- 2자리 숫자
- 공백 또는 대시
- &quot;7&quot; 또는 &quot;8&quot;
- 1자리 숫자
- 공백 또는 대시
- 4자리 숫자

형식 없음:

- 숫자 &quot;9&quot;
- 2자리 숫자
- &quot;7&quot; 또는 &quot;8&quot;
- 5자리 숫자

### <a name="keywords"></a>키워드

#### <a name="keyword_itin"></a>Keyword\_itin

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

## <a name="us-social-security-number-ssn"></a>미국 SSN(사회 보장 번호)

### <a name="format"></a>형식

9자리 숫자, 형식이 있거나 없는 패턴일 수 있음

>[!Note]
> 2011년 중반 이전에 발급된 경우 SSN에 강력히 형식이 적용됨, 숫자의 특정 부분이 특정 범위 내에 있어야 유효함(체크섬은 없음)
>

### <a name="pattern"></a>패턴

4가지 함수가 4가지 다른 패턴으로 SSN을 찾음:

- Func\_ssn - 2011년 이전의 강력한 형식 즉, 대시 또는 공백으로 형식이 적용된(ddd-dd-dddd 또는 ddd dd dddd) SSN을 찾음
- Func\_unformatted\_ssn - 2011년 이전의 강력한 형식 즉, 연속된 9자리 숫자(ddddddddd)로 형식이 적용되지 않은 SSN을 찾음
- Func\_randomized\_formatted\_ssn - 2011년 이후 형식 즉, 대시 또는 공백으로 형식이 지정된(ddd-dd-dddd 또는 ddd dd dddd) SSN을 찾음
- Func\_randomized\_unformatted\_ssn - 2011년 이후 형식 즉, 연속된 9자리 숫자(ddddddddd)로 형식이 적용되지 않은 SSN을 찾음

### <a name="keywords"></a>키워드

#### <a name="keyword_ssn"></a>Keyword\_ssn

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

영국 여권 번호 중요 정보 유형 엔터티는 EU 여권 번호 중요 정보 유형에서 사용할 수 있으며 독립형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="keywords"></a>키워드

#### <a name="keyword_passport"></a>Keyword\_passport

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
