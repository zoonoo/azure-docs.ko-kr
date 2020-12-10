---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6011bf90d5a97dcc027f8a9a0916c28226c5c354
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584475"
---
## <a name="download-and-install"></a>다운로드 및 설치

#### <a name="windows-install"></a>[Windows 설치](#tab/windowsinstall)

Windows에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다. 처음 설치하려면 다시 시작해야 할 수 있습니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. `spx-zips`를 추출한 디렉터리로 이동합니다. 이 폴더에는 다양한 플랫폼의 Speech CLI용 프로그램 파일이 포함되어 있습니다. 
4. 플랫폼에 대한 파일을 추출합니다(.NET Framework 4.7의 경우 `spx-net471` 또는 x64 CPU의 .NET Core 3.0의 경우 `spx-netcore-win-x64`). 이 디렉터리에서 `spx`를 실행한다는 점에 유의하세요.

### <a name="run-the-speech-cli"></a>Speech CLI 실행

1. 명령 프롬프트 또는 PowerShell을 연 다음, Speech CLI를 추출한 디렉터리로 이동합니다.  
2. `spx`를 입력하여 Speech CLI에 대한 도움말 명령을 표시합니다.

> [!NOTE]
> Powershell은 명령을 찾을 때 로컬 디렉터리를 확인하지 않습니다. Powershell에서 디렉터리를 `spx` 위치로 변경하고 `.\spx`를 입력하여 도구를 호출합니다.
> 경로에 이 디렉터리를 추가하면 Powershell 및 Windows 명령 프롬프트가 `.\` 접두사를 포함하지 않고 모든 디렉터리에서 `spx`를 찾습니다.

### <a name="font-limitations"></a>글꼴 제한 사항

Windows에서 Speech CLI는 로컬 컴퓨터에서 명령 프롬프트에 사용할 수 있는 글꼴만 표시할 수 있습니다.
[Windows 터미널](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701)은 Speech CLI에서 대화형으로 생성된 모든 글꼴을 지원합니다.

파일로 출력하면 메모장과 같은 텍스트 편집기나 Microsoft Edge와 같은 웹 브라우저도 모든 글꼴을 표시할 수 있습니다.

#### <a name="linux-install"></a>[Linux 설치](#tab/linuxinstall)

x64 CPU에서 Linux에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)을 설치합니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. 다운로드에서 추출한 루트 디렉터리 `spx-zips`로 이동하여 `spx-netcore-30-linux-x64`를 새 `~/spx` 디렉터리로 추출합니다.
4. 터미널에서 다음 명령을 입력합니다.
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

`spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

#### <a name="docker-install-windows-linux-macos"></a>[Docker 설치(Windows, Linux, macOS)](#tab/dockerinstall)

Docker 컨테이너에서 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. 아직 설치되지 않은 경우 플랫폼에 <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop을 설치<span class="docon docon-navigate-external x-hidden-focus"></span></a>합니다.
2. 새 명령 프롬프트 또는 터미널에서 다음 명령을 입력합니다. 
   ```shell   
   docker pull msftspeech/spx
   ```
3. 다음 명령을 입력합니다. Speech CLI에 대한 도움말 정보가 표시되어야 합니다. 
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>컨테이너에 디렉터리 탑재

Speech CLI 도구는 구성 설정을 파일로 저장하고 명령(도움말 명령 제외)을 수행할 때 이러한 파일을 로드합니다.
Docker 컨테이너 내에서 Speech CLI를 사용하는 경우 도구에서 구성 설정을 저장하거나 찾을 수 있도록 컨테이너에서 로컬 디렉터리를 탑재해야 합니다. 그러면 도구에서 음성의 오디오 파일 등 명령에 필요한 모든 파일을 읽거나 쓸 수 있습니다.

Windows에서 다음 명령을 입력하여 컨테이너 내에서 사용할 수 있는 로컬 디렉터리 Speech CLI를 만듭니다.

`mkdir c:\spx-data`

또는 Linux 또는 macOS에서 터미널에 다음 명령을 입력하여 디렉터리를 만들고 절대 경로를 확인합니다.

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Speech CLI를 호출할 때 절대 경로를 사용합니다.

### <a name="run-speech-cli-in-the-container"></a>컨테이너에서 Speech CLI 실행

이 설명서에서는 비 Docker 설치에서 사용되는 Speech CLI `spx` 명령을 보여 줍니다.
Docker 컨테이너에서 `spx` 명령을 호출하는 경우, Speech CLI에서 구성 값을 저장하고 찾고 파일을 읽고 쓸 수 있는 파일 시스템에 컨테이너의 디렉터리를 탑재해야 합니다.

Windows에서 명령은 다음과 같이 시작됩니다.

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Linux 또는 macOS에서 명령은 아래 샘플과 같습니다. `ABSOLUTE_PATH`를 탑재된 디렉터리의 절대 경로로 바꿉니다. 이 경로는 이전 섹션의 `pwd` 명령에 의해 반환되었습니다. 

키와 지역을 설정하기 전에 이 명령을 실행하면 키와 지역을 설정하라는 오류 메시지가 표시됩니다.
```shell   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

컨테이너에 설치된 `spx` 명령을 사용하려면 항상 위에 표시된 전체 명령과 요청의 매개 변수를 차례로 입력합니다.
예를 들어 Windows에서 이 명령은 키를 설정합니다.

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> Docker 컨테이너 내에서 Speech CLI를 실행하는 경우에는 컴퓨터의 마이크를 사용할 수 없습니다. 그러나 로컬에 탑재된 디렉터리에서는 오디오 파일을 읽고 저장할 수 있습니다. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>구독 구성 만들기

음성 CLI 사용을 시작하려면 음성 구독 키 및 지역 식별자를 입력해야 합니다. [음성 서비스 무료로 사용해 보기](../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요.
구독 키와 지역 식별자가 있으면(예: `eastus`, `westus`) 다음 명령을 실행합니다.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

구독 인증은 이제 향후 SPX 요청에 대해 저장됩니다. 이러한 저장된 값 중 하나를 제거해야 하는 경우 `spx config @region --clear` 또는 `spx config @key --clear`를 실행합니다.
