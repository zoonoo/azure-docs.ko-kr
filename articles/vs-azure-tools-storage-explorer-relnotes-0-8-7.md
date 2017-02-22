---
title: "Microsoft Azure 저장소 탐색기 0.8.7(미리 보기) | Microsoft Docs"
description: "Microsoft Azure 저장소 탐색기 0.8.7(미리 보기)에 대한 릴리스 정보"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Microsoft Azure Storage 탐색기 0.8.7(미리 보기)
## <a name="overview"></a>개요
이 문서는 Azure 저장소 탐색기 0.8.7 미리 보기 릴리스에 대한 릴리스 정보를 포함합니다.

[Microsoft Azure Storage 탐색기(미리 보기)](./vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 사용할 수 있는 독립 실행형 앱입니다.

## <a name="azure-storage-explorer-087-preview"></a>Azure Storage 탐색기 0.8.7(미리 보기)
### <a name="download-azure-storage-explorer-087-preview"></a>Azure Storage 탐색기 0.8.7(미리 보기) 다운로드
- [Windows용 Azure Storage 탐색기 0.8.7 미리 보기](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac용 Azure Storage 탐색기 0.8.7 미리 보기](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux용 Azure Storage 탐색기 0.8.7 미리 보기](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>새 업데이트
* **활동** 창에서 업데이트, 다운로드 또는 복사 세션을 시작할 때 발생하는 충돌을 해결하는 방법을 선택할 수 있습니다.
* 저장소 리소스의 전체 경로를 보려면 탭 위로 마우스를 가져갑니다.
* 탭을 클릭하면 왼쪽 탐색 창의 해당 위치와 동기화됩니다.

### <a name="fixes"></a>수정 프로그램
* 수정됨: 저장소 탐색기는 이제 macOS에서 신뢰할 수 있는 앱입니다.
* 수정됨: Ubuntu 14.04가 다시 지원됩니다.
* 수정됨: 경우에 따라 구독을 로드할 때 계정 추가 UI가 깜박입니다.
* 수정됨: 왼쪽 탐색 창에 모든 저장소 리소스가 나열되지는 않았습니다.
* 수정됨: 경우에 따라 작업 창에 빈 작업이 표시됩니다.
* 수정됨: 마지막에 닫은 세션의 창 크기가 유지됩니다.
* 수정됨: 상황에 맞는 메뉴를 사용하는 동일한 리소스에 대해 여러 탭을 열 수 있습니다.

### <a name="known-issues"></a>알려진 문제
* 빠른 액세스는 구독 기반 항목에만 작동합니다. 로컬 리소스 또는 키나 SAS 토큰을 통해 연결된 리소스는 이번 릴리스에서 지원되지 않습니다.
* 보유하고 있는 리소스 수에 따라 빠른 액세스 기능으로 대상 리소스에 액세스하는 데 몇 초 정도 걸릴 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하되거나 처리되지 않은 예외가 발생할 수 있습니다.
* MacOS의 저장소 탐색기를 처음 사용할 때 키 집합 액세스 권한을 묻는 메시지가 여러 번 표시될 수 있습니다. 확인 메시지가 다시 표시되지 않도록 **항상 허용**을 선택하는 것이 좋습니다.

## <a name="previous-releases"></a>이전 릴리스
### <a name="features"></a>기능
#### <a name="main-features"></a>주요 기능
* macOS, Linux 및 Windows 버전
* 로그인하여 구독별로 그룹화된 저장소 계정 보기
    * 조직 계정, Microsoft 계정, 2FA, 등 사용
    * 프록시 설정 구성 및 관리
    * 로그아웃하여 계정 제거
* 다음을 사용하여 저장소 계정에 연결
    * 계정 이름 및 키
    * 사용자 지정 끝점(Azure 중국 포함)
    * 저장소 계정에 대한 SAS URI
* Azure Resource Manager 및 클래식 저장소 지원
* blob, blob 컨테이너, 큐, 테이블 또는 파일 공유에 대한 SAS 키 생성
* SAS(공유 액세스 서명)를 사용하여 blob 컨테이너, 큐, 테이블 또는 파일 공유에 연결
* blob 컨테이너, 큐, 테이블 또는 파일 공유에 대한 저장된 액세스 정책 관리
* 저장소 에뮬레이터를 사용하는 로컬 개발 저장소(Windows만 해당)
* blob 컨테이너, 큐 또는 테이블 만들기 및 삭제
* $logs blob 컨테이너 및 $metrics 테이블 보기
* 특정 blob, 큐, 테이블 또는 파일 공유 검색
* 리소스를 공유하고 쉽게 액세스하기 위해 저장소 계정 또는 컨테이너, 큐, 테이블 또는 파일 공유에 대한 직접 연결
* Blob 컨테이너, 테이블, 파일 공유 이름 바꾸기
* CORS 규칙을 관리 및 구성하는 기능
* 저장소 계정에 대한 기본 및 보조 키를 쉽게 복사
* 다운로드 및 업로드 시 데이터 무결성 및 일관성에 대해 MD5 검사 수행
* 검색 상자에서 Blob 컨테이너, 테이블, 큐, 파일 공유 또는 저장소 계정 검색
* 쉬운 탐색을 위해 가장 자주 사용되는 서비스를 빠른 액세스 기능에 고정할 수 있습니다.
* 이제 다른 탭에 있는 여러 편집기를 열 수 있습니다. 한 번 클릭하면 임시 탭이 열리고, 두 번 클릭하면 영구 탭이 열립니다. 임시 탭을 클릭하여 영구 탭으로 만들 수도 있습니다.
* 빠른 컴퓨터에서 큰 파일에 대해 업로드 및 다운로드를 수행할 때 특히 성능 및 안정성이 개선되었습니다.
* 향상된 범위 지정 검색을 다시 추가했으며 범위 지정 개념도 추가했습니다. 가리킴 아이콘을 통해 노드 경로를 입력하고 마우스 오른쪽 단추를 클릭한 후 "여기부터 검색"을 선택하거나 해당 노드 범위를 수동으로 지정합니다. 범위가 지정되면 경로 끝에 검색어를 추가하여 해당 노드부터 심층 검색을 수행할 수 있습니다.
* 밝게(기본값), 어둡게, 고대비 검정 및 고대비 흰색 등의 다양한 테마를 추가했습니다.
* 편집 -> 테마로 이동하여 테마 기본 설정 변경
* 이제 Linux에서는 64비트 OS가 필요합니다.
* 로고가 업데이트되었습니다.
#### <a name="blobs"></a>Blob
* Blob 보기 및 디렉터리 탐색
* Blob 및 폴더 업로드, 다운로드, 삭제 및 복사
* 내용 텍스트 및 그림 blob 열기 및 보기
* blob 속성 및 메타데이터 보기 및 편집
* 접두사별로 blob 검색
* blob 및 blob 컨테이너에 대한 임대 만들기 및 중단
* 업로드할 파일 끌어서 놓기
* blob 및 폴더 이름 바꾸기
* 이제 blob 컨테이너에 빈 "가상" 폴더를 만들 수 있음
* Blob 및 파일 속성을 수정할 수 있음
#### <a name="tables"></a>테이블
* ODATA를 사용하여 엔터티를 보고 쿼리하거나 쿼리 작성기를 사용하여 복잡한 쿼리 만들기
* 엔터티 추가, 편집, 삭제
* CSV 형식으로 테이블 내용 가져오기 및 내보내기(쿼리 결과 내보내기 포함)
* 열 순서 사용자 지정
* 쿼리 저장 기능
#### <a name="queues"></a>큐
* 가장 최근의 32개 메시지 엿보기
* 메시지 추가, 큐에서 제거, 보기
* 큐 지우기
* 사용자가 큐 메시지를 인코딩/디코딩할지 결정할 수 있음
#### <a name="file-shares"></a>파일 공유
* 파일 보기 및 디렉터리 탐색
* 파일 및 디렉터리 업로드, 다운로드, 삭제 및 복사
* 파일 속성 보기
* 파일 및 디렉터리 이름 바꾸기

### <a name="bug-fixes"></a>버그 수정
* 수정됨: 화면 중단 문제
* 수정됨: 향상된 보안

### <a name="known-issues"></a>알려진 문제
* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하되고, macOS 설치에 관리자 권한이 필요할 수 있습니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅숏을 보존되지 않습니다. blob, 파일 및 엔터티에 대한 다른 모든 속성 및 메타데이터는 이름을 바꾸는 동안 유지됩니다.
* Azure Stack은 현재 파일을 지원하지 않으므로 **파일** 노드를 확장하려고 하면 오류가 발생합니다.
* Linux 14.04 설치를 위해서는 gcc 버전을 업데이트하거나 업그레이드해야 합니다. 다음 단계에서는 업그레이드 방법을 보여 줍니다.

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>이전 버전
#### <a name="october-2016-release-version-085"></a>2016년 10월 릴리스(버전 0.8.5)
* [Windows용 다운로드](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Mac용 다운로드](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Linux용 다운로드](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->


