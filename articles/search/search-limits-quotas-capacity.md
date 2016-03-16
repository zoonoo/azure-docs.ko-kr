<properties
	pageTitle="Azure 검색의 서비스 제한 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="용량 계획에 사용되는 서비스 제한 및 Azure 검색에 대한 요청 및 응답의 최대 제한입니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Azure 검색의 서비스 제한 사항

저장소, 워크로드 및 인덱스, 문서, 기타 개체의 수량에 대한 최대 제한은 **무료**, **기본** 또는 **표준** 가격 책정 계층에서 Azure 검색을 추가하는지 여부에 따라 달라집니다.

- **무료**는 Azure 구독과 함께 제공되는 다중 테넌트 공유 서비스입니다. 전용 리소스에 등록하기 전에 서비스를 경험해 볼 수 있는 기존 구독자를 위한 추가 비용이 없는 옵션입니다. 
- **기본(미리 보기)**은 소규모의 프로덕션 워크로드를 위한 전용 컴퓨팅 리소스를 제공합니다. 이 계층은 현재 미리 보기 상태이며 [할인된 요금](https://azure.microsoft.com/pricing/details/search/)으로 제공됩니다.
- **표준**은 최소 구성을 포함하여 모든 수준에서 더 많은 저장소 및 처리 용량을 사용하며 서비스에서만 사용되는 전용 컴퓨터에서 실행됩니다. 표준은 두 가지 수준(S1 및 S2)으로 제공됩니다. 

## 계층 제한

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] QPS(초당 쿼리 수)는 다르며 특히 공유 서비스에서 그렇습니다. 이는 처리량이 사용 가능한 대역폭 및 시스템 리소스에 대한 경합에 따라 다르기 때문입니다. 공유 서비스를 백업하는 Azure 계산 및 저장소 리소스는 여러 구독자가 공유하므로 동시에 실행되는 다른 작업의 수에 따라 솔루션의 QPS가 항상 달라집니다. 표준 수준에서는 매개 변수를 더 잘 제어할 수 있기 때문에 QPS를 더 근접하게 예상할 수 있습니다. 워크로드에 대한 QPS를 계산하는 방법에 대한 지침은 [검색 솔루션 관리](search-manage.md)에서 모범 사례 섹션을 참조하세요.

## API 키 제한

API 키는 서비스 인증에 사용됩니다. 두 가지 형식이 있습니다. 관리자 키는 요청 헤더에서 지정되고 서비스에 전체 읽기/쓰기 액세스 권한을 부여합니다. 쿼리 키는 읽기 전용이며 URL에 지정되고 일반적으로 클라이언트 응용 프로그램에 배포됩니다.

- 서비스당 최대 2개의 관리자 키
- 서비스당 최대 50개의 쿼리 키

## 요청 제한

- 요청당 최대 16MB <sup>1</sup>
- URL 길이 최대 8KB
- 인덱스 업로드, 병합 또는 삭제 배치당 최대 1000개의 문서
- $orderby 절에 최대 32개의 필드
- 최대 검색어 크기는 UTF-8 인코딩된 텍스트로 32766바이트(32KB에서 2바이트를 뺀 값)입니다.

## 응답 제한

- 검색 결과 페이지당 반환되는 문서 최대 1000개
- 제안 API 요청당 반환되는 제안 최대 100개

<sup>1</sup> Azure 검색에서는 이론적 제한으로 달리 제한되지 않는 개별 필드 또는 컬렉션의 콘텐츠에 대한 실질적 한도를 부과하기 위해 요청 본문에 16MB의 상한이 적용됩니다(필드 컴퍼지션 및 제한 사항에 대한 자세한 내용은 [지원되는 데이터 형식](https://msdn.microsoft.com/library/azure/dn798938.aspx) 참조).

<!---HONumber=AcomDC_0302_2016-->