# [Analysis Services 설명서](index.md)

# 개요

## [Analysis Services란?](analysis-services-overview.md)

# 빠른 시작

## [서버 만들기 - 포털](analysis-services-create-server.md)
## [서버 만들기 - PowerShell](analysis-services-create-powershell.md)
## [서버 방화벽 구성 - 포털](analysis-services-qs-firewall.md)

# 자습서

## [1 - 포털에서 샘플 모델 추가](analysis-services-create-sample-model.md) 
## [2 - 서버 관리자 및 사용자 역할 구성](tutorials/analysis-services-tutorial-roles.md)
## [3 - Power BI Desktop으로 연결](tutorials/analysis-services-tutorial-pbid.md)

# 개념

## [인증 및 사용자 권한](analysis-services-manage-users.md)
## [서비스 보안 주체를 사용한 자동화](analysis-services-service-principal.md)
## [클라이언트 라이브러리](analysis-services-data-providers.md)
## [호환성 수준](analysis-services-compat-level.md)
## [온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)
## [서버에 연결](analysis-services-connect.md)
## [고가용성](analysis-services-bcdr.md)
## [지원되는 데이터 원본](analysis-services-datasource.md)

# 방법 

## 서버
### [Backup 및 복원](analysis-services-backup.md)
### [서버 이름 별칭 구성](analysis-services-server-alias.md)
### [스케일 아웃 구성](analysis-services-scale-out.md)
### [온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)
### [Azure Virtual Network에서 데이터 원본에 게이트웨이 사용](analysis-services-vnet-gateway.md)
### [서버 관리](analysis-services-manage.md)
### [서버 메트릭 모니터링](analysis-services-monitor.md)
### [진단 로깅 설정](analysis-services-logging.md)
### [서비스 주체 만들기 - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
### [서비스 주체 만들기 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
### [서버 관리자 역할에 서비스 주체 추가](analysis-services-addservprinc-admins.md)

## 모델
### [Power BI Desktop 파일 가져오기](analysis-services-import-pbix.md)
### [Azure Portal에서 모델 만들기(미리 보기)](analysis-services-create-model-portal.md)
### [Visual Studio에서 배포(SSDT)](analysis-services-deploy.md)
### [REST API 사용하여 새로 고침](analysis-services-async-refresh.md)
### [Visual Studio에서 데이터 모델링 - Adventure Works(1400) 자습서](tutorials/aas-adventure-works-tutorial.md)
#### [1 - 테이블 형식 모델 프로젝트 만들기](tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)
#### [2 - 데이터 가져오기](tutorials/aas-lesson-2-get-data.md)
#### [3 - 날짜 테이블로 표시](tutorials/aas-lesson-3-mark-as-date-table.md) 
#### [4 - 관계 만들기](tutorials/aas-lesson-4-create-relationships.md) 
#### [5 - 계산된 열 만들기](tutorials/aas-lesson-5-create-calculated-columns.md)
#### [6 - 측정값 만들기](tutorials/aas-lesson-6-create-measures.md)  
#### [7 - 핵심 성과 지표 만들기](tutorials/aas-lesson-7-create-key-performance-indicators.md)  
#### [8 - 큐브 뷰 만들기](tutorials/aas-lesson-8-create-perspectives.md) 
#### [9 - 계층 구조 만들기](tutorials/aas-lesson-9-create-hierarchies.md) 
#### [10 - 파티션 만들기](tutorials/aas-lesson-10-create-partitions.md) 
#### [11 - 역할 만들기](tutorials/aas-lesson-11-create-roles.md)
#### [12 - Excel에서 분석](tutorials/aas-lesson-12-analyze-in-excel.md)
#### [13 - 배포](tutorials/aas-lesson-13-deploy.md)
#### [추가 단원 - 세부 정보 행](tutorials/aas-supplemental-lesson-detail-rows.md)
#### [추가 단원 - 동적 보안](tutorials/aas-supplemental-lesson-dynamic-security.md)
#### [추가 단원 - 불규칙한 계층 구조](tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  

## 보안
### [데이터베이스 사용자 관리](analysis-services-database-users.md)
### [서버 관리자 관리](analysis-services-server-admins.md)

## 연결
### [Excel과 연결](analysis-services-connect-excel.md)
### [Power BI와 연결](analysis-services-connect-pbi.md)
### [.odc 파일 만들기](analysis-services-odc.md)

# 참고 자료

## [PowerShell](analysis-services-powershell.md)
## [REST (영문)](/rest/api/analysisservices)
## [DAX](https://msdn.microsoft.com/library/gg413422.aspx)
## [파워 쿼리 M](https://msdn.microsoft.com/library/mt211003.aspx)
## [테이블 형식 모델 스크립팅 언어(TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)
## [테이블 형식 개체 모델(TOM)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo)
## [리소스 및 개체 제한](analysis-services-capacity-limits.md)

# 리소스

## [샘플](analysis-services-samples.md)
## [Analysis Services 팀 블로그](https://blogs.msdn.microsoft.com/analysisservices/)
## [Azure Analysis Services 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAnalysisServices)
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=intelligence-analytics)
## [Azure 상태](https://azure.microsoft.com/status/)
## [사용자 의견](https://feedback.azure.com/forums/556165-azure-analysis-services)
## [가격](https://azure.microsoft.com/pricing/details/analysis-services/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-analysis-services)
## [비디오](https://azure.microsoft.com/resources/videos/index/?services=analysis-services&sort=newest)










