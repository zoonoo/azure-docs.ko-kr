<properties urlDisplayName="AzCopy" pageTitle="Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# AzCopy 명령줄 유틸리티 시작

AzCopy는 Microsoft Azure Blob, 파일 및 테이블 저장소에 대해 고성능으로 데이터 업로드, 다운로드 및 복사를 수행하도록 디자인된 명령줄 유틸리티입니다. 이 설명서는 AzCopy 사용의 개요를 제공합니다.

> [WACOM.NOTE] 이 설명서에서는 AzCopy 3.0.0 이상을 설치했다고 가정합니다. AzCopy 3.x는 이제 일반 공급으로 제공됩니다.<br /> 
> 이 설명서에서는 AzCopy의 미리 보기 릴리스인 AzCopy 4.0.0 사용에 대해서도 설명합니다. 이 설명서 전체에서 미리 보기 릴리스로만 제공되는 기능은 *미리 보기*로 지정됩니다.<br />
> AzCopy 4.x의 경우 명령줄 옵션 및 기능이 향후 릴리스에서 변경될 수 있습니다.

##목차

- [AzCopy 다운로드 및 설치](#install)
- [AzCopy 명령줄 구문 이해](#syntax)
- [데이터를 복사하는 동안 동시 쓰기 제한](#limit-writes)
- [AzCopy를 사용하여 Azure Blob 복사](#copy-blobs)
- [AzCopy를 사용하여 Azure 파일 공유의 파일 복사&#40;미리 보기 버전 전용&#41;](#copy-files)
- [AzCopy를 사용하여 Azure 테이블 엔터티 복사&#40;미리 보기 버전 전용&#41;](#copy-entities)
- [AzCopy 버전](#versions)
- [다음 단계](#next-steps)

##<a id="install"></a> AzCopy 다운로드 및 설치

1. [최신 버전의 AzCopy](http://aka.ms/downloadazcopy) 또는 [최신 미리 보기 버전](http://aka.ms/downloadazcopypr)을 다운로드합니다.
2. 설치를 실행합니다. 기본적으로 AzCopy를 설치하면 `AzCopy`라는 폴더가 `%ProgramFiles(x86)%\Microsoft SDKs\Azure\`(64비트 Windows를 실행하는 컴퓨터) 또는 `%ProgramFiles%\Microsoft SDKs\Azure\`(32비트 Windows를 실행하는 컴퓨터) 아래에 만들어집니다. 그렇지만 설치 마법사에서 설치 경로를 변경할 수 있습니다.
3. 원할 경우 시스템 경로에 AzCopy 설치 위치를 추가할 수 있습니다.

##<a id="syntax"></a> AzCopy 명령줄 구문 이해

다음에는 명령 창을 열고 컴퓨터의 AzCopy 설치 디렉터리로 이동합니다. 이 디렉터리에 `AzCopy.exe` 실행 파일이 있습니다. AzCopy 명령의 기본 구문은 다음과 같습니다.

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] AzCopy 버전 3.0.0부터는 AzCopy 명령줄 구문에서 모든 매개 변수가 매개 변수 이름을 포함하도록 지정해야 합니다(*예:*, `/ParameterName:ParameterValue`).

아래 표에서는 AzCopy의 매개 변수에 대해 설명합니다. 명령줄에서 다음 명령 중 하나를 입력하여 AzCopy 사용을 위한 도움말을 확인할 수도 있습니다.

- AzCopy에 대한 자세한 명령줄 도움말: `AzCopy /?`
- AzCopy 매개 변수와 관련된 자세한 도움말: `AzCopy /?:SourceKey`
- 명령줄 예제: `AzCopy /?:Samples` 


<table>
  <tr>
    <th>옵션 이름</th>
    <th>설명</th>
    <th>Blob 저장소 해당 여부(Y/N)</th>
    <th>파일 저장소 해당 여부(Y/N)(미리 보기 릴리스 전용)</th>
    <th>테이블 저장소 해당 여부(Y/N)(미리 보기 릴리스 전용)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source&gt;</b></td>
    <td>복사할 소스 데이터를 지정합니다. 소스는 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리, 저장소 파일 공유, 저장소 파일 디렉터리 또는 Azure 테이블일 수 있습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination&gt;</b></td>
    <td>복사할 대상을 지정합니다. 대상은 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리, 저장소 파일 공유, 저장소 파일 디렉터리 또는 Azure 테이블일 수 있습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern&gt;</b></td>
      <td>
          복사할 파일을 나타내는 파일 패턴을 지정합니다. 선택적 /Pattern 매개 변수의 동작은 소스 데이터의 위치 및 재귀 모드 옵션 유무에 따라 결정됩니다. 재귀 모드는 /S 옵션을 통해 지정됩니다.
          <br />
          지정된 소스가 파일 시스템의 디렉터리이면 표준 와일드카드를 사용할 수 있으며 제공되는 파일 패턴과 일치하는 파일을 디렉터리 내에서 검색합니다. /S 옵션을 지정하면 AzCopy는 디렉터리 아래의 모든 하위 폴더에서 지정된 패턴과 일치하는 모든 파일을 찾습니다.
          <br />
          지정된 소스가 Blob 컨테이너 또는 가상 디렉터리인 경우 와일드카드가 지정되지 않습니다. /S 옵션을 지정하면 AzCopy는 지정된 파일 패턴을 Blob 접두사로 해석합니다. /S 옵션을 지정하지 않으면 AzCopy는 파일 패턴과 정확히 일치하는 Blob 이름을 찾습니다.
          <br />
          지정된 소스가 Azure 파일 공유이면 단일 파일을 복사할 정확한 파일 이름(예: abc.txt)을 지정하거나 /S 옵션을 지정하여 공유의 모든 파일을 재귀 방식으로 복사해야 합니다. 파일 패턴과 /S 옵션을 모두 지정하려고 하면 오류가 발생합니다.
          <br />
          파일 패턴을 지정하지 않을 때 사용되는 기본 파일 패턴은 파일 시스템 위치의 경우 *.* 이고 Azure 저장소 위치의 경우에는 빈 접두사입니다. 여러 파일 패턴을 지정할 수는 없습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>대상 리소스에 대한 저장소 계정 키를 지정합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token&gt;</b></td>
    <td class="auto-style1">대상 컨테이너에 대한 SAS(공유 액세스 서명)를 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다.<br />
        대상 리소스가 Blob 컨테이너 또는 테이블이면 이 옵션을 지정하고 SAS 토큰을 지정하거나 이 옵션 없이 대상 Blob URI의 일부로 SAS를 지정할 수 있습니다.<br />
        소스와 대상이 모두 Blob이면 대상 Blob은 소스 Blob과 같은 저장소 계정 내에 있어야 합니다.</td>
    <td class="auto-style1">Y</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>소스 리소스에 대한 저장소 계정 키를 지정합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token&gt;</b></td>
    <td>소스 컨테이너에 대한 SAS(공유 액세스 서명)를 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다.
        <br />
        소스 리소스가 Blob 컨테이너인 경우 키와 SAS를 모두 지정하지 않으면 익명 액세스를 통해 Blob 컨테이너를 읽습니다.
        <br />
        소스가 테이블이면 키나 SAS를 지정해야 합니다.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>복사 작업의 재귀 모드를 지정합니다. 재귀 모드에서 AzCopy는 하위 폴더의 Blob 또는 파일을 비롯하여 지정된 파일 패턴과 일치하는 모든 Blob 또는 파일을 복사합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>대상 Blob이 블록 Blob인지 아니면 페이지 Blob인지를 지정합니다. 이 옵션은 Blob을 업로드할 때만 적용되며 그 외의 경우에는 오류가 생성됩니다. 대상이 Blob인데 이 옵션을 지정하지 않으면 기본적으로 AzCopy는 블록 Blob을 만듭니다.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>다운로드한 데이터의 MD5 해시를 계산하고 Blob 또는 파일의 Content-MD5 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 기본적으로 이 MD5 검사는 해제되어 있으므로 데이터를 다운로드할 때 MD5 검사를 수행하도록 이 옵션을 지정해야 합니다.
	<br />
    Azure 저장소는 Blob 또는 파일에 대해 저장된 MD5 해시가 최신 버전임을 보장하지 않습니다. Blob 또는 파일이 수정될 때마다 MD5를 업데이트하는 것은 클라이언트의 책임입니다.
	<br />
    AzCopy는 Azure Blob 또는 파일을 서비스로 업로드하기 전에 항상 해당 Content-MD5 속성을 설정합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
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
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>세부 정보 표시 상태 메시지를 로그 파일로 출력합니다. 기본적으로 세부 정보 표시 로그 파일 이름은 <code>AzCopyVerbose.log</code> 이고 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>에 있습니다. 이 옵션에 기존 파일 위치를 지정하면 세부 정보 표시 로그가 해당 파일에 추가됩니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>작업을 다시 시작하기 위한 저널 파일 폴더를 지정합니다.<br />
        AzCopy는 작업이 중단된 경우 항상 다시 시작을 지원합니다.<br />
        이 옵션을 지정하지 않거나 폴더 경로 없이 지정하면 AzCopy는 기본 위치인 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>에 저널 파일을 만듭니다.<br />
        AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.
        <br />
		저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다. 
        <br />
        작업이 성공적으로 완료되면 저널 파일이 삭제됩니다.
		<br />
		이전 버전의 AzCopy에서 만들어진 저널 파일에서 작업을 다시 시작하는 것은 지원되지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>매개 변수를 포함하는 파일을 지정합니다. AzCopy는 파일의 매개 변수가 명령줄에 지정된 것처럼 처리합니다.<br /> 
		지시 파일에서 단일 줄에 여러 매개 변수를 지정하거나 한 줄에 매개 변수를 하나씩 지정할 수 있습니다. 하나의 매개 변수가 여러 줄에 걸쳐 있을 수 없습니다. 
        <br />
		지시 파일은 <code>#</code> 기호로 시작하는 명령줄을 포함할 수 있습니다. 
        <br />
        여러 지시 파일을 지정할 수 있습니다. 그렇지만 AzCopy는 중첩된 지시 파일을 지원하지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>모든 AzCopy 확인 프롬프트를 표시하지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>열거 작업만 지정하고 데이터는 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>다운로드한 파일의 마지막으로 수정한 시간을 소스 Blob 또는 파일과 동일하게 설정합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>최신 소스 리소스를 제외합니다. 이 리소스는 소스가 대상보다 최신이면 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>오래된 소스 리소스를 제외합니다. 이 리소스는 소스 리소스가 대상보다 오래되었으면 복사되지 않습니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Archive 특성 집합이 있는 파일만 업로드합니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>지정된 특성 집합 중 하나라도 있는 파일만 업로드합니다.<br />
        사용 가능한 특성에는 다음이 포함됩니다.  
        <br />
        R&nbsp;&nbsp;&nbsp;읽기 전용 파일
        <br />
        A&nbsp;&nbsp;&nbsp;보관할 준비가 된 파일
        <br />
        S&nbsp;&nbsp;&nbsp;시스템 파일
        <br />
        H&nbsp;&nbsp;&nbsp;숨겨진 파일
        <br />
        C&nbsp;&nbsp;&nbsp;압축된 파일
        <br />
        N&nbsp;&nbsp;&nbsp;일반 파일
        <br />
        E&nbsp;&nbsp;&nbsp;암호화된 파일
        <br />
        T&nbsp;&nbsp;&nbsp;임시 파일
        <br />
        O&nbsp;&nbsp;&nbsp;오프라인 파일
        <br />
        I&nbsp;&nbsp;&nbsp;인덱싱되지 않은 파일</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>지정된 특성 집합 중 하나라도 있는 파일을 제외합니다.<br />
        사용 가능한 특성에는 다음이 포함됩니다.  
        <br />
        R&nbsp;&nbsp;&nbsp;읽기 전용 파일  
        <br />
        A&nbsp;&nbsp;&nbsp;보관할 준비가 된 파일  
        <br />
        S&nbsp;&nbsp;&nbsp;시스템 파일  
        <br />
        H&nbsp;&nbsp;&nbsp;숨겨진 파일  
        <br />
        C&nbsp;&nbsp;&nbsp;압축된 파일  
        <br />
        N&nbsp;&nbsp;&nbsp;일반 파일  
        <br />
        E&nbsp;&nbsp;&nbsp;암호화된 파일  
        <br />
        T&nbsp;&nbsp;&nbsp;임시 파일  
        <br />
        O&nbsp;&nbsp;&nbsp;오프라인 파일  
        <br />
        I&nbsp;&nbsp;&nbsp;인덱싱되지 않은 파일</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Blob 이름에서 가상 디렉터리를 구분하는 데 사용되는 구분 기호를 나타냅니다.<br />
        기본적으로 AzCopy는 구분 문자로 /를 사용합니다. 그렇지만 AzCopy는 아무 일반 문자(예: @, # 또는 %)를 구분 문자로 사용할 수 있게 지원합니다. 명령줄에 이러한 특수 문자 중 하나를 포함해야 하는 경우 파일 이름을 큰따옴표로 묶으세요. 
        <br />
        이 옵션은 Blob을 다운로드하는 데만 적용됩니다.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents&gt;</b></td>
    <td>동시 작업의 수를 지정합니다.
        <br />
        AzCopy는 데이터 전송 처리량을 높이기 위해 기본적으로 특정 수의 동시 작업을 시작합니다. 저대역폭 환경에서는 많은 수의 동시 작업으로 네트워크 연결에 과부하가 걸려 작업이 완전히 실행되지 못할 수 있습니다. 사용 가능한 실제 네트워크 대역폭에 따라 동시 작업을 조절하세요.
        <br />
		동시 작업의 상한은 512개입니다.</td>
    <td>Y</td>
    <td>Y<br /> (미리 보기 전용)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td> <code>source</code> 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob으로 지정합니다.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td> <code>destination</code> 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob으로 지정합니다.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;&quot;key1#key2#key3#...&quot;&gt;</strong></td>
    <td>테이블 데이터를 병렬로 내보낼 수 있도록 파티션 키 범위를 분할합니다. 그러면 내보내기 작업의 속도가 빨라집니다.
        <br />
        이 옵션을 지정하지 않으면 AzCopy는 단일 스레드를 사용하여 테이블 엔터티를 내보냅니다. 예를 들어 사용자가 /PKRS:&quot;aa#bb&quot;를 지정하면 AzCopy는 세 개의 동시 작업을 시작합니다.
        <br />
        각 작업에서는 아래에 나와 있는 것처럼 3개 파티션 키 범위 중 하나를 내보냅니다. 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;&lt;첫 번째 파티션 키&gt;, aa&#41; 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;aa, bb&#41;
        <br />
        &nbsp;&nbsp;&nbsp;&#91;bb, &lt;마지막 파티션 키&gt;&#93; </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size&gt;</strong></td>
    <td>내보내는 파일 분할 크기를 MB로 지정합니다.
        <br />
        이 옵션을 지정하지 않으면 AzCopy는 단일 파일로 테이블 데이터를 내보냅니다.
        <br />
        테이블 데이터를 Blob으로 내보내는데 내보낸 파일 크기가 Blob 크기에 대한 제한인 200GB에 도달하면 AzCopy는 이 옵션을 지정하지 않은 경우에도 내보낸 파일을 분할합니다. </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace&gt;
</b>
</td>
    <td>테이블 데이터 가져오기 동작을 지정합니다.
        <br />
        InsertOrSkip - 기존 엔터티를 건너뛰거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
        <br />
        InsertOrMerge - 기존 엔터티를 병합하거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
        <br />
        InsertOrReplace - 기존 엔터티를 바꾸거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다. </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file&gt;</b></td>
    <td>가져오기 작업을 위한 매니페스트 파일을 지정합니다. <br />
        매니페스트 파일은 내보내기 작업 중에 생성됩니다.</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (미리 보기 전용)</td>
  </tr>
</table>

<br/>

##<a id="limit-writes"></a> 데이터를 복사하는 동안 동시 쓰기 제한

AzCopy를 사용하여 Blob 또는 파일을 복사할 때는 복사하는 동안 다른 응용 프로그램이 데이터를 수정할 수 있다는 사실을 유의해야 합니다. 가능한 경우 복사 중인 데이터가 복사 작업 중에 수정되지 않도록 합니다. 예를 들어 Azure 가상 컴퓨터와 연결된 VHD를 복사할 때는 다른 응용 프로그램이 현재 VHD에 쓰고 있지 않은지 확인합니다. 또는 먼저 VHD의 스냅샷을 만든 후 스냅샷을 복사할 수 있습니다.

다른 응용 프로그램이 복사 중인 Blob 또는 파일에 쓰지 못하게 할 수 없으면 작업이 완료될 때까지 복사된 리소스가 소스 리소스와 더 이상 완전히 동일하지 않을 수 있습니다.

##<a id="copy-blobs"></a> AzCopy를 사용하여 Azure Blob 복사

아래 예에서는 AzCopy를 사용하여 Blob을 복사하기 위한 다양한 시나리오를 보여 줍니다.

### 단일 Blob 복사

**파일 시스템의 파일을 Blob 저장소에 업로드:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Blob 저장소의 Blob을 파일 시스템에 다운로드:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### 서버 쪽 복사를 통해 Blob 복사

단일 저장소 계정 내에서 또는 여러 저장소 계정 간에 Blob을 복사할 때는 서버 쪽 복사 작업이 수행됩니다. 서버 쪽 복사 작업에 대한 자세한 내용은 [비동기 크로스 계정 Blob 복사 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)(영문)를 참조하세요.

**저장소 계정 내 Blob 복사:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**저장소 계정 간 Blob 복사:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### 보조 지역에서 Blob 복사 

저장소 계정의 읽기 액세스 지역 중복 저장소가 사용하도록 설정된 경우 보조 지역에서 데이터를 복사할 수 있습니다. 

**보조 계정에서 기본 계정으로 Blob 복사:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**보조 계정의 Blob을 파일 시스템의 파일로 다운로드:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### 새 Blob 컨테이너 또는 가상 디렉터리로 파일 업로드

**새 Blob 컨테이너로 파일 업로드**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

지정된 대상 컨테이너가 존재하지 않을 경우 AzCopy는 컨테이너를 만든 후 여기에 파일을 업로드합니다.

**새 Blob 가상 디렉터리에 파일 업로드**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

지정한 가상 디렉터리가 없으면 AzCopy는 해당 이름에 가상 디렉터리를 포함하여 파일을 업로드합니다(*예:* 위 예제의 `vd/abc.txt`).

### 새 폴더에 Blob 다운로드

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

`C:\myfolder` 폴더가 없으면 AzCopy는 파일 시스템에서 해당 폴더를 만들고 새 폴더에 `abc.txt`를 다운로드합니다.

### 디렉터리의 파일 및 하위 폴더를 재귀적으로 컨테이너에 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

`/S` 옵션을 지정하면 지정된 디렉터리의 내용이 Blob 저장소에 재귀적으로 복사됩니다. 즉, 모든 하위 폴더 및 해당 파일도 복사됩니다. 예를 들어 다음 파일이 `C:\myfolder` 폴더에 있다고 가정해 보겠습니다.

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

명령줄에서 `/S` 옵션을 지정하지 않으면 AzCopy는 복사를 재귀적으로 수행하지 않습니다. 지정된 디렉터리에 있는 파일만 복사되고 모든 하위 폴더와 해당 파일은 복사되지 않습니다. 예를 들어 다음 파일이 `C:\myfolder` 폴더에 있다고 가정해 보겠습니다.

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.  

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

복사 작업 후에 `C:\myfolder` 디렉터리에는 다음 파일이 포함됩니다. 가상 디렉터리에 있는 Blob만 복사됩니다.

	C:\myfolder\a.txt
	C:\myfolder\abcd.txt

### 지정된 파일 패턴과 일치하는 파일을 재귀적으로 컨테이너에 업로드 

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

다음 파일이 `C:\myfolder` 폴더에 있다고 가정해 보겠습니다.

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다. 접두사 `a`로 시작하는 모든 Blob이 복사됩니다.

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

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

복사 작업 후에 대상 컨테이너에는 Blob 및 해당 스냅숏이 포함됩니다. 위 예에서 Blob에 두 개의 스냅숏이 있다면 컨테이너에는 다음 Blob과 스냅숏이 포함될 것입니다.

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### 지시 파일을 사용하여 명령줄 매개 변수 지정

	AzCopy /@:"C:\myfolder\abc.txt"

지시 파일에 AzCopy 명령줄 매개 변수를 포함할 수 있습니다. AzCopy는 파일의 매개 변수가 마치 명령줄에 지정된 것처럼 처리하고 파일 내용을 직접적으로 대체합니다.

**하나 이상의 한 줄 지시 파일 지정**

소스 컨테이너를 지정하는 `source.txt`라는 지시 파일을 가정할 경우:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

파일 시스템의 대상 폴더를 지정하는 `dest.txt`라는 지시 파일을 가정할 경우:

	/Dest:C:\myfolder

AzCopy에 대한 옵션을 지정하는 `options.txt`라는 지시 파일을 가정할 경우:

	/S /Y

모두 `C:\responsefiles` 디렉터리에 있는 이러한 지시 파일을 사용하여 AzCopy를 호출하려면 다음 명령을 사용합니다.

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy는 명령줄에 개별 매개 변수를 모두 포함할 때처럼 이 명령을 처리합니다.

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**여러 줄 지시 파일 지정**

다음 줄을 포함하는 `copyoperation.txt`라는 지시 파일을 가정해 보겠습니다. 각 AzCopy 매개 변수는 자체 줄에 지정되어 있을 경우

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

이 지시 파일을 사용하여 AzCopy를 호출하려면 다음 명령을 사용합니다.

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy는 명령줄에 개별 매개 변수를 모두 포함할 때처럼 이 명령을 처리합니다.	

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

각 AzCopy 매개 변수는 모두 한 줄에 지정해야 합니다. 예를 들어 `/sourcekey` 매개 변수에 대해 여기에 표시된 대로 매개 변수를 두 줄로 분할하면 AzCopy가 실패합니다.

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### SAS(공유 액세스 서명) 지정
	
**/sourceSAS 옵션을 사용하여 소스 컨테이너에 대해 SAS 지정**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**소스 컨테이너 URI에 소스 컨테이너에 대한 SAS 지정**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**/destSAS 옵션을 사용하여 대상 컨테이너에 대해 SAS 지정**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**소스 및 대상 컨테이너에 대해 SAS 지정**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### 저널 파일 폴더 지정

AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.

저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다. 

**저널 파일에 대해 기본 위치 사용**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

`/Z` 옵션을 생략하거나 위에 표시된 대로 폴더 경로 없이 `/Z` 옵션을 지정하면 AzCopy는 기본 위치인 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`에 저널 파일을 만듭니다. 저널 파일이 이미 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다. 

**저널 파일의 사용자 지정 위치 지정**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

이 예에서는 저널 파일이 없는 경우 파일을 만듭니다. 저널 파일이 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

**AzCopy 작업 다시 시작**

	AzCopy /Z:C:\journalfolder\

이 예에서는 완료하지 못했을 수 있는 마지막 작업을 다시 시작합니다.


### 로그 파일 생성

**기본 위치에 세부 정보 표시 로그 파일 쓰기**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

세부 정보 표시 로그의 파일 경로를 제공하지 않고 `/V` 옵션을 지정하면 AzCopy는 기본 위치인 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`에 로그 파일을 만듭니다.

**사용자 지정 위치에 세부 정보 표시 로그 파일 쓰기**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

`/V:test/azcopy1.log`와 같이 `/V` 옵션 다음에 상대 경로를 지정하면 하위 폴더 `test` 내의 현재 작업 디렉터리에 세부 정보 표시 로그가 만들어집니다.


### 다운로드한 파일의 마지막으로 수정한 시간을 소스 Blob과 동일하게 설정

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### 마지막으로 수정한 시간에 따라 복사 작업에서 Blob 제외

`/MT` 옵션을 지정하여 소스 Blob과 대상 파일의 마지막으로 수정한 시간을 비교합니다.

**대상 파일보다 최신인 Blob 제외**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**대상 파일보다 오래된 Blob 제외**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### 시작할 동시 작업 수 지정

`/NC` 옵션은 동시 복사 작업의 수를 지정합니다. 기본적으로 AzCopy는 보유한 코어 프로세서 수의 8배에 해당하는 동시 작업을 시작합니다. 저대역폭 네트워크에서 AzCopy를 실행하는 경우에는 리소스 경쟁으로 인한 실패를 방지하기 위해 이 옵션 값을 더 낮게 지정할 수 있습니다.


###	저장소 에뮬레이터의 Blob 리소스에 대해 AzCopy 실행

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S


##<a id="copy-files"></a> AzCopy를 사용하여 Azure 파일 저장소의 파일 복사(미리 보기 버전 전용)

아래 예에서는 AzCopy를 사용하여 Azure 파일을 복사하기 위한 다양한 시나리오를 보여 줍니다.

### Azure 파일 공유의 파일을 파일 시스템으로 다운로드

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

지정된 소스가 Azure 파일 공유이면 단일 파일을 복사할 정확한 파일 이름(*예:* `abc.txt`)를 지정해야 하고, 공유의 모든 파일을 재귀적으로 복사하려면 `/S` 옵션을 지정해야 합니다. 파일 패턴과 `/S` 옵션을 모두 지정하려고 하면 오류가 발생합니다.

### Azure 파일 공유의 파일 및 폴더를 파일 시스템으로 재귀적으로 다운로드

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

빈 폴더는 복사되지 않습니다.


### 파일 시스템의 파일 및 폴더를 Azure 파일 공유로 재귀적으로 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

빈 폴더는 복사되지 않습니다.


### 지정된 파일 패턴과 일치하는 파일을 재귀적으로 Azure 파일 공유에 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

##<a id="copy-entities"></a> AzCopy를 사용하여 Azure 테이블의 엔터티 복사(미리 보기 버전 전용)

아래 예에서는 AzCopy를 사용하여 Azure 테이블 엔터티를 복사하기 위한 다양한 시나리오를 보여 줍니다.

### 로컬 파일 시스템으로 엔터티 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### Azure Blob으로 엔터티 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy는 다음 명명 규칙을 사용하여 로컬 폴더 또는 Blob 컨테이너에 JSON 데이터 파일을 생성합니다.

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

생성된 JSON 데이터 파일은 최소 메타데이터의 페이로드 형식을 따릅니다. 이 페이로드 형식에 대한 자세한 내용은 [테이블 서비스 작업을 위한 페이로드 형식](http://msdn.microsoft.com/library/azure/dn535600.aspx)을 참조하세요.

### 내보내기 파일 분할

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy는 분할 데이터 파일 이름에서 *볼륨 인덱스*를 사용하여 여러 파일을 구분합니다. 볼륨 인덱스는 *파티션 키 범위 인덱스*와 *분할 파일 인덱스*의 두 부분으로 구성됩니다. 두 인덱스는 모두 0부터 시작됩니다.

사용자가 `/PKRS` 옵션(다음 섹션에서 소개함)을 지정하지 않으면 파티션 키 범위 인덱스는 0이 됩니다.

예를 들어 사용자가 `/SplitSize` 옵션을 지정한 후 AzCopy에서 데이터 파일 두 개를 생성한다고 가정해 보겠습니다. 이 경우 결과 데이터 파일 이름은 다음과 같습니다.

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

`/SplitSize` 옵션에 사용할 수 있는 최소값은 32MB입니다. 지정된 대상이 Blob 저장소인 경우 AzCopy는 데이터 파일이 Blob 크기 제한(200GB)에 도달하면 사용자가 `/SplitSize` 옵션을 지정했는지 여부에 관계없이 데이터 파일을 분할합니다.

### 동시에 엔터티 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

사용자가 `/PKRS` 옵션을 지정하면 AzCopy는 엔터티를 내보내는 동시 작업을 시작합니다. 각 작업에서는 파티션 키 범위 하나를 내보냅니다.

`/NC` 옵션도 동시 작업 수를 제어합니다. AzCopy는 `/NC`가 지정되지 않은 경우에도 테이블 엔터티를 복사할 때 `/NC`의 기본값으로 코어 프로세서의 수를 사용합니다. 사용자가 `/PKRS` 옵션을 지정하는 경우 AzCopy는 두 값, 즉 파티션 키 범위와 암시적 또는 명시적으로 지정된 동시 작업의 수 중 더 작은 쪽을 사용하여 시작할 동시 작업의 수를 결정합니다. 자세한 내용을 보려면 명령줄에 `AzCopy /?:NC`를 입력하세요.

### 동시에 엔터티 가져오기

	AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

테이블 엔터티를 내보낼 때 AzCopy는 지정된 대상 폴더 또는 Blob 컨테이너에 매니페스트 파일을 씁니다. 이 매니페스트 파일은 가져오기 프로세스에서 필요한 데이터 파일을 찾고 가져오기 프로세스 중에 데이터 유효성 검사를 수행하는 데 사용됩니다. 매니페스트 파일에는 다음의 명명 규칙이 사용됩니다.

	<account name>_<table name>_<timestamp>.manifest

`/EntityOperation` 옵션은 테이블에 엔터티를 삽입하는 방법을 나타냅니다. 가능한 값은 다음과 같습니다.

- `InsertOrSkip`: 기존 엔터티를 건너뛰거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
- `InsertOrMerge`: 기존 엔터티를 병합하거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
- `InsertOrReplace`: 기존 엔터티를 바꾸거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.

가져오기 시나리오에서는 `/PKRS` 옵션을 지정할 수 없습니다. 동시 작업을 시작하려면 `/PKRS` 옵션을 지정해야 하는 내보내기 시나리오와는 달리 엔터티를 가져올 때는 AzCopy가 기본적으로 동시 작업을 시작합니다. 시작되는 동시 작업의 기본 수는 코어 프로세서의 수와 같습니다. 그러나 `/NC` 옵션을 사용하여 다른 동시 작업 수를 지정할 수 있습니다. 자세한 내용을 보려면 명령줄에 `AzCopy /?:NC`를 입력하세요.


##<a id="versions"></a> AzCopy 버전

| 버전 | 새로운 기능                                                                                      				|
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.0.0**  | **현재 미리 보기 버전. V3.0.0 이후의 모든 기능을 포함합니다. 또한 Azure 파일 저장소에 대한 파일 복사와 Azure 테이블 저장소에 대한 엔터티 복사도 지원합니다.**	
| **V3.0.0**  | **현재 릴리스 버전. 매개 변수 이름을 포함해야 하도록 AzCopy 명령줄 구문이 수정되었으며 명령줄 도움말이 다시 디자인되었습니다. 이 버전에서는 Azure Blob 저장소에 대한 복사만 지원됩니다.**	
| V2.5.1  | /xo 및 /xn 옵션 사용 시 성능을 최적화합니다. 소스 파일 이름의 특수 문자 관련 버그와 사용자가 잘못된 명령줄 구문을 입력한 후의 저널 파일 손상 현상이 수정되었습니다.	
| V2.5.0  | 대규모 복사 시나리오에 맞게 성능이 최적화되었으며 몇 가지 향상된 중요한 사용 편의성을 제공합니다.	
| V2.4.1  | 설치 마법사에서 대상 폴더를 지정할 수 있습니다.                     			
| V2.4.0  | Azure 파일 저장소 파일을 업로드 및 다운로드할 수 있습니다.                       				                              
| V2.3.0  | 읽기 액세스 지역 중복 저장소 계정을 지원합니다.                                                  			|
| V2.2.2  | Azure 저장소 클라이언트 라이브러리 버전 3.0.3을 사용하도록 업그레이드되었습니다.                                            				                    
| V2.2.1  | 동일한 저장소 계정 내에서 대량의 파일을 복사할 때의 성능 문제가 수정되었습니다.            				                                                
| V2.2    | Blob 이름에 대한 가상 디렉터리 구분 기호를 설정할 수 있습니다. 저널 파일 경로를 지정할 수 있습니다.		|
| V2.1    | 효율적인 방법으로 Blob 업로드, 다운로드 및 복사 작업을 지원하기 위한 20가지 이상의 옵션을 제공합니다.		|


##<a id="next-steps"></a> 다음 단계

Azure 저장소 및 AzCopy에 대한 자세한 내용은 다음 리소스를 참조하세요.

### Azure 저장소 설명서

- [Azure 저장소 소개](http://azure.microsoft.com/ko-kr/documentation/articles/storage-introduction/)
- [Blob 저장소에 파일 저장](영문)(http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/)
- [파일 저장소를 사용하여 Azure에서 SMB 파일 공유 만들기](영문)(http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-files/)

### Azure 저장소 블로그 게시물:

- [AzCopy 3.0: General Availability of AzCopy 3.0의 일반 공급 및 테이블 및 파일을 지원하는 AzCopy 4.0의 미리 보기 릴리스 발표](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy 2.5: 대량 복사 시나리오에 맞게 최적화](영문)(http://go.microsoft.com/fwlink/?LinkId=507682)
- [Microsoft Azure 파일 서비스 소개](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: 읽기 액세스 지역 중복 저장소 지원](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: 크로스 계정 Blob 복사 사용](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Microsoft Azure Blob 파일 업로드/다운로드](영문)(http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


<!--HONumber=35.1-->
