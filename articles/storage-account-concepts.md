<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" services="storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# 저장소 계정 개념

## 저장소 계정 복제 옵션

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## 저장소 계정 끝점

저장소 계정의 *끝점*은 Blob, 테이블, 큐 또는 파일에 액세스하기 위한 가장 높은 수준의 네임스페이스를 나타냅니다. 저장소 계정의 기본 끝점 형식은 다음과 같습니다.

-   Blob 서비스: http://*mystorageaccount*.blob.core.windows.net

-   테이블 서비스: http://*mystorageaccount*.table.core.windows.net

-   큐 서비스: http://*mystorageaccount*.queue.core.windows.net

-   파일 서비스: http://*mystorageaccount*.file.core.windows.net

저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 끝점에 추가하여 작성됩니다. 예를 들어, Blob 주소의 형식은 다음과 같습니다. http://mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

## 저장소 계정 보안

저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

저장소 계정 액세스 키를 다른 사람과 공유하지 않는 것이 좋습니다. 계정이 노출되었다고 생각되면 포털 내에서 액세스 키를 다시 생성할 수 있습니다. 저장소 계정을 선택하고 **액세스 키 관리**를 선택합니다.

액세스 키를 제공하지 않고 저장소 리소스에 대한 액세스를 허용하려는 경우에는 *공유 액세스 서명*을 사용할 수 있습니다. 공유 액세스 서명에서는 정의된 간격으로 지정된 권한을 사용하여 계정의 리소스에 액세스할 수 있습니다. 자세한 내용은 [공유 액세스 서명 자습서][공유 액세스 서명 자습서]를 참조하세요.

## 저장소 계정 메트릭 및 로깅

-   **최소 및 상세 메트릭** 저장소 계정에 대해 모니터링 설정에서 최소 또는 상세 메트릭을 구성할 수 있습니다. *최소 메트릭*은 수신/송신, 가용성, 대기 시간, 성공률 등 Blob, 테이블 및 큐 서비스에 대해 집계되는 데이터에 대한 메트릭을 수집합니다. *상세 메트릭*은 동일한 메트릭에 대해 서비스 수준 집계뿐만 아니라 작업 수준 세부 정보를 수집합니다. 세부 정보 표시 메트릭을 사용하면 응용 프로그램 작업 중 발생하는 문제를 더 면밀하게 분석할 수 있습니다. 사용 가능한 메트릭의 전체 목록은 [저장소 분석 메트릭 테이블 스키마][저장소 분석 메트릭 테이블 스키마](영문)를 참조하세요. 저장소 모니터링에 대한 자세한 내용은 [저장소 분석 메트릭 정보][저장소 분석 메트릭 정보]를 참조하세요.

-   **로깅** 로깅은 Blob, 테이블, 큐에 대한 읽기, 쓰기, 삭제 요청 로깅을 사용할 수 있는 저장소 계정의 구성 가능한 기능입니다. 로깅은 Azure 관리 포털에서 구성하지만 로그를 관리 포털에서 볼 수는 없습니다. 로그는 저장소 계정, $logs 컨테이너에서 저장되고 액세스됩니다. 자세한 정보는 [저장소 분석 개요][저장소 분석 개요]를 참조하세요.

## Azure 저장소 및 기타 서비스 배치를 위한 선호도 그룹

*선호도 그룹*은 Azure 저장소 계정을 사용하여 Azure 서비스와 VM을 지리적으로 그룹화한 것을 말합니다. 선호도 그룹은 동일한 데이터 센터 또는 대상 사용자 그룹 인근의 컴퓨터 작업을 찾아 서비스 성능을 향상시킬 수 있습니다. 또한 같은 선호도 그룹에 속한 다른 서비스에서 저장소 계정의 데이터에 액세스할 경우 송신 요금이 청구되지 않습니다.

  [storage-replication-options]: ../includes/storage-replication-options.md
  [공유 액세스 서명 자습서]: ../storage-dotnet-shared-access-signature-part-1/
  [저장소 분석 메트릭 테이블 스키마]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343264.aspx
  [저장소 분석 메트릭 정보]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343258.aspx
  [저장소 분석 개요]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343268.aspx
