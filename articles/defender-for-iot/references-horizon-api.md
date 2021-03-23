---
title: Horizon API
description: 이 가이드에서는 일반적으로 사용 되는 수평선 메서드를 설명 합니다.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786790"
---
# <a name="horizon-api"></a>Horizon API 

이 가이드에서는 일반적으로 사용 되는 수평선 메서드를 설명 합니다.

### <a name="getting-more-information"></a>추가 정보 보기

IoT 플랫폼용 및 수평 사용에 대 한 자세한 내용은 다음 정보를 참조 하세요.

- 수평 ODE (Open Development Environment) SDK의 경우 Defender에 IoT 담당자에 게 문의 하세요.
- 지원 및 문제 해결 정보는에 문의 하세요 <support@cyberx-labs.com> .

- IoT 용 Defender 콘솔에서 iot 용 Defender 사용자 가이드에 액세스 하려면를 선택 하 :::image type="icon" source="media/references-horizon-api/profile.png"::: 고 **사용자 가이드 다운로드** 를 선택 합니다.


## `horizon::protocol::BaseParser`

모든 플러그 인에 대 한 추상입니다. 이는 다음과 같은 두 가지 방법으로 구성 됩니다.

- 위에 정의 된 플러그 인 필터를 처리 합니다. 이 방법에서는 파서와 통신 하는 방법을 알 수 있습니다.
- 실제 데이터를 처리 하기 위한입니다.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

플러그 인에 대해 호출 되는 첫 번째 함수는 수평으로 파서 인스턴스를 만들어이를 인식 하 고 등록 합니다.

### <a name="parameters"></a>매개 변수 

없음

### <a name="return-value"></a>반환 값

파서 인스턴스에 shared_ptr 합니다.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

이 함수는 위에 등록 된 각 플러그 인에 대해 호출 됩니다. 

대부분의 경우이는 비어 있습니다. 오류가 발생 한 것을 파악 하기 위해 마감일에 대 한 예외를 throw 합니다.

### <a name="parameters"></a>매개 변수 

- 등록 하려는 다른 플러그 인의 config.js에 정의 된 dissect_as 구조를 포함 하는 맵입니다.

### <a name="return-value"></a>반환 값 

일종의 uint64_t으로 처리 되는 등록 인 uint64_t의 배열입니다. 즉, 맵에 값이 uin64_t 되는 포트 목록이 있습니다.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Main 함수입니다. 특히 새 패킷이 파서에 도달할 때마다 플러그 인의 논리입니다. 이 함수는 호출 되며 여기에서 패킷 처리와 관련 된 모든 항목을 수행 해야 합니다.

### <a name="considerations"></a>고려 사항

다른 스레드에서이 함수를 호출할 수 있으므로 플러그 인은 스레드로부터 안전 해야 합니다. 적절 한 방법은 스택에 모든 항목을 정의 하는 것입니다.

### <a name="parameters"></a>매개 변수

- 데이터를 저장 하 고 ILayer 및 필드와 같은 SDK 관련 개체를 만드는 것을 담당 하는 SDK 컨트롤 단위입니다.
- 원시 패킷의 데이터를 읽기 위한 도우미입니다. config.js에서 정의한 바이트 순서를 사용 하 여 이미 설정 되어 있습니다.

### <a name="return-value"></a>반환 값 

처리 결과입니다. *성공*, *잘못* 된 또는 *온전성* 중 하나일 수 있습니다.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

는 처리를 sanitation 실패로 표시 합니다. 즉, 현재 프로토콜에서 패킷을 인식할 수 없으며, 동일한 필터에 등록 된 경우에는 다른 파서로 전달 해야 합니다.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

생성자

### <a name="parameters"></a>매개 변수 

- config.js에 정의 된 대로 기록에 사용 되는 오류 코드를 정의 합니다.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

잘못 된 결과입니다. 이미 패킷을 프로토콜로 인식 했지만 일부 유효성 검사가 잘못 되었거나 (예약 비트가 켜져 있거나 일부 필드가 누락 됨)

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

생성자

### <a name="parameters"></a>매개 변수  

- config.js에 정의 된 오류 코드입니다.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

성공적인 처리를 위해 메시지를 표시 합니다. 성공적으로 완료 되 면 패킷이 수락 되 고 데이터가 microsoft에 속하고 모든 데이터가 추출 됩니다.

## `horizon::protocol::SuccessResult()`

생성자입니다. 기본 성공적인 결과를 만들었습니다. 즉, 패킷에 대 한 방향 또는 다른 메타 데이터를 알 수 없습니다.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

생성자입니다.

### <a name="parameters"></a>매개 변수 

- 패킷의 방향 (식별 된 경우)입니다. 값은 *요청* 또는 *응답이* 될 수 있습니다.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

생성자입니다.

### <a name="parameters"></a>매개 변수

- 패킷 방향 (확인 된 경우)은 *요청*, *응답* 일 수 있습니다.
- 경고. 이러한 이벤트는 실패 하지 않지만, 마감일에는 알림이 표시 됩니다.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

