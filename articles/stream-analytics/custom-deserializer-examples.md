---
title: Azure 스트림 분석에서 .NET 사용자 지정 디직렬화기를 사용하여 모든 형식의 입력 읽기
description: 이 문서에서는 Azure Stream Analytics 클라우드 및 에지 작업에 대한 사용자 지정 .NET 역직렬화를 정의하는 직렬화 형식 및 인터페이스에 대해 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845268"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>.NET 사용자 지정 디직렬화기를 사용하여 모든 형식의 입력 읽기

.NET 사용자 지정 디serializer를 사용하면 Azure Stream Analytics 작업이 기본 제공 데이터 형식 의 세 가지 형식 이외의 형식에서 데이터를 읽을 수 [있습니다.](stream-analytics-parsing-json.md) 이 문서에서는 Azure Stream Analytics 클라우드 및 에지 작업에 대한 .NET 사용자 지정 디serializers를 정의하는 직렬화 형식 및 인터페이스에 대해 설명합니다. 프로토콜 버퍼 및 CSV 형식에 대한 예제 디직부타이저도 있습니다.

## <a name="net-custom-deserializer"></a>.NET 사용자 지정 직렬화기

다음 코드 샘플은 사용자 지정 디직렬화기를 `StreamDeserializer<T>`정의하고 구현하는 인터페이스입니다.

`UserDefinedOperator`은 모든 사용자 지정 스트리밍 연산자의 기본 클래스입니다. 디serializer와 `StreamingContext`함께 모든 문제를 디버깅 해야 하는 진단을 게시 하기 위한 메커니즘을 포함 하는 컨텍스트를 제공 하는 초기화 합니다.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

다음 코드 코드 조각은 스트리밍 데이터에 대한 직렬화입니다. 

건너뛸 수 있는 오류는 `IStreamingDiagnostics` '초기화 메서드'를 통과하여 `UserDefinedOperator`내보내야 합니다. 모든 예외는 오류로 처리되고 역직렬화가 다시 만들어집니다. 특정 수의 오류가 발생하면 작업이 실패한 상태로 이동합니다.

`StreamDeserializer<T>`스트림을 형식의 `T`개체로 디serialize합니다. 다음 조건이 충족되어야 합니다.

1. T는 클래스 또는 구조체입니다.
1. T의 모든 공용 필드는
    1. [sbyte, 바이트, 짧은, ushort, int, uint, 긴, DateTime, 문자열, 부동, 더블] 또는 해당 null able 등가물 중 하나.
    1. 동일한 규칙을 따르는 다른 구조체 또는 클래스입니다.
    1. 동일한 규칙을 `T2` 따르는 형식의 배열입니다.
    1. T2가 동일한 규칙을 따르는 IList.`T2`
    1. 재귀 형식이 없습니다.

매개 `stream` 변수는 직렬화된 개체를 포함하는 스트림입니다. `Deserialize``T` 인스턴스 컬렉션을 반환합니다.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`은 사용자 운영자에 대한 진단을 게시하기 위한 메커니즘을 포함하는 컨텍스트를 제공합니다.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`는 직렬화기, 역직렬화 및 사용자 정의 함수를 포함하여 사용자 정의 연산자에 대한 진단입니다.

`WriteError`을 사용하소서에는 진단 로그에 오류 메시지가 전송되고 진단 유틸리티에 오류가 전송됩니다.

`briefMessage`은 간단한 오류 메시지입니다. 이 메시지는 진단 유틸리티에 표시되며 디버깅을 위해 제품 팀에서 사용됩니다. 중요한 정보를 포함하지 말고 메시지를 200자 미만으로 유지하십시오.

`detailedMessage`은 저장소의 진단 로그에만 추가되는 자세한 오류 메시지입니다. 이 메시지는 2000자 미만이어야 합니다.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>역직렬화 예제

이 섹션에서는 Protobuf 및 CSV에 대한 사용자 지정 디직직렬화기를 작성하는 방법을 보여 주십니다. 이벤트 허브 캡처에 대한 AVRO 형식과 같은 추가 예제의 경우 [GitHub에서 Azure 스트림 분석을](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)방문하십시오.

### <a name="protocol-buffer-protobuf-format"></a>프로토콜 버퍼(프로토부프) 형식

프로토콜 버퍼 형식을 사용하는 예입니다.

다음 프로토콜 버퍼 정의를 가정합니다.

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

`protoc.exe` **Google.Protobuf.Tools** NuGet에서 실행하면 정의가 있는 .cs 파일이 생성됩니다. 생성된 파일은 여기에 표시되지 않습니다.

다음 코드 조각은 생성된 파일이 프로젝트에 포함되어 있다고 가정하는 디직렬화 자 구현입니다. 이 구현은 생성된 파일에 대한 씬 래퍼일 뿐입니다.

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

다음 코드 스니펫은 전파 오류를 보여 주는 간단한 CSV 역직렬화입니다.

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

## <a name="serialization-format-for-rest-apis"></a>REST API에 대한 직렬화 형식

모든 스트림 분석 입력에는 **직렬화 형식이**있습니다. 입력 옵션에 대한 자세한 내용은 [입력 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) 설명서를 참조하십시오.

다음 자바 스크립트 코드는 REST API를 사용할 때 .NET 디직렬화 직렬화 형식의 예입니다.

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

`serializationClassName`을 구현하는 클래스여야 `StreamDeserializer<T>`합니다. 이것은 다음 섹션에서 설명합니다.

## <a name="region-support"></a>지역 지원

이 기능은 다음 지역에서 사용할 수 있습니다.

* 미국 중서부
* 북유럽
* 미국 동부
* 미국 서부
* 미국 동부 2
* 서유럽

추가 지역에 대한 [지원을 요청할](https://aka.ms/ccodereqregion) 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>모든 Azure 지역에서 이 기능을 언제 사용할 수 있습니까?

이 기능은 [6개 지역에서](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)사용할 수 있습니다. 다른 지역에서 이 기능을 사용하려는 경우 [요청을 제출할](https://aka.ms/ccodereqregion)수 있습니다. 모든 Azure 지역에 대한 지원은 로드맵에 있습니다.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue 함수와 유사한 내 입력에서 메타데이터속성값에 액세스할 수 있습니까?

이제는 이 기능이 지원되지 않습니다. 이 기능이 필요한 경우 [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)에서 이 요청에 투표할 수 있습니다.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>다른 사람들이 혜택을 누릴 수 있도록 내 직렬화 구현을 커뮤니티와 공유할 수 있습니까?

디serializer를 구현하면 커뮤니티와 공유하여 다른 사람들을 도울 수 있습니다. [Azure 스트림 분석 GitHub 리포지토리에](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)코드를 제출합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 스트림 분석 클라우드 작업에 대한 .NET 사용자 지정 디serializers](custom-deserializer.md)
