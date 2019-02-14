---
title: 포함 파일
description: 포함 파일
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 06651b06ae84934c16e9f1ac9f604abda8b65615
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55648626"
---
## <a name="open-cli-shell"></a>열기 CLI 셸

[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)을 사용하여 CLI 명령을 실행하는 것이 좋습니다. **Cloud Shell**은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있습니다. 작업 방식에 가장 적합한 셸 환경을 유연하게 선택할 수 있습니다. Linux 사용자는 Bash 환경을 선택할 수 있으며, Windows 사용자는 PowerShell을 선택할 수 있습니다.

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. 

### <a name="login"></a>로그인

CLI 셸 사용을 시작하려면(클라우드에서 또는 로컬로) `az login`을 실행하여 Azure로 연결을 만듭니다.

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않은 경우, 브라우저 페이지를 열고 명령줄의 지침에 따라 브라우저에서 https://aka.ms/devicelogin으로 이동한 후 권한 부여 코드를 입력합니다.

### <a name="specify-location-of-files"></a>파일의 위치 지정

여러 Media Services CLI 명령을 사용하면 파일 이름으로 매개 변수를 전달할 수 있습니다. **Cloud Shell**을 사용하는 경우 파일을 클라우드 드라이브에 업로드할 수 있습니다(Bash 또는 PowerShell 사용). 

![파일 업로드]

로컬 CLI 또는 **Cloud Shell**을 사용하는 경우 사용 중인 OS 또는 Cloud Shell(Bash 또는 PowerShell)에 따라 파일 경로를 지정해야 합니다. 다음은 몇 가지 예입니다.

상대 파일 경로(모든 OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac 및 Windows OS의 절대 파일 경로

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`


[파일 업로드]: ./media/media-services-cli/upload-download-files.png
