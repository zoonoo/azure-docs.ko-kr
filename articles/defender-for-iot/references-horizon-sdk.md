---
title: Horizon SDK
titleSuffix: Azure Defender for IoT
description: 수평 SDK를 사용 하면 IoT 용 Azure Defender 개발자는 네트워크 트래픽을 디코딩하는 dissector 플러그 인을 설계 하 여 자동 Defender for IoT 네트워크 분석 프로그램에서 처리할 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 53aafc4146680c89dd01174ec5fde765f1cc0c01
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746022"
---
# <a name="horizon-proprietary-protocol-dissector"></a>수평 소유 프로토콜 dissector

수평은 독점 프로토콜을 실행 하는 IoT 및 ICS 장치를 보호 하는 데 사용 되는 ODE (Open Development Environment)입니다.

이 환경은 고객과 기술 파트너를 위한 다음과 같은 솔루션을 제공 합니다.

- 모든 표준에서 벗어난 공용, 소유, 사용자 지정 프로토콜 또는 프로토콜에 대 한 완전 한 지원. 

- 새 수준의 유연성 및 DPI 개발의 범위입니다.

- IoT 플랫폼 버전에 대 한 Defender를 업그레이드할 필요 없이 눈에 띄게 표시 하 고 제어 하는 도구입니다.

- 중요 한 정보를 divulging 하지 않고 독점적인 개발을 허용 하는 보안입니다.

수평 SDK를 사용 하면 IoT 용 Azure Defender 개발자는 네트워크 트래픽을 디코딩하는 dissector 플러그 인을 설계 하 여 자동 Defender for IoT 네트워크 분석 프로그램에서 처리할 수 있습니다.

프로토콜 dissectors는 외부 플러그 인으로 개발 되며 IoT 서비스용 포괄적인 범위와 통합 됩니다. 예를 들어 모니터링, 경고 및 보고 기능을 제공 하는 서비스입니다.

## <a name="secure-development-environment"></a>보안 개발 환경 

수평 ODE를 사용 하면 조직 외부에서 공유할 수 없는 사용자 지정 또는 소유 프로토콜을 개발할 수 있습니다. 예를 들어 법률 규정 또는 회사 정책에 의해 발생 합니다.

다음을 사용 하지 않고 dissector 플러그 인 개발 

- 프로토콜이 정의 되는 방식에 대 한 소유 정보를 노출 합니다.

- 중요 한 PCAPs를 공유 합니다.

- 규정 준수 규정 위반.

## <a name="customization-and-localization"></a>사용자 지정 및 지역화  

