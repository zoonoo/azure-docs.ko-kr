---
title: Azure stack 저장소 차이점 및 고려 사항 | Microsoft Docs
description: Azure stack storage 및 Azure Stack 배포 고려 사항과 함께 Azure storage 간의 차이점을 이해 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 1393dd32aea8cb6d348092ea1fc56752f659beab
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717877"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack 저장소: 차이점 및 고려 사항

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 저장소는 Microsoft Azure Stack의 storage 클라우드 서비스의 집합입니다. Azure Stack 저장소 blob, 테이블, 큐 및 Azure와 일관 된 의미 체계를 사용 하 여 계정 관리 기능을 제공합니다.

이 문서는 Azure Storage 서비스에서 알려진된 Azure Stack 저장소의 차이 요약합니다. 또한 Azure Stack을 배포 하는 경우를 고려해 야 할 사항을 나열 합니다. 전역 Azure 및 Azure Stack 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

## <a name="cheat-sheet-storage-differences"></a>참고 자료: 저장소의 차이

| 기능 | Azure (전역) | Azure Stack |
| --- | --- | --- |
|File Storage|지원 되는 클라우드 기반 SMB 파일 공유|아직 지원 되지 않음
|미사용 데이터에 대 한 azure storage 서비스 암호화|256 비트 AES 암호화 합니다. 고객 관리 키를 사용 하 여 Key Vault에 암호화를 지원 합니다.|BitLocker 128 비트 AES 암호화 합니다. 고객 관리 키를 사용 하 여 암호화가 지원 되지 않습니다.
|Storage 계정 유형|범용 V1, V2 및 Blob storage 계정|범용 V1에만 해당 합니다.
|복제 옵션|로컬 중복 저장소, 지역 중복 저장소, 읽기 액세스 지역 중복 저장소 및 영역 중복 저장소|로컬 중복 저장소입니다.
|Premium Storage|완전하게 지원|성능 제한이 있지만 프로비저닝할 수 있습니다 하거나 보장 합니다.
|관리 디스크|프리미엄 및 표준 지원|1808 이상 버전을 사용할 때 지원 됩니다.
|Blob 이름|1,024 자 (2,048 바이트)|880 문자 (1,760 바이트)
|블록 blob의 최대 크기|4.75TB (100 X 50,000 개 블록)|4.75TB 1802 업데이트 또는 최신 버전 (100 x 50,000 개 블록) 추가 합니다. 50,000 X 4MB((약 195GB) 이전 버전에 대 한 합니다.
|페이지 blob 스냅숏 복사|지원 되는 실행 중인 VM에 연결 된 백업 Azure 관리 되지 않는 VM 디스크|아직 지원 되지 않습니다.
|페이지 blob 증분 스냅숏 복사|Premium 및 지원 되는 표준 Azure 페이지 blob|아직 지원 되지 않습니다.
|Blob storage에 대 한 저장소 계층|핫, 쿨 및 보관 저장소 계층입니다.|아직 지원 되지 않습니다.
|Blob storage에 대 한 일시 삭제|사용 가능한 일반|아직 지원 되지 않습니다.
|페이지 blob의 최대 크기|8 TB|1TB
|페이지 blob 페이지 크기|512바이트|4KB
|테이블 파티션 키와 행 키 크기|1,024 자 (2,048 바이트)|400 자 (800 바이트)
|Blob 스냅숏|하나의 blob 스냅숏의 최대 수는 제한 되지 않습니다.|하나의 blob 스냅숏의 최대 수는 1,000입니다.
|저장소에 대 한 azure AD 인증|미리 보기|아직 지원 되지 않습니다.
|변경할 수 없는 Blob|사용 가능한 일반|아직 지원 되지 않습니다.
|방화벽 및 저장소에 대 한 가상 네트워크 규칙|사용 가능한 일반|아직 지원 되지 않습니다.|

저장소 메트릭 사용 하 여 차이점도 있습니다.

* Storage 메트릭에서는 트랜잭션 데이터는 내부 또는 외부 네트워크 대역폭을 지지 않습니다.
* Storage 메트릭에서는 트랜잭션 데이터에는 탑재 된 디스크에 대 한 가상 머신 액세스를 포함 되지 않습니다.

## <a name="api-version"></a>API 버전

다음 버전은 Azure Stack의 Storage를 사용 하 여 지원 됩니다.

Azure Storage 서비스 Api:

1811 업데이트 또는 최신 버전:

 - [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
 - [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

1809 업데이트 1802 업데이트:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

이전 버전:

- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure Storage 서비스 관리 Api:

- [2015-05-01-미리 보기](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

이전 버전:

 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-05-01-미리 보기](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 
## <a name="sdk-versions"></a>SDK 버전

Azure Stack 저장소 클라이언트 라이브러리를 지원합니다.

| 클라이언트 라이브러리 | Azure Stack 지원 되는 버전 | 링크                                                                                                                                                                                                                                                                                                                                     | 끝점 지정       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | 6.2.0 8.7.0 하 합니다.          | NuGet 패키지:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | app.config 파일              |
| 자바           | 6.1.0 4.1.0에서           | Maven 패키지:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | 연결 문자열 설정      |
| Node.js        | 2.7.0 1.1.0에서           | NPM 링크:<br>https://www.npmjs.com/package/azure-storage<br>(예: 실행 "npm 설치 azure-storage@2.7.0")<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | 서비스 인스턴스 선언 |
| C++            | 3.1.0 2.4.0에서           | NuGet 패키지:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | 연결 문자열 설정      |
| PHP            | 1.0.0 0.15.0에서          | GitHub 릴리스:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>작성기를 통해 설치 (아래 세부 정보 참조)                                                                                                                                                                                                                  | 연결 문자열 설정      |
| Python         | 1.0.0 0.30.0에서          | GitHub 릴리스:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | 서비스 인스턴스 선언 |
| Ruby           | 1.0.1 0.12.1에서          | RubyGems 패키지:<br>일반적인:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>큐: https://rubygems.org/gems/azure-storage-queue/<br>테이블: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-ruby/releases | 연결 문자열 설정      |

## <a name="next-steps"></a>다음 단계

* [Azure Stack 저장소 개발 도구를 사용 하 여 시작](azure-stack-storage-dev.md)
* [Azure Stack 저장소 소개](azure-stack-storage-overview.md)
