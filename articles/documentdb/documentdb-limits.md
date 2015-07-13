<properties 
	pageTitle="DocumentDB 한도 및 할당량 | Azure" 
	description="DocumentDB의 한도 및 할당량 적용에 대해 알아봅니다." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="mimig"/>


#DocumentDB 한도 및 할당량

다음 표에서는 DocumentDB의 한도 및 할당량 적용에 대해 설명합니다. 별표(*)가 표시된 할당량은 [Azure 지원부에 문의하여 조정할 수 있습니다](documentdb-increase-limits.md).

|엔터티 |할당량(표준 제공)|
|-------|--------|
|데이터베이스 계정* |5
|데이터베이스 계정당 데이터베이스 수 |100
|데이터베이스 계정당 사용자 수 - 모든 데이터베이스 |500,000
|데이터베이스 계정당 사용 권한 수 - 모든 데이터베이스 |2,000,000
|데이터베이스 계정당 첨부 파일 저장소(미리 보기 기능) |2 GB
|컬렉션당 최대 요청 단위/초 |2500
|컬렉션당 저장 프로시저, 트리거 및 UDF 수* |각 25개
|저장 프로시저 및 트리거의 최대 실행 시간 |5초
|프로비전된 문서 저장소/컬렉션 |10 GB
|데이터베이스 계정당 최대 컬렉션* |100
|데이터베이스(100개 컬렉션)당 최대 문서 저장소* |1TB
|Id 속성의 최대 길이 |255자
|페이지당 최대 항목 수 |1000
|문서 및 첨부 파일의 최대 요청 크기 |512KB
|저장 프로시저, 트리거 및 UDF의 최대 요청 크기 |512KB
|최대 응답 크기 |1MB
|문자열 |모든 문자열은 UTF-8 인코딩을 준수해야 합니다. UTF-8은 가변 너비 인코딩이므로 UTF-8바이트를 사용하여 문자열 크기를 확인합니다.
|속성 또는 값의 최대 길이 |실질적인 한도 없음
|쿼리당 최대 UDF 수* |1
|쿼리당 최대 기본 제공 함수 수 |실질적인 한도 없음
|쿼리당 최대 JOIN 수* |2
|쿼리당 최대 AND 절 수* |5
|쿼리당 최대 OR 절 수* |5
|IN 식당 최대 값 수* |100
|분당 최대 컬렉션 생성 수* |5
|분당 최대 크기 조정 작업 수* |5
 

<!---HONumber=July15_HO1-->