SDK는 다음과 같은 다양 한 사용자 지정 옵션을 지원 합니다.

  - 함수 코드에 대 한 텍스트입니다. 

  - 경고, 이벤트 및 프로토콜 매개 변수에 대 한 전체 지역화 텍스트입니다. 자세한 내용은 [매핑 파일 만들기 (JSON)](#create-mapping-files-json)를 참조 하세요.

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="완전히 지역화 된 경고를 봅니다.":::

## <a name="horizon-architecture"></a>수평 아키텍처

아키텍처 모델에는 세 가지 제품 계층이 포함 되어 있습니다.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>IoT 용 Defender 플랫폼 계층

IoT 플랫폼 버전에 대 한 Defender를 업그레이드할 필요 없이 IoT 플랫폼용 Defender에서 사용자 지정 dissector 플러그 인에 대 한 즉각적인 통합 및 실시간 모니터링을 사용할 수 있습니다.

## <a name="defender-for-iot-services-layer"></a>IoT 서비스용 Defender 계층

각 서비스는 특정 프로토콜에서 분리 된 파이프라인으로 설계 되어 더 효율적이 고 독립적인 개발을 가능 하 게 합니다.

각 서비스는 특정 프로토콜에서 분리 된 파이프라인으로 설계 되었습니다. 서비스는 파이프라인에서 트래픽을 수신 대기 합니다. 이러한 사용자는 플러그 인 데이터 및 센서에서 캡처한 트래픽과 상호 작용 하 여 배포 된 프로토콜을 인덱싱하고 트래픽 페이로드를 분석 하 고 보다 효율적이 고 독립적인 개발을 가능 하 게 합니다.

## <a name="custom-dissector-layer"></a>사용자 지정 dissector 계층 

IoT 독점 SDK (c + + 구현 및 JSON 구성 포함) 용 Defender를 사용 하 여 플러그 인을 만들 수 있습니다. 

- 프로토콜을 식별 하는 방법 정의

- 추출 하려는 필드를 트래픽에 매핑하고 추출 하는 방법을 정의 합니다. 

- IoT 서비스용 Defender와 통합 하는 방법 정의

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="기본 제공 계층입니다.":::

IoT 용 Defender는 일반적인 프로토콜에 대 한 기본 dissectors를 제공 합니다. 이러한 프로토콜을 기반으로 하는 dissectors를 빌드할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

## <a name="what-this-sdk-contains"></a>이 SDK에 포함 된 내용 

이 키트에는 개발에 필요한 헤더 파일이 포함 되어 있습니다. 개발 프로세스에는이 SDK에 설명 된 기본 단계와 선택적 고급 단계가 필요 합니다.

<support@cyberx-labs.com>헤더 파일 및 기타 리소스를 받는 방법에 대 한 정보를 문의 하세요.

## <a name="about-the-environment-and-setup"></a>환경 및 설치 정보 

### <a name="requirements"></a>요구 사항 

- 기본 개발 환경은 Linux입니다. Windows 환경에서 개발 하는 경우 Linux 시스템에서 VM을 사용 하는 것이 좋습니다.

- 컴파일 프로세스의 경우 GCC v7.4.0 이상을 사용 합니다. C + + 17에서 지원 되는 stdlib.h의 모든 표준 클래스를 사용 합니다.

- IoT 용 Defender 버전 3.0 이상

### <a name="process"></a>프로세스

1. C/c + + 개발자를 위한 Eclipse IDE를 [다운로드](https://www.eclipse.org/) 합니다. 선호 하는 다른 IDE를 사용할 수 있습니다. 이 문서에서는 Eclipse IDE를 사용 하 여 구성을 안내 합니다.

1. Eclipse IDE를 시작 하 고 프로젝트를 저장할 작업 영역을 구성한 후 **ctrl + n** 을 눌러 c + + 프로젝트로 만듭니다.

1. 다음 화면에서 이름을 개발 하려는 프로토콜로 설정 하 고 프로젝트 형식을 및로 선택 합니다 `Shared Library` `AND Linux GCC` .

1. **C/c + + 빌드**  >  **설정**  >  **도구 설정**  >  **GCC c + + 컴파일러**  >  **기타**  >  **눈금 위치 독립적 코드** 에서 프로젝트 속성을 편집 합니다.

1. SDK를 사용 하 여 받은 예제 코드를 붙여넣고 컴파일합니다.

1. Release.tar.gz 파일에 아티팩트 (library, config.json 및 metadata)를 추가 하 고 파일 확장명을 hdp로 변경 합니다 \<XXX> . 여기서은 \<XXX> 플러그 인의 이름입니다.

### <a name="research"></a>연구 

시작 하기 전에 다음을 확인 합니다.

- 사용 가능한 경우 프로토콜 사양을 읽습니다.

- 추출 하려는 프로토콜 필드를 확인 합니다.

- 매핑 목표를 계획 했습니다.

## <a name="about-plugin-files"></a>플러그 인 파일 정보 

개발 프로세스 중에 세 개의 파일이 정의 됩니다.

### <a name="json-configuration-file-required"></a>JSON 구성 파일 (필수) 

이 파일은 dissector ID와 선언, 종속성, 통합 요구 사항, 유효성 검사 매개 변수 및 매핑 정의를 정의 하 여 값을 이름으로, 숫자를 텍스트로 변환 해야 합니다. 자세한 내용은 다음 링크를 참조 하세요.

- [구성 파일 준비 (JSON)](#prepare-the-configuration-file-json)

- [구현 코드 유효성 검사 준비](#prepare-implementation-code-validations)

- [장치 메타 데이터 추출](#extract-device-metadata)

- [인덱싱 서비스에 연결 (기준)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>구현 코드: c + + (필수)

구현 코드 (CPP)는 원시 트래픽을 구문 분석 하 여 서비스, 클래스 및 함수 코드와 같은 값에 매핑합니다. 계층 필드를 추출 하 여 JSON 구성 파일의 인덱스 이름에 매핑합니다. CPP에서 추출할 필드가 구성 파일에 정의 되어 있습니다. 자세한 내용은 [구현 코드 준비 (c + +)](#prepare-the-implementation-code-c)를 참조 하세요.

### <a name="mapping-files-optional"></a>파일 매핑 (옵션)

엔터프라이즈 환경의 요구 사항에 맞게 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="마이그레이션이나":::

코드를 변경 하지 않고 텍스트를 업데이트 하는 매핑 파일을 정의 하 고 업데이트할 수 있습니다. 각 파일은 하나 이상의 필드를 매핑할 수 있습니다.

  - 필드 값을 이름에 매핑합니다 (예: 1: Reset, 2: Start, 3: Stop).

  - 여러 언어를 지원 하도록 텍스트를 매핑합니다.

자세한 내용은 [매핑 파일 만들기 (JSON)](#create-mapping-files-json)를 참조 하세요.

## <a name="create-a-dissector-plugin-overview"></a>Dissector 플러그 인 만들기 (개요)

1. [환경 및 설정 정보](#about-the-environment-and-setup) 섹션을 검토 합니다.

2.  [구현 코드 (c + +)를 준비](#prepare-the-implementation-code-c)합니다. 파일 **에template.js** 를 복사 하 고 요구 사항에 맞게 편집 합니다. 키를 변경 하지 마십시오. 

3. [구성 파일 (JSON)을 준비](#prepare-the-configuration-file-json)합니다. **템플릿 .cpp** 파일을 복사 하 고 override 메서드를 구현 합니다. 자세한 내용은 [마감일::p rotocol:: BaseParser](#horizonprotocolbaseparser) 를 참조 하세요.

4. [구현 코드 유효성 검사를 준비](#prepare-implementation-code-validations)합니다.

## <a name="prepare-the-implementation-code-c"></a>구현 코드 준비 (c + +)

CPP 파일은 다음을 담당 하는 파서입니다.

- 패킷 헤더 및 페이로드 (예: 헤더 길이 또는 페이로드 구조)의 유효성을 검사 하는 중입니다.

- 헤더 및 페이로드에서 정의 된 필드로 데이터를 추출 하는 중입니다.

- JSON 파일에서 구성 된 필드 추출을 구현 합니다.

### <a name="what-to-do"></a>알아두어야 할 사항

템플릿 **.cpp** 파일을 복사 하 고 override 메서드를 구현 합니다. 자세한 내용은 [마감일::p rotocol:: BaseParser](#horizonprotocolbaseparser)를 참조 하세요.

### <a name="basic-c-template-sample"></a>기본 c + + 템플릿 샘플 

이 섹션에서는 IoT 수평 프로토콜용 샘플 Defender에 대 한 표준 함수를 사용 하는 기본 프로토콜 템플릿을 제공 합니다.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>기본 c + + 템플릿 설명  

이 섹션에서는 기본 프로토콜 템플릿과 IoT 수평 프로토콜용 샘플 Defender에 대 한 표준 함수에 대해 설명 합니다. 

### <a name="include-pluginpluginh"></a>"plugin/plugin. h"를 #include 합니다.

플러그 인에서 사용 하는 정의입니다. 헤더 파일에는 개발을 완료 하는 데 필요한 모든 항목이 포함 되어 있습니다.

### <a name="horizonprotocolbaseparser"></a>마감일::p rotocol:: BaseParser

수평 인프라와 플러그 인 계층 간의 통신 인터페이스입니다. 자세한 내용은 계층에 대 한 개요는 [수평 아키텍처](#horizon-architecture) 를 참조 하세요.

ProcessLayer는 데이터를 처리 하는 데 사용 되는 방법입니다.

- 함수 코드의 첫 번째 매개 변수는 이전에 처리 된 데이터를 검색 하 고, 새 필드를 만들고, 계층을 만드는 데 사용 되는 처리 유틸리티입니다.

- 함수 코드의 두 번째 매개 변수는 이전 파서에서 전달 된 현재 데이터입니다.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

를 사용 하 여 파서의 인스턴스를 만듭니다.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>프로토콜 함수 코드 샘플 

이 섹션에서는 코드 번호 (2 바이트)와 메시지 길이 (4 바이트)를 추출 하는 방법의 예를 제공 합니다.

이 작업은 JSON 구성 파일에 제공 된 endian에 따라 수행 됩니다. 즉, 프로토콜이 *거의 endian* 이 고 센서가 작은 endian 컴퓨터에서 실행 되는 경우에는 변환 됩니다.

또한 계층은 데이터를 저장 하기 위해 생성 됩니다. 처리 유틸리티에서 *Fieldmanager* 를 사용 하 여 새 필드를 만듭니다. 필드에는 *문자열*, *숫자*, *원시 데이터*, *배열* (특정 형식) 또는 *복합* 유형 중 하나만 사용할 수 있습니다. 이 계층에는 ID가 인 숫자, 원시 또는 문자열이 포함 될 수 있습니다.

아래 샘플에서는 다음 두 필드를 추출 합니다.

- `function_code_number`

- `headerLength`

새 레이어가 만들어지고 추출 된 필드가 여기에 복사 됩니다.

아래 샘플에서는 플러그 인 처리를 위해 구현 된 기본 논리 인 특정 함수에 대해 설명 합니다.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>관련 JSON 필드 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="관련 json 필드입니다.":::

## <a name="prepare-the-configuration-file-json"></a>구성 파일 준비 (JSON)

수평 SDK는 데이터를 저장 하 고 전송 하는 간단한 형식인 표준 JavaScript Object Notation (JSON)를 사용 하며, 전용 스크립팅 언어가 필요 하지 않습니다.

이 단원에서는 최소 JSON 구성 선언과 관련 된 구조에 대해 설명 하 고 프로토콜을 정의 하는 샘플 구성 파일을 제공 합니다. 이 프로토콜은 장치 검색 서비스와 자동으로 통합 됩니다.

## <a name="file-structure"></a>파일 구조

아래 샘플에서는 파일 구조에 대해 설명 합니다.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="파일 구조의 샘플입니다.":::

### <a name="what-to-do"></a>알아두어야 할 사항

템플릿 파일을 복사 하 `config.json` 고 요구 사항에 맞게 편집 합니다. 키를 변경 하지 마십시오. 키는 [샘플 JSON 구성 파일](#sample-json-configuration-file)에서 빨간색으로 표시 됩니다. 

### <a name="file-naming-requirements"></a>파일 명명 요구 사항 

JSON 구성 파일은로 저장 해야 합니다 `config.json` .

### <a name="json-configuration-file-fields"></a>JSON 구성 파일 필드

이 단원에서는 정의할 JSON 구성 필드에 대해 설명 합니다. 필드 *레이블은* 변경 하지 마십시오.

### <a name="basic-parameters"></a>기본 매개 변수

이 섹션에서는 기본 매개 변수를 설명 합니다.

| 매개 변수 레이블 | 설명 | 형식 |
|--|--|--|
| **ID** | 프로토콜의 이름입니다. 기본값을 삭제 하 고 표시 되는 프로토콜의 이름을 추가 합니다. | String |
| **endianess** | 다중 바이트 데이터를 인코딩하는 방법을 정의 합니다. "사소한" 또는 "빅" 용어를 사용 합니다. 프로토콜 사양 또는 트래픽 기록에서 가져옵니다. | String |
| **sanity_failure_codes** | 이러한 코드는 코드 id와 관련 된 온전성 충돌이 있을 때 파서에서 반환 되는 코드입니다. C + + 섹션에서 매직 넘버 유효성 검사를 참조 하세요. | String |
| **malformed_codes** | 이러한 코드는 올바르게 식별 되었지만 오류가 검색 되는 코드입니다. 예를 들어, 필드 길이가 너무 짧거나 길고 값이 잘못 된 경우입니다. | String |
| **dissect_as** | 특정 프로토콜 트래픽이 도착할 위치를 정의 하는 배열입니다. | TCP/UDP, 포트 등 |
| **필드** | 트래픽에서 추출 되는 필드의 선언입니다. 각 필드에는 고유한 ID (이름)와 형식 (숫자, 문자열, 원시, 배열, 복합)이 있습니다. 예를 들어 구현 파서 파일에서 추출 된 field [함수](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) 구성 파일에 작성 된 필드도 계층에 추가할 수 있습니다. |  |

### <a name="other-advanced-fields"></a>기타 고급 필드 

이 섹션에서는 다른 필드에 대해 설명 합니다.

| 매개 변수 레이블 | 설명 |
|-----------------|--------|
| **허용 목록** | 프로토콜 값을 인덱싱하고 데이터 마이닝 보고서에 표시할 수 있습니다. 이러한 보고서는 네트워크 기준선을 반영 합니다. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="데이터 마이닝 뷰의 샘플입니다."::: <br /> 자세한 내용은 [인덱싱 서비스에 연결 (기준)](#connect-to-an-indexing-service-baseline) 을 참조 하세요. |
| **펌웨어입니다** | 펌웨어 정보를 추출 하 고, 인덱스 값을 정의 하 고, 플러그 인 프로토콜에 대 한 펌웨어 경고를 트리거할 수 있습니다. 자세한 내용은 [펌웨어 데이터 추출](#extract-firmware-data) 항목을 참조 하세요. |
| **value_mapping** | 매핑 파일을 정의 하 고 업데이트 하 여 엔터프라이즈 환경의 요구 사항에 맞게 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다. 예를 들어 언어 파일에 매핑할 수 있습니다. 코드를 변경 하거나 영향을 주지 않고 텍스트에 변경 내용을 쉽게 구현할 수 있습니다. 자세한 내용은 [매핑 파일 만들기 (JSON)](#create-mapping-files-json) 를 참조 하세요. |

## <a name="sample-json-configuration-file"></a>샘플 JSON 구성 파일

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>구현 코드 유효성 검사 준비

이 단원에서는 c + + 코드 유효성 검사 함수 구현에 대해 설명 하 고 샘플 코드를 제공 합니다. 다음 두 계층의 유효성 검사를 사용할 수 있습니다.

- 검사.

- 코드 형식이 잘못 되었습니다.

작동 하는 플러그 인을 빌드하기 위해 유효성 검사 코드를 만들 필요가 없습니다. 유효성 검사 코드를 준비 하지 않은 경우 성공적으로 처리 되었음을 나타내는 센서 데이터 마이닝 보고서를 검토할 수 있습니다.

필드 값은 매핑 파일의 텍스트에 매핑되고 처리에 영향을 주지 않고 원활 하 게 업데이트할 수 있습니다.

## <a name="sanity-code-validations"></a>온전성 코드 유효성 검사 

이렇게 하면 전송 된 패킷이 프로토콜의 유효성 검사 매개 변수와 일치 하는지 확인 하 여 트래픽 내에서 프로토콜을 식별 하는 데 도움이 됩니다.

예를 들어, 처음 8 바이트를 *매직 숫자로* 사용 합니다. 온전성에 오류가 발생 하는 경우 온전성 실패 응답이 반환 됩니다.

예:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

다른 관련 플러그 인을 배포한 경우 해당 플러그 인에 대해 패킷의 유효성이 검사 됩니다.

## <a name="malformed-code-validations"></a>잘못 된 형식의 코드 유효성 검사 

잘못 된 유효성 검사는 프로토콜의 유효성을 검사 한 후에 사용 됩니다.

프로토콜을 기반으로 하는 패킷을 처리 하는 데 실패 하면 오류 응답이 반환 됩니다.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="형식이 잘못 된 코드":::

## <a name="c-sample-with-validations"></a>유효성 검사가 포함 된 c + + 샘플

함수에 따라 프로세스는 아래 예제와 같이 수행 됩니다.

### <a name="function-20"></a>함수 20 

- 펌웨어로 처리 됩니다.

- 필드는 함수에 따라 읽혀집니다.

- 필드는 계층에 추가 됩니다.

### <a name="function-10"></a>함수 10 

- 함수는 보다 구체적인 작업 인 다른 하위 함수를 포함 합니다.

- 하위 함수를 읽고 계층에 추가 합니다.

이 작업이 완료 되 면 처리가 완료 된 것입니다. 반환 값은 dissector 계층이 성공적으로 처리 되었는지 여부를 나타냅니다. 이 경우 계층을 사용할 수 있게 됩니다.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>장치 메타 데이터 추출

자산에서 다음 메타 데이터를 추출할 수 있습니다.

  - `Is_distributed_control_system` -프로토콜이 분산 제어 시스템에 포함 되어 있는지 여부를 나타냅니다. (예: SCADA protocol은 false 여야 함)

  - `Has_protocol_address` – 프로토콜 주소가 있는지 여부를 나타냅니다. 현재 프로토콜에 대 한 특정 주소 (예: MODBUS 단위 식별자)

  - `Is_scada_protocol` -프로토콜이 OT 네트워크와 관련 되어 있는지 여부를 나타냅니다.

  - `Is_router_potential` -프로토콜이 주로 라우터를 사용 하는지 여부를 나타냅니다. 예를 들면 LLDP, CDP 또는 STP가 있습니다.

이를 위해서는 메타 데이터 속성을 사용 하 여 JSON 구성 파일을 업데이트 해야 합니다.

## <a name="json-sample-with-metadata"></a>메타 데이터가 포함 된 JSON 샘플 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>프로그래밍 코드 추출 

프로그래밍 이벤트가 발생 하는 경우 코드 콘텐츠를 추출할 수 있습니다. 추출 된 콘텐츠를 통해 다음을 수행할 수 있습니다.

- 여러 프로그래밍 이벤트에서 코드 파일 콘텐츠를 비교 합니다.

- 권한 없는 프로그래밍에 대 한 경고를 트리거합니다.  

- 프로그래밍 코드 파일을 받기 위한 이벤트를 트리거합니다.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="프로그래밍 변경 로그입니다.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="단추를 클릭 하 여 프로그래밍을 봅니다.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="권한 없는 PLC 프로그래밍 경고입니다.":::

이를 위해 JSON 구성 파일은 속성을 사용 하 여 업데이트 해야 합니다 `code_extraction` . 

### <a name="json-configuration-fields"></a>JSON 구성 필드 

이 섹션에서는 JSON 구성 필드에 대해 설명 합니다. 

- **방법이**

  프로그래밍 이벤트 파일을 수신 하는 방법을 나타냅니다. 

  모든 (각 프로그래밍 작업으로 인해 파일이 변경 되지 않은 경우에도 모든 코드 파일이 수신 됩니다.)

- **file_type**  

  코드 콘텐츠 형식을 나타냅니다.  

  텍스트 (각 코드 파일에는 텍스트 정보가 포함 되어 있습니다.)

- **code_data_field**
  
  코드 콘텐츠를 제공 하기 위해 사용할 구현 필드를 나타냅니다.

  필드가.

- **code_name_field**

  코딩 파일의 이름을 제공 하기 위해 사용할 구현 필드를 나타냅니다.

  필드가.

- **size_limit**

  코드 파일이 설정 된 제한을 초과 하는 경우 각 코딩 파일 콘텐츠의 크기 제한 (바이트)을 나타냅니다. 이 필드를 지정 하지 않으면 기본값은 1500만가 됩니다.

  수많은.

- **메타**

  코드 파일에 대 한 추가 정보를 나타냅니다.

  두 개의 속성이 있는 개체를 포함 하는 배열입니다.

    - name (String)-메타 데이터 키 XSense는 현재 사용자 이름 키만 지원함을 나타냅니다.
 
    - value (Field)-메타 데이터 데이터를 제공 하기 위해 사용할 구현 필드를 나타냅니다.

## <a name="json-sample-with-programming-code"></a>프로그래밍 코드를 포함 하는 JSON 샘플

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>사용자 지정 가로 경고

일부 프로토콜 함수 코드는 오류를 나타낼 수 있습니다. 예를 들어 프로토콜에서 항상 특정 온도에 저장 해야 하는 특정 화학의 컨테이너를 제어 합니다. 이 경우 온도계에서 오류를 나타내는 함수 코드가 있을 수 있습니다. 예를 들어 함수 코드가 25 인 경우 컨테이너에 문제가 있음을 나타내는 경고를 웹 콘솔에서 트리거할 수 있습니다. 이러한 경우 심층 패킷 경고를 정의할 수 있습니다.

플러그 인의에 **경고** 매개 변수를 추가 합니다 `config.json` .

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON 구성 필드

이 섹션에서는 JSON 구성 필드에 대해 설명 합니다. 

| 필드 이름 | Description | 가능한 값 |
|--|--|--|
| **ID** | 단일 경고 ID를 나타냅니다. 이 컨텍스트에서 고유 해야 합니다. | 숫자 값 0-10000 |
| **message** | 사용자에 게 표시 되는 정보입니다. 이 필드를 사용 하 여 다른 필드를 사용할 수 있습니다. | 프로토콜의 모든 필드 또는 더 낮은 계층 프로토콜을 사용 합니다. |
| **title** | 경고 제목 |  |
| **expression** | 이 경고를 표시 하려는 경우 | 하위 계층에 있는 숫자 필드나 현재 계층을 사용 합니다.</br></br> SDK에서 필드를 검색 하기 위해 각 필드는와 함께 사용 해야 합니다. `{}` 지원 되는 논리 연산자는</br> = =-같음</br> <=-보다 작거나 같음</br> >=-이상 또는 같음</br> >-보다 큼</br> <-보다 작음</br> ~ =-같지 않음 |

## <a name="more-about-expressions"></a>식에 대 한 자세한 정보

수평 SDK가 식을 호출 하 고 *true* 가 될 때마다 센서에서 경고가 트리거됩니다.

동일한 경고에 여러 식을 포함할 수 있습니다. 예를 들면 다음과 같습니다.

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

이 식은 패킷 ipv4 src가 10.10.10.10 경우에만 함수 코드의 유효성을 검사 합니다. 숫자 표현에서 IP 주소의 원시 표현입니다.

`and`식을 연결 하기 위해 또는를 사용할 수 있습니다 `or` .

## <a name="json-sample-custom-horizon-alerts"></a>JSON 샘플 사용자 지정 가로선 경고

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>인덱싱 서비스에 연결 (기준)

프로토콜 값을 인덱싱하고 데이터 마이닝 보고서에 표시할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="데이터 마이닝 옵션의 뷰입니다.":::

나중에 이러한 값을 특정 텍스트에 매핑할 수 있습니다. 예를 들어 숫자를 텍스트로 매핑 또는 정보를 추가 하 여 모든 언어를 사용할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="마이그레이션이나":::

자세한 내용은 [매핑 파일 만들기 (JSON)](#create-mapping-files-json) 를 참조 하세요.

이전에 구문 분석 된 프로토콜의 값을 사용 하 여 추가 정보를 추출할 수도 있습니다.

예를 들어 TCP를 기반으로 하는 값의 경우 IPv4 계층의 값을 사용할 수 있습니다. 이 계층에서 패킷의 원본 및 대상과 같은 값을 추출할 수 있습니다.

이를 위해 JSON 구성 파일은 속성을 사용 하 여 업데이트 해야 합니다 `whitelists` .

## <a name="allow-list-data-mining-fields"></a>허용 목록 (데이터 마이닝) 필드

다음 허용 목록 필드를 사용할 수 있습니다.

- 이름 – 인덱싱에 사용 되는 이름입니다.

- alert_title – 이벤트를 설명 하는 짧고 고유한 제목입니다.

- alert_text – 추가 정보

여러 허용 목록을 추가 하 여 인덱싱의 완전 한 유연성을 허용할 수 있습니다.

## <a name="json-sample-with-indexing"></a>인덱싱이 포함 된 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>펌웨어 데이터 추출

펌웨어 정보를 추출 하 고, 인덱스 값을 정의 하 고, 플러그 인 프로토콜에 대 한 펌웨어 경고를 트리거할 수 있습니다. 예를 들면 다음과 같습니다.

- 펌웨어 모델 또는 버전을 추출 합니다. 이 정보를 활용 하 여 CVEs를 식별할 수 있습니다.

- 새 펌웨어 버전이 검색 되 면 경고를 트리거합니다.

이를 위해, JSON 구성 파일은 펌웨어 속성을 사용 하 여 업데이트 해야 합니다.

## <a name="firmware-fields"></a>펌웨어 필드

이 섹션에서는 JSON 펌웨어 구성 필드에 대해 설명 합니다.

- **name**
  
  센서 콘솔에서 필드가 표시 되는 방법을 나타냅니다.

- **value**

 데이터를 제공 하기 위해 사용할 구현 필드를 나타냅니다. 

- **firmware_index:**

  다음 중 하나를 선택합니다.  
  - **모델**: 장치 모델입니다. CVEs의 검색을 사용 하도록 설정 합니다.
  - **직렬**: 장치 일련 번호입니다. 모든 프로토콜에 대해 일련 번호를 항상 사용할 수 있는 것은 아닙니다. 이 값은 장치 마다 고유 합니다.
  - **랙**: 장치가 랙의 일부인 경우 랙 식별자를 나타냅니다.
  - **슬롯**: 장치가 랙의 일부인 경우 슬롯 식별자입니다.  
  - **module_address**: 모듈을 다른 장치 뒤에 표시할 수 있는 경우를 사용 하 여 계층을 제공 합니다. 더 간단한 프레젠테이션 인 랙 슬롯 조합이 있는 경우에는 해당 됩니다.  
  - **firmware_version**: 장치 버전을 나타냅니다. CVEs의 검색을 사용 하도록 설정 합니다.
  - **alert_text**: 펌웨어 편차를 설명 하는 텍스트를 나타냅니다 (예: 버전 변경).  
  - **index_by**: 장치를 식별 하 고 인덱싱하는 데 사용 되는 필드를 나타냅니다. 아래 예제에서 장치는 IP 주소로 식별 됩니다. 특정 프로토콜에서는 더 복잡 한 인덱스를 사용할 수 있습니다. 예를 들어 다른 장치가 연결 되어 있는 경우 내부 경로를 추출 하는 방법을 알고 있습니다. 예를 들어 MODBUS 단위 ID는 IP 주소와 단위 식별자의 다른 조합으로 인덱스의 일부로 사용할 수 있습니다.
  - **firmware_fields**: 장치 메타 데이터 필드에 해당 하는 필드를 나타냅니다. 이 예제에서는 모델, 수정 버전 및 이름을 사용 합니다. 각 프로토콜은 자체 펌웨어 데이터를 정의할 수 있습니다.

## <a name="json-sample-with-firmware"></a>펌웨어가 포함 된 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>장치 특성 추출 

장치에서 인벤토리, 데이터 마이닝 및 기타 보고서의 장치에 사용할 수 있는 정보를 향상할 수 있습니다.

- 이름

- 유형

- Vendor

- 운영 체제

이를 위해서는 **속성** 속성을 사용 하 여 JSON 구성 파일을 업데이트 해야 합니다. 

기본 플러그 인을 작성 하 고 필요한 필드를 추출 하 여이 작업을 수행할 수 있습니다.

## <a name="properties-fields"></a>속성 필드

이 섹션에서는 JSON 속성 구성 필드에 대해 설명 합니다. 

**config_file** 

필드의 각 키를 처리 하는 방법을 정의 하는 파일 이름을 포함 `key` 합니다. 구성 파일 자체는 JSON 형식 이어야 하 고 플러그 인 프로토콜 폴더의 일부로 포함 되어야 합니다.

JSON의 각 키는 패킷에서이 키를 추출할 때 수행 해야 하는 작업 집합을 정의 합니다.

각 키에는 다음이 포함 될 수 있습니다.

- **패킷 데이터** -패킷에서 추출 된 데이터 (해당 데이터를 제공 하는 데 사용 되는 구현 필드)를 기반으로 업데이트 되는 속성을 나타냅니다.

- **정적 데이터** - `property-value` 업데이트 해야 하는 미리 정의 된 작업 집합을 나타냅니다.

이 파일에서 구성할 수 있는 속성은 다음과 같습니다. 

- **이름** -장치 이름을 나타냅니다.

- **유형** -장치 유형을 나타냅니다.

- **공급 업체** -장치 공급 업체를 나타냅니다.

- **설명** -장치 펌웨어 모델 ("모델" 보다 낮은 우선 순위)을 나타냅니다.

- 운영 **체제-장치 운영 체제를 나타냅니다** .

### <a name="fields"></a>필드

| 필드 | 설명 |
|--|--|
| key | 키를 나타냅니다. |
| 값 | 데이터를 제공 하기 위해 사용할 구현 필드를 나타냅니다. |
| is_static_key | `key`필드가 패킷의 값으로 파생 되거나 미리 정의 된 값 인지 여부를 나타냅니다. |

### <a name="working-with-static-keys-only"></a>정적 키만 사용

정적 키로 작업 하는 경우을 구성할 필요가 없습니다 `config.file` . JSON 파일만 구성할 수 있습니다.

## <a name="json-sample-with-properties"></a>속성이 포함 된 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>매핑 파일 만들기 (JSON)

및 업데이트 매핑 파일을 정의 하 여 엔터프라이즈 환경의 요구 사항에 맞게 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다. 코드를 변경 하거나 영향을 주지 않고 텍스트에 변경 내용을 쉽게 구현할 수 있습니다. 각 파일은 하나 이상의 필드를 매핑할 수 있습니다.

- 필드 값을 이름에 매핑합니다 (예: 1: Reset, 2: Start, 3: Stop).

- 여러 언어를 지원 하도록 텍스트를 매핑합니다.

두 가지 유형의 매핑 파일을 정의할 수 있습니다.

  - [단순 매핑 파일](#simple-mapping-file)입니다.

  - [종속성 매핑 파일](#dependency-mapping-file)입니다.

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="에테르 스코프 net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="처리 되지 않은 경고의 뷰입니다.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="알려진 정책 위반 목록입니다.":::

## <a name="file-naming-and-storage-requirements"></a>파일 이름 지정 및 저장소 요구 사항 

매핑 파일은 메타 데이터 폴더 아래에 저장 해야 합니다.

파일 이름은 JSON 구성 파일 ID와 일치 해야 합니다.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 구성 파일의 샘플입니다.":::

## <a name="simple-mapping-file"></a>단순 매핑 파일 

다음 샘플에서는 기본 JSON 파일을 키 값으로 제공 합니다.

허용 목록을 만들 때 매핑된 필드를 하나 이상 포함 합니다. 값은 숫자, 문자열 또는 모든 형식에서 매핑 파일에 표시 되는 서식 있는 텍스트로 변환 됩니다.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>종속성 매핑 파일 

파일이 종속성 파일 임을 나타내려면 키워드를 `dependency` 매핑 구성에 추가 합니다.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

이 파일에는 종속성 필드와 함수 필드 간의 매핑이 포함 되어 있습니다. 예를 들어, 함수와 sub 함수 사이에 있습니다. Sub 함수는 제공 된 함수에 따라 변경 됩니다.

이전에 구성 된 허용 목록에는 아래와 같이 종속성 구성이 없습니다.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

종속성은 특정 값 이나 필드를 기반으로 할 수 있습니다. 아래 예제에서는 필드를 기반으로 합니다. 값을 기준으로 하는 경우 매핑 파일에서 읽을 추출 값을 정의 합니다.

아래 예제에서는 필드의 동일한 값에 대 한 종속성이 다음과 같이 수행 됩니다.

예를 들어 하위 함수 5에서 의미는 함수에 따라 변경 됩니다.

  - 읽기 함수인 경우 5는 메모리 읽기를 의미 합니다.

  - 쓰기 함수인 경우 동일한 값을 사용 하 여 파일에서 읽습니다.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>샘플 파일

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>매핑이 포함 된 JSON 샘플

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>플러그 인 패키지, 업로드 및 모니터링 

이 섹션에서는 다음 작업을 수행하는 방법을 설명합니다.

  - 플러그 인을 패키지 합니다.

  - 플러그 인을 업로드 합니다.

  - 플러그 인을 모니터링 하 고 디버그 하 여 성능이 얼마나 잘 작동 하 고 있는지 평가 합니다.

플러그 인을 패키지 하려면:

1. **아티팩트** 를 파일에 추가할 수 있습니다 (, 라이브러리, config.js또는 메타 데이터) `tar.gz` .

1. 파일 확장명을로 변경 합니다 \<XXX.hdp> . 여기서은 \<XXX> 플러그 인의 이름입니다.

수평 콘솔에 로그인 하려면 다음을 수행 합니다.

1.  센서 CLI를 관리자, CyberX 또는 지원 사용자로 로그인 합니다.

2.  파일에서: `/var/cyberx/properties/horizon.properties` **ui. enabled** 속성을 **true** ()로 변경 합니다. `horizon.properties:ui.enabled=true`

3.  센서 콘솔에 로그인 합니다.

4.  주 메뉴에서 **가로선** 옵션을 선택 합니다.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="왼쪽 창에서 가로선 옵션을 선택 합니다.":::

    수평 콘솔이 열립니다.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="수평 콘솔 및 모든 플러그 인의 뷰입니다.":::

## <a name="plugins-pane"></a>플러그 인 창

플러그 인 창에는 다음 목록이 표시 됩니다.

  - 인프라 플러그 인: IoT 용 Defender를 사용 하 여 기본적으로 설치 된 인프라 플러그 인

  - 응용 프로그램 플러그 인: 응용 프로그램 플러그 인은 기본적으로 Defender 용 Defender 및 IoT 용 Defender에서 개발한 기타 플러그인 또는 외부 개발자에 의해 설치 됩니다.
    
설정/해제를 사용 하 여 업로드 된 플러그 인을 사용 하거나 사용 하지 않도록 설정 합니다.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP 설정/해제입니다.":::

### <a name="uploading-a-plugin"></a>플러그 인 업로드

플러그 인을 만들고 패키지 한 후에 IoT 센서 용 Defender에 업로드할 수 있습니다. 네트워크의 전체 범위를 확보 하려면 플러그 인을 조직의 각 센서에 업로드 해야 합니다.

업로드 하려면:

1.  센서에 로그인 합니다.


2. **업로드** 를 선택합니다.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="플러그 인을 업로드 합니다.":::

3. 플러그 인으로 이동 하 여 플러그 인 대화 상자로 끕니다. 접두사가 인지 확인 `.hdp` 합니다. 플러그 인이 로드 됩니다.

## <a name="plugin-status-overview"></a>플러그 인 상태 개요 

수평 콘솔 **개요** 창에서는 업로드 한 플러그 인에 대 한 정보를 제공 하 고 사용 하지 않도록 설정 하 고 사용 하도록 설정할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="수평 콘솔의 개요입니다.":::

| 필드 | Description |
|--|--|
| 애플리케이션 | 업로드 한 플러그 인의 이름입니다. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="On 및 off 스위치입니다."::: | 플러그 **인을 설정 하거나** **해제** 합니다. IoT 용 Defender는 플러그 인을 해제 하는 경우 플러그 인에 정의 된 프로토콜 트래픽을 처리 하지 않습니다. |
| 시간 | 데이터를 마지막으로 분석 한 시간입니다. 5 초 마다 업데이트 됩니다. |
| PPS | 초당 패킷 수입니다. |
| 대역폭 | 최근 5 초 내에 검색 된 평균 대역폭입니다. |
| Malforms | 잘못 된 유효성 검사는 프로토콜의 유효성을 검사 한 후에 사용 됩니다. 프로토콜을 기반으로 하는 패킷을 처리 하는 데 실패 하면 오류 응답이 반환 됩니다.   <br><br>이 열은 지난 5 초 동안의 malform 오류 수를 나타냅니다. 자세한 내용은 [잘못 된 형식의 코드 유효성 검사](#malformed-code-validations) 를 참조 하세요. |
| 경고 | 패킷은 구조와 사양과 일치 하지만 플러그 인 경고 구성에 따라 예기치 않은 동작이 발생 합니다. |
| 오류 | 기본 프로토콜 유효성 검사에 실패 한 패킷 수입니다. 패킷이 프로토콜 정의와 일치 하는지 확인 합니다. 여기에 표시 된 숫자는 지난 5 초 동안 검색 된 오류 수를 나타냅니다. 자세한 내용은 자세한 내용은 [온전성 코드 유효성 검사](#sanity-code-validations) 를 참조 하세요. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="모니터 아이콘입니다."::: | 플러그 인에 대해 검색 된 malform 및 경고에 대 한 세부 정보를 검토 합니다. |

## <a name="plugin-details"></a>플러그 인 정보

플러그 인에 대해 검색 된 *Malform* 및 *경고* 수를 분석 하 여 실시간 플러그 인 동작을 모니터링할 수 있습니다. 추가 조사를 위해 화면을 고정 하 고 내보낼 수 있는 옵션을 사용할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP 모니터 화면입니다.":::

모니터링할 대상:

개요에서 플러그 인의 모니터 단추를 선택 합니다.

다음 단계

[수평 API](references-horizon-api.md) 설정
