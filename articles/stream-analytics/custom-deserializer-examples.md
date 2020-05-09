---
title: Azure Stream Analytics에서 .NET 사용자 지정 deserializers를 사용 하 여 모든 형식의 입력 읽기
description: 이 문서에서는 Azure Stream Analytics 클라우드 및에 지 작업에 대 한 사용자 지정 .NET deserializers를 정의 하는 serialization 형식 및 인터페이스에 대해 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 5cde80bf3205557884dfe8f2b8f5e79031bbca69
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612064"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>.NET 사용자 지정 deserializers를 사용 하 여 모든 형식의 입력 읽기

.NET 사용자 지정 deserializers를 사용 하면 Azure Stream Analytics 작업을 통해 세 가지 [기본 제공 데이터 형식](stream-analytics-parsing-json.md)외부의 형식에서 데이터를 읽을 수 있습니다. 이 문서에서는 Azure Stream Analytics 클라우드 및에 지 작업에 대해 .NET 사용자 지정 deserializers를 정의 하는 serialization 형식 및 인터페이스에 대해 설명 합니다. 프로토콜 버퍼 및 CSV 형식에 대 한 예제 deserializers도 있습니다.

## <a name="net-custom-deserializer"></a>.NET 사용자 지정 역직렬 변환기

다음 코드 샘플은 사용자 지정 역직렬 변환기를 정의 하 고을 `StreamDeserializer<T>`구현 하는 인터페이스입니다.

`UserDefinedOperator`는 모든 사용자 지정 스트리밍 연산자의 기본 클래스입니다. 역직렬화는 `StreamingContext`역직렬 변환기와 관련 된 문제를 디버깅 하는 데 필요한 게시 진단을 위한 메커니즘이 포함 된 컨텍스트를 제공 합니다.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

다음 코드 조각은 스트리밍 데이터에 대 한 deserialization입니다. 

Skippable `UserDefinedOperator`의 Initialize 메서드를 사용 `IStreamingDiagnostics` 하 여 오류를 내보내야 합니다. 모든 예외가 오류로 처리 되 고 역직렬 변환기가 다시 생성 됩니다. 특정 오류가 발생 하면 작업이 실패 한 상태가 됩니다.

`StreamDeserializer<T>`스트림을 형식의 `T`개체로 deserialize 합니다. 다음 조건이 충족되어야 합니다.

1. T는 클래스 또는 구조체입니다.
1. T의 모든 public 필드는
    1. [Sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] 또는 해당 하는 nullable 해당 항목 중 하나입니다.
    1. 동일한 규칙을 수행 하는 다른 구조체 또는 클래스입니다.
    1. 동일한 규칙을 `T2` 따르는 형식의 배열입니다.
    1. IList`T2` where T2는 동일한 규칙을 따릅니다.
    1. 에는 재귀 형식이 없습니다.

매개 변수 `stream` 는 serialize 된 개체를 포함 하는 스트림입니다. `Deserialize``T` 인스턴스의 컬렉션을 반환 합니다.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`사용자 운영자 용 진단 게시 메커니즘을 포함 하는 컨텍스트를 제공 합니다.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`는 serializer, 역직렬 변환기 및 사용자 정의 함수를 포함 하는 사용자 정의 연산자에 대 한 진단입니다.

`WriteError`리소스 로그에 오류 메시지를 기록 하 고 오류를 진단으로 보냅니다.

`briefMessage`는 간단한 오류 메시지입니다. 이 메시지는 진단에 표시 되며 제품 팀에서 디버깅 목적으로 사용 됩니다. 중요 한 정보를 포함 하지 않고 메시지를 200 자 미만으로 유지 합니다.

`detailedMessage`는 저장소의 리소스 로그에만 추가 되는 자세한 오류 메시지입니다. 이 메시지는 2000 자 미만 이어야 합니다.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>역직렬 변환기 예제

이 섹션에서는 Protobuf 및 CSV에 대 한 사용자 지정 deserializers를 작성 하는 방법을 보여 줍니다. 이벤트 허브 캡처에 대 한 AVRO 형식과 같은 추가 예제는 [GitHub의 Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)을 참조 하세요.

### <a name="protocol-buffer-protobuf-format"></a>프로토콜 버퍼 (Protobuf) 형식

프로토콜 버퍼 형식을 사용 하는 예제입니다.

다음 프로토콜 버퍼 정의를 가정 합니다.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Protobuf `protoc.exe` NuGet에서 **Google.Protobuf.Tools** 실행 하면 정의를 사용 하 여 .cs 파일이 생성 됩니다. 생성 된 파일은 여기에 표시 되지 않습니다.

다음 코드 조각은 생성 된 파일이 프로젝트에 포함 된 것으로 가정 하는 역직렬 변환기 구현입니다. 이 구현은 생성 된 파일에 대 한 씬 래퍼입니다.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

다음 코드 조각에서는 오류 전파도 보여 주는 간단한 CSV 역직렬 변환기입니다.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST Api에 대 한 Serialization 형식

모든 Stream Analytics 입력에는 **serialization 형식이**있습니다. 입력 옵션에 대 한 자세한 내용은 [입력 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) 설명서를 참조 하세요.

다음 Javascript 코드는 REST API를 사용 하는 경우 .NET 역직렬 변환기 직렬화 형식의 예입니다.

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`는를 구현 `StreamDeserializer<T>`하는 클래스 여야 합니다. 이에 대해서는 다음 섹션에서 설명 합니다.

## <a name="region-support"></a>지역 지원

이 기능은 다음 지역에서 제공 됩니다.

* 미국 중서부
* 북유럽
* 미국 동부
* 미국 서부
* 미국 동부 2
* 서유럽

추가 영역에 대 한 [지원을 요청할](https://aka.ms/ccodereqregion) 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>언제 모든 Azure 지역에서이 기능을 사용할 수 있나요?

이 기능은 [6 개 지역](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)에서 사용할 수 있습니다. 다른 지역에서이 기능을 사용 하는 데 관심이 있는 경우 [요청을 제출할](https://aka.ms/ccodereqregion)수 있습니다. 모든 Azure 지역에 대 한 지원은 로드맵에 있습니다.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue 함수와 유사한 내 입력에서 MetadataPropertyValue에 액세스할 수 있나요?

이제는 이 기능이 지원되지 않습니다. 이 기능이 필요한 경우 [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)에서이 요청에 대해 응답할 수 있습니다.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>다른 사용자가 혜택을 받을 수 있도록 내 역직렬 변환기 구현을 커뮤니티와 공유할 수 있나요?

역직렬 변환기를 구현한 후에는 다른 사용자가 커뮤니티와 공유 하 여 도울 수 있습니다. [Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)리포지토리에 코드를 제출 합니다.

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Stream Analytics에서 사용자 지정 deserializers를 사용 하는 다른 제한은 무엇 인가요?

입력이 MapField type을 포함 하는 스키마를 사용 하는 Protobuf 형식인 경우에는 사용자 지정 역직렬 변환기를 구현할 수 없습니다. 이 유형을 앞으로 지원 하기 위해 노력 하 고 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 클라우드 작업에 대 한 .NET 사용자 지정 deserializers](custom-deserializer.md)
