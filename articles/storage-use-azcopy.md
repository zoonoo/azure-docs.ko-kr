<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram"></tags>

# AzCopy 명령줄 유틸리티 시작

AzCopy는 Microsoft Azure Blob 및 파일 저장소에 대해 고성능으로 데이터 업로드, 다운로드 및 복사를 수행하도록 디자인된 명령줄 유틸리티입니다. 이 설명서는 AzCopy 사용의 개요를 제공합니다.

> [WACOM.NOTE] 이 설명서에서는 AzCopy 2.5 이상을 설치했다고 가정합니다. AzCopy는 시험판 기능이므로 명령줄 옵션 및 해당 함수가 향후 릴리스에서 변경될 수 있습니다.

## 목차

-   [AzCopy 다운로드 및 설치][AzCopy 다운로드 및 설치]
-   [AzCopy 명령줄 구문 이해][AzCopy 명령줄 구문 이해]
-   [데이터를 복사하는 동안 동시 쓰기 제한][데이터를 복사하는 동안 동시 쓰기 제한]
-   [AzCopy를 사용하여 Azure Blob 복사][AzCopy를 사용하여 Azure Blob 복사]
-   [AzCopy를 사용하여 Azure 파일 공유의 파일 복사][AzCopy를 사용하여 Azure 파일 공유의 파일 복사]
-   [AzCopy 버전][AzCopy 버전]
-   [다음 단계][다음 단계]

## <span id="install"></span></a> AzCopy 다운로드 및 설치

