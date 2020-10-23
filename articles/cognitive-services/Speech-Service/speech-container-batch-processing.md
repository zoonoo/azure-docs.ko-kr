---
title: 음성 컨테이너 용 Batch 처리 키트
titleSuffix: Azure Cognitive Services
description: 일괄 처리 키트를 사용 하 여 음성 컨테이너 요청의 크기를 조정 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: a3b2a9db688104c168017863910745427a3a68f9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425798"
---
# <a name="batch-processing-kit-for-speech-containers"></a>음성 컨테이너 용 Batch 처리 키트

일괄 처리 키트를 사용 하 여 음성 컨테이너에서 작업을 보완 하 고 확장 합니다. 이 오픈 소스 유틸리티는 컨테이너로 사용할 수 있으며, 많은 수의 온-프레미스 및 클라우드 기반 음성 컨테이너 끝점에서 많은 오디오 파일에 대 한 일괄 처리 기록을 용이 하 게 합니다. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="일괄 처리 키트 컨테이너 워크플로 예제를 보여 주는 다이어그램입니다.":::

Batch 키트 컨테이너는 [GitHub](https://github.com/microsoft/batch-processing-kit) 및   [Docker 허브](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)에서 무료로 제공 됩니다. 사용 중인 음성 컨테이너에 대해서만 [요금이 청구](speech-container-howto.md#billing) 됩니다.

| 기능  | 설명  |
|---------|---------|
| Batch 오디오 파일 배포     | 많은 수의 파일을 온-프레미스 또는 클라우드 기반 음성 컨테이너 끝점으로 자동으로 디스패치합니다. 파일은 네트워크 파일 시스템을 비롯 한 모든 POSIX 규격 볼륨에 있을 수 있습니다.       |
| Speech SDK 통합 | N-best 가설, diarization, language, 비속어 마스킹을 포함 하 여 일반 플래그를 음성 SDK에 전달 합니다.  |
|실행 모드     | 백그라운드에서 지속적으로 일괄 처리 클라이언트를 실행 하거나 오디오 파일용 HTTP 끝점을 만듭니다.         |
| 내결함성 | 진행 상황을 잃지 않고 자동으로 다시 시도 하 여 기록을 계속 하 고, 오류와 오류를 구분 하 여에서 다시 시도할 수 없습니다. |
| 끝점 가용성 검색 | 끝점을 사용할 수 없게 되 면 batch 클라이언트는 다른 컨테이너 끝점을 사용 하 여 계속 찍으면 되므로 간편 됩니다. 다시 사용할 수 있게 되 면 클라이언트는 자동으로 끝점 사용을 시작 합니다.   |
| 엔드포인트 핫 스왑 | 일괄 처리 진행률을 중단 하지 않고 런타임 중에 음성 컨테이너 끝점을 추가, 제거 또는 수정 합니다. 업데이트는 즉시 실행 됩니다. |
| 실시간 로깅 | 각 오디오 파일에 대해 음성 SDK 로그 파일을 사용 하 여 시도 된 요청, 타임 스탬프 및 실패에 대 한 실시간 로깅 |

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 최신 batch 키트 컨테이너를 다운로드 합니다.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>엔드포인트 구성

Batch 클라이언트는 온-프레미스 컨테이너 끝점을 지정 하는 yaml 구성 파일을 사용 합니다. 다음 예는 `/mnt/my_nfs/config.yaml` 아래 예제에서 사용 되는에 쓸 수 있습니다. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

이 yaml 예는 세 개의 호스트에서 3 개의 음성 컨테이너를 지정 합니다. 첫 번째 호스트는 IPv4 주소로 지정 되 고, 두 번째 호스트는 배치 클라이언트와 동일한 VM에서 실행 되며, 세 번째 컨테이너는 다른 VM의 DNS 호스트 이름으로 지정 됩니다. `concurrency`값은 동일한 컨테이너에서 실행할 수 있는 최대 동시 파일 수를 지정 합니다. `rtf`(실시간 요소) 값은 선택 사항이 며 성능을 조정 하는 데 사용할 수 있습니다.
 
Batch 클라이언트는 컨테이너를 다시 시작 하거나 네트워킹 문제 등을 사용 하 여 끝점을 사용할 수 없게 되는 경우와 다시 사용할 수 있게 되 면 동적으로 감지할 수 있습니다. 기록 요청은 사용할 수 없는 컨테이너로 보내지지 않으며 클라이언트는 사용 가능한 다른 컨테이너를 계속 사용 합니다. 일괄 처리의 진행률을 중단 하지 않고 언제 든 지 끝점을 추가, 제거 또는 편집할 수 있습니다.


## <a name="run-the-batch-processing-container"></a>일괄 처리 컨테이너를 실행 합니다.
  
> [!NOTE] 
> * 이 예에서는 `/my_nfs` 구성 파일 및 입력, 출력 및 로그 디렉터리에 동일한 디렉터리 ()를 사용 합니다. 이러한 폴더에 대해 호스트 되거나 NFS로 탑재 된 디렉터리를 사용할 수 있습니다.
> * 에서 클라이언트를 실행 `–h` 하면 사용 가능한 명령줄 매개 변수와 해당 기본값이 나열 됩니다. 
> * 일괄 처리 컨테이너는 Linux 에서만 지원 됩니다.

Docker 명령을 사용 `run` 하 여 컨테이너를 시작 합니다. 그러면 컨테이너 내에서 대화형 셸이 시작 됩니다.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Batch 클라이언트를 실행 하려면 다음을 수행 합니다.  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

단일 명령으로 batch 클라이언트 및 컨테이너를 실행 하려면 다음을 수행 합니다.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


클라이언트가 실행을 시작 합니다. 이전 실행에서 오디오 파일이 이미 transcribed 된 경우 클라이언트는 자동으로 파일을 건너뜁니다. 일시적인 오류가 발생 하는 경우 자동으로 다시 시도 하 여 파일이 전송 되며, 클라이언트에서 다시 시도할 오류를 구분할 수 있습니다. 기록 오류가 발생 하면 클라이언트는 기록을 계속 하 고 진행률을 잃지 않고 다시 시도할 수 있습니다.  

## <a name="run-modes"></a>실행 모드 

일괄 처리 키트는 매개 변수를 사용 하 여 세 가지 모드를 제공 합니다 `--run-mode` .

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` 모드는 입력 디렉터리 및 선택적 파일 목록에서 하나의 오디오 파일 일괄 처리를 출력 폴더로 speech 합니다.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="일괄 처리 키트 컨테이너 워크플로 예제를 보여 주는 다이어그램입니다.":::

1. Batch 클라이언트가 파일에서 사용할 음성 컨테이너 끝점을 정의 합니다 `config.yaml` . 
2. 입력 디렉터리에 기록을 위한 오디오 파일을 저장 합니다.  
3. 파일 처리를 시작 하는 디렉터리에서 컨테이너를 호출 합니다. 동일한 출력 디렉터리 (동일한 파일 이름 및 체크섬)를 사용 하 여 이전 실행에서 오디오 파일이 이미 transcribed 된 경우 클라이언트는 파일을 건너뜁니다. 
4. 파일은 1 단계의 컨테이너 끝점으로 발송 됩니다.
5. 로그 및 음성 컨테이너 출력이 지정 된 출력 디렉터리에 반환 됩니다. 

#### <a name="daemon"></a>[데몬](#tab/daemon)

> [!TIP]
> 여러 파일을 동시에 입력 디렉터리에 추가 하는 경우에는 일정 한 간격으로 추가 하는 대신 성능을 향상 시킬 수 있습니다.

`DAEMON` 모드는 지정 된 폴더에 있는 기존 파일을 speech 새 오디오 파일이 추가 될 때 지속적으로 speech 합니다.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="일괄 처리 키트 컨테이너 워크플로 예제를 보여 주는 다이어그램입니다.":::

1. Batch 클라이언트가 파일에서 사용할 음성 컨테이너 끝점을 정의 합니다 `config.yaml` . 
2. 입력 디렉터리에서 컨테이너를 호출 합니다. Batch 클라이언트는 들어오는 파일의 디렉터리 모니터링을 시작 합니다. 
3. 입력 디렉터리로 연속 오디오 파일 배달을 설정 합니다. 동일한 출력 디렉터리 (동일한 파일 이름 및 체크섬)를 사용 하 여 이전 실행에서 오디오 파일이 이미 transcribed 된 경우 클라이언트는 파일을 건너뜁니다. 
4. 파일 쓰기 또는 POSIX 신호를 감지 하면 컨테이너는 응답 하도록 트리거됩니다.
5. 파일은 1 단계의 컨테이너 끝점으로 발송 됩니다.
6. 로그 및 음성 컨테이너 출력이 지정 된 출력 디렉터리에 반환 됩니다. 

#### <a name="rest"></a>[REST (영문)](#tab/rest)

`REST` 모드는 오디오 파일 일괄 처리 전송, 상태 검사 및 긴 폴링을 위한 기본 HTTP 끝점 집합을 제공 하는 API 서버 모드입니다. 또한 python 모듈 확장을 사용 하 여 프로그래밍 방식으로 사용 하거나 하위 모듈로 가져올 수 있습니다.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="일괄 처리 키트 컨테이너 워크플로 예제를 보여 주는 다이어그램입니다.":::

1. Batch 클라이언트가 파일에서 사용할 음성 컨테이너 끝점을 정의 합니다 `config.yaml` . 
2. API 서버 끝점 중 하나로 HTTP 요청 요청을 보냅니다. 
        
    |엔드포인트  |설명  |
    |---------|---------|
    |`/submit`     | 새 일괄 처리 요청을 만들기 위한 끝점입니다.        |
    |`/status`     | 일괄 처리 요청 상태를 확인 하기 위한 끝점입니다. 일괄 처리가 완료 될 때까지 연결이 열린 상태로 유지 됩니다.       |
    |`/watch`     | 일괄 처리가 완료 될 때까지 HTTP 긴 폴링을 사용 하기 위한 끝점입니다.        |

3. 오디오 파일은 입력 디렉터리에서 업로드 됩니다. 동일한 출력 디렉터리 (동일한 파일 이름 및 체크섬)를 사용 하 여 이전 실행에서 오디오 파일이 이미 transcribed 된 경우 클라이언트는 파일을 건너뜁니다. 
4. 요청을 끝점에 보내는 경우 `/submit` 파일은 1 단계의 컨테이너 끝점으로 발송 됩니다.
5. 로그 및 음성 컨테이너 출력이 지정 된 출력 디렉터리에 반환 됩니다. 

---

## <a name="logging"></a>로깅

> [!NOTE]
> 일괄 처리 클라이언트는 *실행 로그* 파일이 너무 클 경우 정기적으로 덮어쓸 수 있습니다.

클라이언트는 docker 명령의 인수로 지정 된 디렉터리에 *실행 .log* 파일을 만듭니다 `-log_folder` `run` . 로그는 기본적으로 디버그 수준에서 캡처됩니다. 동일한 로그가에 전송 되 `stdout/stderr` 고 인수에 따라 필터링 됩니다 `-log_level` . 이 로그는 디버깅에만 필요 하며, 지원에 대 한 추적을 보내야 하는 경우에만 필요 합니다. 로깅 폴더에는 각 오디오 파일에 대 한 음성 SDK 로그도 포함 됩니다.

로 지정 된 출력 디렉터리에는 `-output_folder` 파일 *에 대 한run_summary.js*이 포함 됩니다   .이 파일은 30 초 마다 또는 새로운 새 최종 스크립트가 완료 될 때마다 정기적으로 다시 작성 됩니다. 일괄 처리가 진행 되는 동안이 파일을 사용 하 여 진행률을 확인할 수 있습니다. 또한 일괄 처리가 완료 되 면 모든 파일의 최종 실행 통계 및 최종 상태를 포함 합니다. 프로세스가 완전히 종료 되 면 일괄 처리가 완료 됩니다. 

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](speech-container-howto.md)