생성자입니다.

### <a name="parameters"></a>매개 변수 

-  경고. 이러한 이벤트는 실패 하지 않지만, 마감일에는 알림이 표시 됩니다.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

문자열 기반 참조를 필드 이름 (예: function_code)으로 변환 합니다.

### <a name="parameters"></a>매개 변수 

- 변환할 문자열입니다.

### <a name="return-value"></a>반환 값

- 문자열에서 만든 HorizonID입니다.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

는 플러그 인에서 일부 데이터를 저장 하려고 한다는 것을 알 수 있도록 새 계층을 만듭니다. 사용 해야 하는 기본 저장소 단위입니다.

### <a name="return-value"></a>반환 값

만든 레이어에 대 한 참조로, 여기에 데이터를 추가할 수 있습니다.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

ILayer의 경우와 같이 다른 개체에 대해 필드를 만드는 일을 담당 하는 필드 관리 개체를 가져옵니다.

### <a name="return-value"></a>반환 값

관리자에 대 한 참조입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

계층에서 요청 된 ID를 사용 하 여 64 비트의 새 숫자 필드를 만듭니다.

### <a name="parameters"></a>매개 변수 

- 이전에 만든 계층입니다.
- **HORIZON_FIELD** 매크로에 의해 생성 된 HorizonID입니다.
- 저장 하려는 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

계층에서 요청 된 ID를 사용 하 여의 새 문자열 필드를 만듭니다. 메모리가 이동 되므로 주의 해야 합니다. 이 값은 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수  

- 이전에 만든 계층입니다.
- **HORIZON_FIELD** 매크로에 의해 생성 된 HorizonID입니다.
- 저장 하려는 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

계층에서 요청 된 ID를 사용 하 여의 새 원시 값 (바이트 배열) 필드를 만듭니다. 메모리가 이동 되므로 주의 해야 합니다 .이 값을 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 계층입니다.
- **HORIZON_FIELD** 매크로에 의해 생성 된 HorizonID입니다.
- 저장 하려는 원시 값입니다.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

요청 된 ID를 사용 하 여 지정 된 형식의 계층에 배열 값 (array) 필드를 만듭니다.

### <a name="parameters"></a>매개 변수

- 이전에 만든 계층입니다.
- **HORIZON_FIELD** 매크로에 의해 생성 된 HorizonID입니다.
- 배열 내에 저장 될 값의 형식입니다.

### <a name="return-value"></a>반환 값

값을 추가 해야 하는 배열에 대 한 참조입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

앞에서 만든 배열에 새 정수 값을 추가 합니다.

### <a name="parameters"></a>매개 변수

- 앞에서 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

이전에 만든 배열에 새 문자열 값을 추가 합니다. 메모리가 이동 되므로 주의 해야 합니다 .이 값을 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 앞에서 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

이전에 만든 배열에 새 원시 값을 추가 합니다. 메모리가 이동 되므로 주의 해야 합니다 .이 값을 다시 사용할 수 없습니다.

### <a name="parameters"></a>매개 변수

- 앞에서 만든 배열입니다.
- 배열에 저장할 원시 값입니다.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

버퍼에 X 바이트가 이상 포함 되어 있는지 확인 합니다.

### <a name="parameters"></a>매개 변수

존재 해야 하는 바이트 수입니다.

### <a name="return-value"></a>반환 값

버퍼에 X 바이트가 이상 포함 되어 있으면 True입니다. 그렇지 않으면 `False`입니다.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

바이트 순서에 따라 버퍼에서 uint8 값 (1 바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

바이트 순서에 따라 버퍼에서 uint16 값 (2 바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

바이트 순서에 따라 버퍼에서 uint32 값 (4 바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

바이트 순서에 따라 버퍼에서 uint64 값 (8 바이트)을 읽습니다.

### <a name="return-value"></a>반환 값

버퍼에서 읽은 값입니다.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

는 지정 된 크기의 미리 할당 된 메모리를 읽고 실제로 데이터를 메모리 영역에 복사 합니다.

### <a name="parameters"></a>매개 변수 

- 데이터를 복사할 대상 메모리 영역입니다.
- 메모리 영역의 크기입니다 .이 매개 변수는 복사 될 바이트 수를 정의 하기도 합니다.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

버퍼에서 문자열로 읽습니다.

### <a name="parameters"></a>매개 변수 

- 읽어야 하는 바이트 수입니다.

### <a name="return-value"></a>반환 값

문자열의 메모리 영역에 대 한 참조입니다.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

버퍼에 남아 있는 바이트 수를 알려 줍니다.

### <a name="return-value"></a>반환 값

버퍼의 남은 크기입니다.

## `void horizon::general::IDataBuffer::skip(size_t)`

버퍼에서 X 바이트를 건너뜁니다.

### <a name="parameters"></a>매개 변수

- 건너뛸 바이트 수입니다.