1.  [최신 버전의 AzCopy][최신 버전의 AzCopy]를 다운로드합니다.
2.  설치를 실행합니다. 기본적으로 AzCopy 설치는 `%ProgramFiles(x86)%\Microsoft SDKs\Azure\`(64비트 Windows 실행 컴퓨터) 또는 `%ProgramFiles%\Microsoft SDKs\Azure\`(32비트 Windows 실행 컴퓨터) 아래에 `AzCopy`라는 폴더를 만듭니다. 그렇지만 설치 마법사에서 설치 경로를 변경할 수 있습니다.
3.  원할 경우 시스템 경로에 AzCopy 설치 위치를 추가할 수 있습니다.

## <span id="syntax"></span></a> AzCopy 명령줄 구문 이해

다음에는 명령 창을 열고 컴퓨터의 AzCopy 설치 디렉터리로 이동합니다. 이 디렉터리에 `AzCopy.exe` 실행 파일이 있습니다. AzCopy 명령의 기본 구문은 다음과 같습니다.

    AzCopy <source> <destination> [filepattern] [options]

-   `<source>` 매개 변수는 복사할 원본 데이터를 지정합니다. 소스는 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리 또는 저장소 파일 공유일 수 있습니다.

-   `<destination>` 매개 변수는 복사할 대상을 지정합니다. 대상은 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리 또는 저장소 파일 공유일 수 있습니다.

-   선택적 `filepattern` 매개 변수의 동작은 소스 데이터의 위치 및 재귀 모드 옵션의 존재 여부에 따라 결정됩니다. 재귀 모드는 `/S` 옵션을 통해 지정됩니다.

    지정된 소스가 파일 시스템의 디렉터리이면 표준 와일드카드를 사용할 수 있으며 제공되는 파일 패턴과 일치하는 파일을 디렉터리 내에서 검색합니다. `/S` 옵션을 지정하면 AzCopy는 디렉터리 아래의 모든 하위 폴더에서 지정된 패턴과 일치하는 모든 파일을 찾습니다.

    지정된 소스가 Blob 컨테이너 또는 가상 디렉터리인 경우 와일드카드가 지정되지 않습니다. `/S` 옵션을 지정하면 AzCopy는 지정된 파일 패턴을 Blob 접두사로 해석합니다. `/S` 옵션을 지정하지 않으면 AzCopy는 파일 패턴과 정확히 일치하는 Blob 이름을 찾습니다.

    지정된 소스가 Azure 파일 공유이면 단일 파일을 복사할 정확한 파일 이름(*예:* `abc.txt`)을 지정해야 하고, 공유의 모든 파일을 반복해서 복사하려면 `/S` 옵션을 지정해야 합니다. 파일 패턴과 `/S` 옵션을 모두 지정하려고 하면 오류가 발생합니다.

    파일 패턴을 지정하지 않을 때 사용되는 기본 파일 패턴은 파일 시스템 디렉터리의 경우에는 `*.*`이고, Azure Blob 또는 파일 저장소 리소스에 대해서는 빈 접두사입니다.

    > [WACOM.NOTE] 성능을 위해 AzCopy 버전 2.5는 더 이상 단일 명령으로 여러 파일 패턴을 지원하지 않습니다. 이제 여러 파일 패턴 시나리오를 해결하려면 각각이 단일 파일 패턴을 갖는 여러 명령을 실행해야 합니다.

-   `options` 매개 변수에 대해 사용할 수 있는 옵션은 아래 표에 설명되어 있습니다. 옵션에 대한 도움말을 보려면 명령줄에서 `AzCopy /?`를 입력할 수도 있습니다.

<table>
  <tr>
    <th>옵션 이름</th>
    <th>설명</th>
    <th>Blob 저장소 해당 여부(Y/N)</th>
    <th>파일 저장소 해당 여부(Y/N)</th>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>대상 리소스에 대한 저장소 계정 키를 지정합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/DestSAS:&lt;container-SAS&gt;</b></td>
    <td>대상 컨테이너에 대한 SAS(공유 액세스 서명)를 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다.<br />
        대상 리소스가 Blob이면 이 옵션을 지정하고 SAS 토큰을 지정하거나 이 옵션 없이 대상 Blob URI의 일부로 SAS를 지정할 수 있습니다.<br />
        이 옵션은 Blob 저장소에만 해당되며, 동일한 저장소 계정 내에서 Blob을 업로드하거나 복사할 때만 유효합니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>소스 리소스에 대한 저장소 계정 키를 지정합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;container-SAS&gt;</b></td>
    <td>소스 컨테이너에 대한 SAS(공유 액세스 서명)를 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다. 
        <br />
        키와 SAS를 모두 지정하지 않으면 컨테이너는 익명 액세스를 통해 읽힙니다. 
        <br />
        이 옵션은 Blob 저장소에만 적용됩니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>복사 작업의 재귀 모드를 지정합니다. 재귀 모드에서 AzCopy는 하위 폴더의 Blob 또는 파일을 비롯하여 지정된 파일 패턴과 일치하는 모든 Blob 또는 파일을 복사합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>대상이 블록 Blob 또는 페이지 Blob임을 지정합니다. 이 옵션은 대상이 Blob인 경우에만 적용되고 그렇지 않으면 오류가 생성됩니다. 대상이 Blob이고 이 옵션을 지정하지 않으면 기본적으로 AzCopy는 블록 Blob이 요구된다고 가정합니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMd5</b></td>
    <td>다운로드한 데이터의 MD5 해시를 계산하고 Blob 또는 파일의 Content-MD5 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 기본적으로 이 MD5 검사는 해제되어 있으므로 데이터를 다운로드할 때 MD5 검사를 수행하도록 이 옵션을 지정해야 합니다.
	<br />
    Azure 저장소는 Blob 또는 파일에 대해 저장된 MD5 해시가 최신 버전임을 보장하지 않습니다. Blob 또는 파일이 수정될 때마다 MD5를 업데이트하는 것은 클라이언트의 책임입니다.
	<br />
    AzCopy는 Azure Blob 또는 파일을 서비스로 업로드하기 전에 항상 해당 Content-MD5 속성을 설정합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>스냅숏을 전송할지 여부를 나타냅니다. 이 옵션은 소스가 Blob일 때만 유효합니다. 
        <br />
        전송된 Blob 스냅숏은 다음 형식으로 이름이 바뀝니다. [blob-name] (snapshot-time)[extension]. 
        <br />
        기본적으로 스냅숏은 복사되지 않습니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>세부 정보 표시 상태 메시지를 로그 파일로 출력합니다. 기본적으로 세부 정보 표시 로그 파일 이름은 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>에서 <code>AzCopyVerbose.log</code>로 바뀝니다. 이 옵션에 기존 파일 위치를 지정하면 세부 정보 표시 로그가 해당 파일에 추가됩니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>작업을 다시 시작하기 위한 저널 파일 폴더를 지정합니다.<br />
        AzCopy는 항상 작업이 중단되었을 때 다시 시작을 지원합니다.<br />
        이 옵션을 지정하지 않거나 폴더 경로 없이 지정하면 AzCopy는 기본 위치인 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>에 저널 파일을 만듭니다.<br />
        AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.
        <br />
		저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다. 
        <br />
        작업이 성공적으로 완료되면 저널 파일이 삭제됩니다.
		<br />
		이전 버전의 AzCopy에서 만들어진 저널 파일에서 작업을 다시 시작하는 것은 지원되지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/@:response-file</b></td>
    <td>매개 변수를 포함하는 파일을 지정합니다. AzCopy는 파일의 매개 변수가 마치 명령줄에 지정된 것처럼 처리합니다.<br /> 
		지시 파일에서 단일 줄에 여러 매개 변수를 지정하거나 한 줄에 매개 변수를 하나씩 지정할 수 있습니다. 하나의 매개 변수가 여러 줄에 걸쳐 있을 수 없습니다. 
        <br />
		지시 파일은 `#` 기호로 시작하는 명령줄을 포함할 수 있습니다. 
        <br />
        여러 지시 파일을 지정할 수 있습니다. 그렇지만 AzCopy는 중첩된지시 파일을 지원하지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>모든 AzCopy 확인 프롬프트를 표시하지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>열거 작업만 지정하고 데이터는 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>다운로드한 파일의 마지막으로 수정한 시간을 소스 Blob 또는 파일과 동일하게 설정합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>최신 소스 리소스를 제외합니다. 이 리소스는 소스가 대상보다 최신이면 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>오래된 소스 리소스를 제외합니다. 이 리소스는 소스 리소스가 대상보다 오래되었으면 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Archive 특성 집합이 있는 파일만 업로드합니다.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>지정된 특성 집합 중 하나라도 있는 파일만 업로드합니다.<br />
        사용 가능한 특성에는 다음이 포함됩니다. 
        <br />
        R   읽기 전용 파일
        <br />
        A   보관 준비가 된 파일
        <br />
        S   시스템 파일
        <br />
        H   숨김 파일
        <br />
        C   압축된 파일
        <br />
        N   일반 파일
        <br />
        E   암호화된 파일
        <br />
        T   임시 파일
        <br />
        O   오프라인 파일
        <br />
        I   인덱싱되지 않은 파일
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>E지정된 특성 집합 중 하나라도 있는 파일만 제외합니다.<br />
        사용 가능한 특성에는 다음이 포함됩니다.  
        <br />
        R   읽기 전용 파일  
        <br />
        A   보관 준비가 된 파일  
        <br />
        S   시스템 파일  
        <br />
        H   숨김 파일  
        <br />
        C   압축된 파일  
        <br />
        N   일반 파일  
        <br />
        E   암호화된 파일  
        <br />
        T   임시 파일  
        <br />
        O   오프라인 파일  
        <br />
        I   인덱싱되지 않은 파일</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Blob 이름에서 가상 디렉터리를 구분하는 데 사용되는 구분 문자를 나타냅니다.<br />
        기본적으로 AzCopy는 구분 문자로 /를 사용합니다. 그렇지만 AzCopy는 아무 일반 문자(예: @, \# 또는 %)를 구분 문자로 사용할 수 있게 지원합니다. 명령줄에 이러한 특수 문자 중 하나를 포함해야 하는 경우 파일 이름을 큰따옴표로 묶으세요. 
        <br />
        이 옵션은 Blob을 다운로드하는 데만 적용됩니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC</b></td>
    <td>동시 작업의 수를 지정합니다. 
        <br />
        기본값은 실행 중인 코어 프로세스 수의 8배입니다. 따라서 컴퓨터에 코어가 8개 있으면 기본적으로 AzCopy는 64개의 동시 작업을 시작합니다.<br />
        저대역폭 환경에서는 많은 수의 동시 작업으로 네트워크 연결에 과부하가 걸려 작업이 완전히 실행되지 못할 수 있습니다. 사용 가능한 실제 네트워크 대역폭에 따라 동시 작업을 조절하세요.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob</b></td>
    <td>`source` 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob로 지정합니다.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob</b></td>
    <td>`destination` 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob로 지정합니다..</td>
    <td>Y</td>
    <td>N</td>
  </tr>
</table>

## <span id="limit-writes"></span></a> 데이터를 복사하는 동안 동시 쓰기 제한

AzCopy를 사용하여 Blob 또는 파일을 복사할 때는 복사하는 동안 다른 응용 프로그램이 데이터를 수정할 수 있다는 사실을 유의해야 합니다. 가능한 경우 복사 중인 데이터가 복사 작업 중에 수정되지 않도록 합니다. 예를 들어 Azure 가상 컴퓨터와 연결된 VHD를 복사할 때는 다른 응용 프로그램이 현재 VHD에 쓰고 있지 않은지 확인합니다. 또는 먼저 VHD의 스냅샷을 만든 후 스냅샷을 복사할 수 있습니다.

다른 응용 프로그램이 복사 중인 Blob 또는 파일에 쓰지 못하게 할 수 없으면 작업이 완료될 때까지 복사된 리소스가 소스 리소스와 더 이상 완전히 동일하지 않을 수 있습니다.

## <span id="copy-blobs"></span></a> AzCopy를 사용하여 Azure Blob 복사

아래 예에서는 AzCopy를 사용하여 Blob을 복사하기 위한 다양한 시나리오를 보여 줍니다.

### 단일 Blob 복사

**파일 시스템의 파일을 Blob 저장소에 업로드**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Blob 저장소의 Blob을 파일 시스템에 다운로드**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### 서버 쪽 복사를 통해 Blob 복사

단일 저장소 계정 내에서 또는 여러 저장소 계정 간에 Blob을 복사할 때는 서버 쪽 복사 작업이 수행됩니다. 서버 쪽 복사 작업에 대한 자세한 내용은 [비동기 크로스 계정 Blob 복사 소개][비동기 크로스 계정 Blob 복사 소개](영문)를 참조하세요.

**저장소 계정 내 Blob 복사**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**저장소 계정 간 Blob 복사**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### 보조 지역에서 Blob 복사

저장소 계정의 읽기 액세스 지역 중복 저장소가 사용하도록 설정된 경우 보조 지역에서 데이터를 복사할 수 있습니다.

**보조 계정에서 기본 계정으로 Blob 복사**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**보조 계정의 Blob을 파일 시스템의 파일로 다운로드**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### 새 Blob 컨테이너 또는 가상 디렉터리로 파일 업로드

**새 Blob 컨테이너로 파일 업로드**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

지정된 대상 컨테이너가 존재하지 않을 경우 AzCopy는 컨테이너를 만든 후 여기에 파일을 업로드합니다.

**새 Blob 가상 디렉터리에 파일 업로드**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

지정한 가상 디렉터리가 없으면 AzCopy는 해당 이름에 가상 디렉터리를 포함하여 파일을 업로드합니다(*예:* 위 예의 `vd/abc.txt`).

### 새 폴더에 Blob 다운로드

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

`C:\myfolder` 폴더가 없으면 AzCopy는 파일 시스템에서 이 폴더를 만든 후 새 폴더에 `abc.txt`를 다운로드합니다.

### 디렉터리의 파일 및 하위 폴더를 재귀적으로 컨테이너에 업로드

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

`/S` 옵션을 지정하면 지정된 디렉터리의 내용이 Blob 저장소에 재귀적으로 복사됩니다. 즉, 모든 하위 폴더 및 해당 파일도 복사됩니다. 예를 들어 다음 파일이 `C:\myfolder` 폴더에 있는 경우를 생각해 보겠습니다.

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

복사 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 디렉터리의 파일을 컨테이너에 비재귀적으로 업로드

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

명령줄에 `/S` 옵션을 지정하지 않으면 AzCopy는 복사를 재귀적으로 수행하지 않습니다. 지정된 디렉터리에 있는 파일만 복사되고 모든 하위 폴더와 해당 파일은 복사되지 않습니다. 예를 들어 다음 파일이 `C:\myfolder` 폴더에 있는 경우를 생각해 보겠습니다.

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

복사 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

    abc.txt
    abc1.txt
    abc2.txt

### 컨테이너의 모든 Blob을 파일 시스템의 디렉터리에 재귀적으로 다운로드

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

다음 Blob가 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

복사 작업 후에 `C:\myfolder` 디렉터리에는 다음 파일이 포함됩니다.

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### 가상 Blob 디렉터리의 Blob을 파일 시스템의 디렉터리에 재귀적으로 다운로드

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

다음 Blob가 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

복사 작업 후에 `C:\myfolder` 디렉터리에는 다음 파일이 포함됩니다. 가상 디렉터리에 있는 Blob만 복사됩니다.

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### 지정된 파일 패턴과 일치하는 파일을 재귀적으로 컨테이너에 업로드

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

다음 파일이 `C:\myfolder` 폴더에 있는 경우를 생각해 보겠습니다.

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

복사 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 지정된 접두사를 갖는 Blob을 파일 시스템에 재귀적으로 다운로드

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

다음 Blob가 지정된 컨테이너에 있는 경우를 생각해 보겠습니다. 접두사 `a`로 시작하는 모든 Blob이 복사됩니다.

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

복사 작업 후에 `C:\myfolder` 폴더에는 다음 파일이 포함됩니다.

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

접두사는 Blob 이름의 처음 부분을 구성하는 가상 디렉터리에 적용됩니다. 위에 표시된 예에서는 가상 디렉터리가 지정된 접두사와 일치하지 않으므로 복사가 진행되지 않습니다.

### Blob 및 해당 스냅숏을 다른 저장소 계정으로 복사

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

복사 작업 후에 대상 컨테이너에는 Blob 및 해당 스냅숏이 포함됩니다. 위 예에서 Blob에 두 개의 스냅숏이 있다면 컨테이너에는 다음 Blob과 스냅숏이 포함될 것입니다.

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### 지시 파일을 사용하여 명령줄 매개 변수 지정

    AzCopy /@:"C:\myfolder\abc.txt"

지시 파일에 AzCopy 명령줄 매개 변수를 포함할 수 있습니다. AzCopy는 파일의 매개 변수가 마치 명령줄에 지정된 것처럼 처리하고 파일 내용을 직접적으로 대체합니다.

**하나 이상의 한 줄 지시 파일 지정**

소스 컨테이너를 지정하는 `source.txt`라는지시 파일이 있고,

    http://myaccount.blob.core.windows.net/mycontainer

파일 시스템의 대상 폴더를 지정하는 `dest.txt`라는지시 파일이 있고,

    C:\myfolder

AzCopy에 대한 옵션을 지정하는 `options.txt`라는지시 파일이 있는 경우

    /S /Y

모두 `C:\responsefiles` 디렉터리에 있는 이러한 지시 파일을 사용하여 AzCopy를 호출하려면 다음 명령을 사용합니다.

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy는 명령줄에 개별 매개 변수를 모두 포함할 때처럼 이 명령을 처리합니다.

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**여러 줄 지시 파일 지정**

다음 줄을 포함하는 `copyoperation.txt`라는지시 파일이 있고 각 AzCopy 매개 변수는 자체 줄에 지정되어 있을 경우

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

이 지시 파일을 사용하여 AzCopy를 호출하려면 다음 명령을 사용합니다.

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy는 명령줄에 개별 매개 변수를 모두 포함할 때처럼 이 명령을 처리합니다.

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

각 AzCopy 매개 변수는 모두 한 줄에 지정해야 합니다. 예를 들어 `/sourcekey` 매개 변수에 대해 여기에 표시된 것처럼 매개 변수를 두 줄로 분할하면 AzCopy는 실패합니다.

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### SAS(공유 액세스 서명) 지정

**/sourceSAS 옵션을 사용하여 소스 컨테이너에 대해 SAS 지정**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**소스 컨테이너 URI에 소스 컨테이너에 대한 SAS 지정**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**/destSAS 옵션을 사용하여 대상 컨테이너에 대해 SAS 지정**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**소스 및 대상 컨테이너에 대해 SAS 지정**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### 저널 파일 폴더 지정

AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.

저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다.

**저널 파일에 대해 기본 위치 사용**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

`/Z` 옵션을 생략하거나 위에 표시된 것처럼 폴더 경로 없이 `/Z`를 지정하면 AzCopy는 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`인 기본 위치에 저널 파일을 만듭니다. 저널 파일이 이미 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

**저널 파일의 사용자 지정 위치 지정**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

이 예에서는 저널 파일이 없는 경우 파일을 만듭니다. 저널 파일이 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

**AzCopy 작업 다시 시작**

    AzCopy /Z:C:\journalfolder\

이 예에서는 완료하지 못했을 수 있는 마지막 작업을 다시 시작합니다.

### 로그 파일 생성

**기본 위치에 세부 정보 표시 로그 파일 쓰기**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

세부 정보 표시 로그 파일에 대한 파일 경로를 제공하지 않고 `/V` 옵션을 지정하면 AzCopy는 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`에 해당하는 기본 위치에 로그 파일을 만듭니다.

**사용자 지정 위치에 세부 정보 표시 로그 파일 쓰기**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

`/V:test/azcopy1.log`와 같이 `/V` 옵션 다음에 상대 경로를 지정하면 하위 폴더 `test` 내의 현재 작업 디렉터리에 세부 정보 표시 로그가 만들어집니다.

### 다운로드한 파일의 마지막으로 수정한 시간을 소스 Blob과 동일하게 설정

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### 마지막으로 수정한 시간에 따라 복사 작업에서 Blob 제외

`/MT` 옵션을 지정하여 소스 Blob 및 대상 파일의 마지막으로 수정한 시간을 비교합니다.

**대상 파일보다 최신인 Blob 제외**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**대상 파일보다 오래된 Blob 제외**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### 시작할 동시 작업 수 지정

`/NC` 옵션은 동시 복사 작업의 수를 지정합니다. 기본적으로 AzCopy는 보유한 코어 프로세서 수의 8배에 해당하는 동시 작업을 시작합니다. 저대역폭 네트워크에서 AzCopy를 실행하는 경우에는 리소스 경쟁으로 인한 실패를 방지하기 위해 이 옵션 값을 더 낮게 지정할 수 있습니다.

### 저장소 에뮬레이터의 Blob 리소스에 대해 AzCopy 실행

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> AzCopy를 사용하여 Azure 파일 공유의 파일 복사

아래 예에서는 AzCopy를 사용하여 Azure 파일을 복사하기 위한 다양한 시나리오를 보여 줍니다.

### Azure 파일 공유의 파일을 파일 시스템으로 다운로드

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

지정된 소스가 Azure 파일 공유이면 단일 파일을 복사할 정확한 파일 이름(*예:* `abc.txt`)을 지정해야 하고, 공유의 모든 파일을 반복해서 복사하려면 `/S` 옵션을 지정해야 합니다. 파일 패턴과 `/S` 옵션을 모두 지정하려고 하면 오류가 발생합니다.

### Azure 파일 공유의 파일 및 폴더를 파일 시스템으로 재귀적으로 다운로드

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

빈 폴더는 복사되지 않습니다.

### 파일 시스템의 파일 및 폴더를 Azure 파일 공유로 재귀적으로 업로드

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

빈 폴더는 복사되지 않습니다.

### 지정된 파일 패턴과 일치하는 파일을 재귀적으로 Azure 파일 공유에 업로드

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> AzCopy 버전

| 버전       | 새로운 기능                                                                                                        |
|------------|--------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **최신 버전입니다. 대규모 복사 시나리오에 맞게 성능을 최적화하고 몇 가지 향상된 중요한 사용 편의성을 제공합니다.** |
| V2.4.1     | 설치 마법사에서 대상 폴더를 지정할 수 있습니다.                                                                    |
| V2.4.0     | Azure 파일 저장소 파일을 업로드 및 다운로드할 수 있습니다.                                                         |
| V2.3.0     | 읽기 액세스 지역 중복 저장소 계정을 지원합니다.                                                                    |
| V2.2.2     | Azure 저장소 클라이언트 라이브러리 버전 3.0.3을 사용하도록 업그레이드되었습니다.                                   |
| V2.2.1     | 동일한 저장소 계정 내에서 대량의 파일을 복사할 때의 성능 문제가 수정되었습니다.                                    |
| V2.2       | Blob 이름에 대한 가상 디렉터리 구분 기호를 설정할 수 있습니다. 저널 파일 경로를 지정할 수 있습니다.                |
| V2.1       | 효율적인 방법으로 Blob 업로드, 다운로드 및 복사 작업을 지원하기 위한 20가지 이상의 옵션을 제공합니다.              |

## <span id="next-steps"></span></a>다음 단계

Azure 저장소 및 AzCopy에 대한 자세한 내용은 다음 리소스를 참조하세요.

### Azure 저장소 설명서

-   [Azure 저장소 소개][Azure 저장소 소개]
-   [Blob 저장소에 파일 저장(영문)][Blob 저장소에 파일 저장(영문)]
-   [파일 저장소를 사용하여 Azure에서 SMB 파일 공유 만들기(영문)][파일 저장소를 사용하여 Azure에서 SMB 파일 공유 만들기(영문)]

### Azure 저장소 블로그 게시물:

-   [Microsoft Azure 파일 서비스 소개(영문)][Microsoft Azure 파일 서비스 소개(영문)]
-   [AzCopy 2.5: 대량 복사 시나리오에 맞게 최적화][AzCopy 2.5: 대량 복사 시나리오에 맞게 최적화](영문)
-   [AzCopy: 읽기 액세스 지역 중복 저장소 지원][AzCopy: 읽기 액세스 지역 중복 저장소 지원](영문)
-   [AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송][AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송](영문)
-   [AzCopy: 크로스 계정 Blob 복사 사용][AzCopy: 크로스 계정 Blob 복사 사용](영문)
-   [AzCopy: Windows Azure Blob 파일 업로드/다운로드][AzCopy: Windows Azure Blob 파일 업로드/다운로드](영문)

  [AzCopy 다운로드 및 설치]: #install
  [AzCopy 명령줄 구문 이해]: #syntax
  [데이터를 복사하는 동안 동시 쓰기 제한]: #limit-writes
  [AzCopy를 사용하여 Azure Blob 복사]: #copy-blobs
  [AzCopy를 사용하여 Azure 파일 공유의 파일 복사]: #copy-files
  [AzCopy 버전]: #versions
  [다음 단계]: #next-steps
  [최신 버전의 AzCopy]: http://aka.ms/downloadazcopy
  [비동기 크로스 계정 Blob 복사 소개]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Azure 저장소 소개]: http://azure.microsoft.com/ko--kr/documentation/articles/storage-introduction/
  [Blob 저장소에 파일 저장(영문)]: http://azure.microsoft.com/ko--kr/documentation/articles/storage-dotnet-how-to-use-blobs/
  [파일 저장소를 사용하여 Azure에서 SMB 파일 공유 만들기(영문)]: http://azure.microsoft.com/ko--kr/documentation/articles/storage-dotnet-how-to-use-files/
  [Microsoft Azure 파일 서비스 소개(영문)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: 대량 복사 시나리오에 맞게 최적화]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: 읽기 액세스 지역 중복 저장소 지원]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: 크로스 계정 Blob 복사 사용]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Windows Azure Blob 파일 업로드/다운로드]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
