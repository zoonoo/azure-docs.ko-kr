<properties title="DocumentDB limits for the preview release" pageTitle="DocumentDB limits for the preview release | Azure" description="Learn about the limits and quota enforcements of DocumentDB for the preview release." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# DocumentDB 미리 보기 릴리스의 한도

다음 표에서는 DocumentDB 미리 보기 릴리스의 한도 및 할당량 적용에 대해 설명합니다. 대부분의 경우 한도는 사용자 의견을 얻기 위해 또는 현재 용량 제약 조건에 따라 적용됩니다. 한도 완화가 필요한 비즈니스 요구가 있을 경우 Microsoft로 문의하시면 일반 사용자 제공의 제약 조건 내에서 요구를 수용하기 위해 최선을 다할 것입니다.

| 엔터티                                                         | 할당량(미리 보기 릴리스의 표준 제공)                                                                                          |
|----------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 데이터베이스 계정                                              | 5                                                                                                                             |
| 데이터베이스 계정당 데이터베이스 수                            | 100                                                                                                                           |
| 데이터베이스 계정당 사용자 수 - 모든 데이터베이스              | 500,000                                                                                                                       |
| 데이터베이스 계정당 사용 권한 수 - 모든 데이터베이스           | 2,000,000                                                                                                                     |
| 데이터베이스 계정당 첨부 파일 저장소                           | 2GB                                                                                                                           |
| 데이터베이스 계정당 최대 용량 단위 수                          | 5                                                                                                                             |
| 용량 단위당 컬렉션 수                                          | 3                                                                                                                             |
| 문서가 1개 이상 포함된 컬렉션당 최소 할당 저장소               | 3.3GB                                                                                                                         |
| 문서가 1개 이상 포함된 컬렉션당 최소 할당 처리량               | 667 RU                                                                                                                        |
| 컬렉션의 탄력성                                                | 0-10GB                                                                                                                        |
| 컬렉션당 최대 요청 단위/초                                     | 2000                                                                                                                          |
| 컬렉션당 저장 프로시저, 트리거 및 UDF 수                       | 각 5개                                                                                                                        |
| 저장 프로시저 및 트리거의 최대 실행 시간                       | 5초                                                                                                                           |
| 프로비전된 문서 저장소/용량 단위                               | 10GB                                                                                                                          |
| 프로비전된 요청 단위/초/용량 단위                              | 2000                                                                                                                          |
| 데이터베이스(5개 용량 단위)당 최대 문서 저장소                 | 50GB                                                                                                                          |
| Id 속성의 최대 길이                                            | 255자                                                                                                                         |
| 페이지당 기본 항목 수                                          | 100                                                                                                                           |
| 페이지당 최대 항목 수                                          | 1000                                                                                                                          |
| 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF의 최대 요청 크기 | 16KB                                                                                                                          |
| 최대 응답 크기                                                 | 1MB                                                                                                                           |
| 컬렉션당 최대 고유 경로 수                                     | 100                                                                                                                           |
| String                                                         | 모든 문자열은 UTF-8 인코딩을 준수해야 합니다. UTF-8은 가변 너비 인코딩이므로 UTF-8바이트를 사용하여 문자열 크기를 확인합니다. |
| 속성 또는 값의 최대 길이                                       | 실질적인 한도 없음                                                                                                            |
| 쿼리당 최대 UDF 수                                             | 1                                                                                                                             |
| 쿼리당 최대 JOIN 수                                            | 2                                                                                                                             |
| 쿼리당 최대 AND 절 수                                          | 3                                                                                                                             |
| 쿼리당 최대 OR 절 수                                           | 3                                                                                                                             |


