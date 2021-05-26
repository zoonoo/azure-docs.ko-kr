---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 6a4a3ee151c75afcd213dd1bac6d451c8857c4e7
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468100"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSM 클라이언트 이진 파일 다운로드 및 설치

bash 기반 셸에서 `curl`을 사용하여 OSM 릴리스를 다운로드한 후, 다음과 같이 `tar`로 추출합니다.

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.4

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`osm` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 이를 통해 AKS 클러스터에서 OSM을 관리할 수 있습니다. 다음 명령을 사용하여 bash 기반 셸에서 OSM `osm` 클라이언트 이진 파일을 설치합니다. 이 명령을 사용하여 `osm` 클라이언트 이진 파일을 `PATH`의 표준 사용자 프로그램 위치에 복사합니다.

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

다음 명령을 사용하여 `osm` 클라이언트 라이브러리가 경로 및 해당 버전 번호에 올바르게 추가되었는지 확인할 수 있습니다.

```
osm version
```
