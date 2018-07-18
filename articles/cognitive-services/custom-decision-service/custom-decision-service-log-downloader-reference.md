---
title: LogDownloader - Azure Cognitive Services | Microsoft Docs
description: Azure Custom Decision Service에서 생성된 로그 파일을 다운로드합니다.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376966"
---
# <a name="logdownloader"></a>LogDownloader

Azure Custom Decision Service에서 생성된 로그 파일을 다운로드하고 실험에서 사용되는 *.gz* 파일을 생성합니다.

## <a name="prerequisites"></a>필수 조건

- Python 3: 설치되어 사용자 경로에 있습니다. 큰 파일을 처리하려면 64비트 버전을 사용하는 것이 좋습니다.
- *Microsoft/mwt-ds* 리포지토리: [리포지토리를 복제](https://github.com/Microsoft/mwt-ds)합니다.
- *azure-storage-blob* 패키지: 설치 정보를 보려면 [Microsoft Azure Storage Library for Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi)으로 이동합니다.
- *mwt-ds/DataScience/ds.config*에 Azure Storage 연결 문자열을 입력합니다. *my_app_id: my_connectionString* 템플릿을 따릅니다. 여러 `app_id`를 지정할 수 있습니다. `LogDownloader.py`를 실행하는 경우 `ds.config`에서 입력 `app_id`를 찾을 수 없으면 `LogDownloader.py`는 `$Default` 연결 문자열을 사용합니다.

## <a name="usage"></a>사용 현황

`mwt-ds/DataScience`로 이동한 후 다음 코드에 설명된 것처럼 관련 인수를 사용해서 `LogDownloader.py`를 실행합니다.

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>매개 변수

| 입력 | 설명 | 기본값 |
| --- | --- | --- |
| `-h`, `--help` | 도움말 메시지를 표시하고 종료됩니다. | |
| `-a APP_ID`, `--app_id APP_ID` | 앱 ID(Azure Storage Blob 컨테이너 이름)입니다. | 필수 |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | 데이터 다운로드를 위한 기준 디렉터리입니다(하위 폴더가 생성됨).  | 필수 |
| `-s START_DATE`, `--start_date START_DATE` | *YYYY-MM-DD* 형식의 다운로드 시작 날짜(포함)입니다. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | *YYYY-MM-DD* 형식의 다운로드 종료 날짜(포함)입니다. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | 사용할 덮어쓰기 모드입니다. | |
| | `0`: 덮어쓰지 않습니다. Blob이 현재 사용되었는지 여부를 사용자에게 확인합니다. | 기본값 | |
| | `1`: 파일 크기가 서로 다른 경우 또는 Blob이 현재 사용 중인 경우 진행하는 방법을 사용자에게 확인합니다. | |
| | `2`: 항상 덮어씁니다. 현재 사용된 Blob을 다운로드합니다. | |
| | `3`: 덮어쓰지 않으며, 크기가 크면 확인하지 않고 추가합니다. 현재 사용된 Blob을 다운로드합니다. | |
| | `4`: 덮어쓰지 않으며, 크기가 크면 확인하지 않고 추가합니다. 현재 사용된 Blob을 건너뜁니다. | |
| `--dry_run` | 다운로드되었을 Blob을 다운로드하지 않고 인쇄합니다. | `False` |
| `--create_gzip` | Vowpal Wabbit에 대한 *gzip* 파일을 만듭니다. | `False` |
| `--delta_mod_t DELTA_MOD_T` | 파일이 현재 사용 중인지 여부를 감지하기 위한 기간(초)입니다. | `3600`초(`1`시간) |
| `--verbose` | 자세한 정보를 인쇄합니다. | `False` |
| `-v VERSION`, `--version VERSION` | 사용할 로그 다운로더 버전입니다. | |
| | `1`: 가공되지 않은 로그용입니다(이전 버전과의 호환성 용도로만 사용됨). | 사용되지 않음 |
| | `2`: 가공된 로그용입니다. | 기본값 |

### <a name="examples"></a>예

Azure Storage Blob 컨테이너에 있는 모든 데이터를 다운로드하는 시험 실행의 경우 다음 코드를 사용합니다.
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

2018년 1월 1일 이후에 생성된 로그만 `overwrite_mode=4`로 다운로드하려면 다음 코드를 사용합니다.
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

다운로드한 모든 파일을 병합하는 *gzip* 파일을 만들려면 다음 코드를 사용합니다.
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```