<properties
	pageTitle="R로 실험 확장 | Microsoft Azure"
	description="R 스크립트 실행 모듈을 사용하여 R 언어를 통해 Azure 기계 학습 스튜디오의 기능을 확장하는 방법."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="garye" />


#R을 사용하여 실험 확장 

[R 스크립트 실행][execute-r-script] 모듈을 사용하여 R 언어를 통해 기계 학습 스튜디오의 기능을 확장할 수 있습니다.

이 모듈에서는 여러 입력 데이터 집합을 허용하고 출력으로 단일 데이터 집합을 생성합니다. [R 스크립트 실행][execute-r-script] 모듈의 **R 스크립트** 매개 변수에 R 스크립트를 입력할 수 있습니다.

다음과 비슷한 코드를 사용하여 모듈의 각 입력 포트에 액세스합니다.

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##현재 설치된 모든 패키지 나열

설치된 패키지 목록을 변경할 수 있습니다. 전체 목록을 얻으려면 [R 스크립트 실행][execute-r-script] 모듈에 다음 줄을 포함하여 출력 데이터 집합에 목록을 보냅니다.

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

패키지 목록을 보려면 [CSV로 변환][convert-to-csv]과 같은 변환 모듈을 [R 스크립트 실행][execute-r-script] 모듈의 출력에 연결하고, 실험을 실행한 다음, 변환 모듈의 출력을 클릭하고 **다운로드**를 선택합니다.

##패키지 가져오기

[R 스크립트 실행][execute-r-script] 모듈 및 압축된 패키지 아카이브에서 다음 명령을 사용하여 스테이징된 기계 학습 스튜디오 리포지토리에서 아직 설치되지 않은 패키지도 가져올 수 있습니다.

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

여기서 `my_favorite_package.zip`에는 패키지의 zip이 포함되어 있습니다.

##설치된 패키지 나열

사용자의 편의를 위해 현재 릴리스에 포함된 패키지 목록이 다음 표에 제공됩니다.

현재 사용 가능한 전체 패키지 목록을 가져오려면 위의 **현재 설치된 모든 패키지 나열**이라는 섹션을 참조하세요. 현재 R 설명서는 [여기](http://cran.r-project.org/manuals.html)에 있습니다.

- [A-E로 시작하는 R 모듈]
- [F-L로 시작하는 R 모듈]
- [M-R로 시작하는 R 모듈]
- [S-Z로 시작하는 R 모듈]

[A-E로 시작하는 R 모듈]: #r-modules-beginning-with-a-through-e
[F-L로 시작하는 R 모듈]: #r-modules-beginning-with-f-through-l
[M-R로 시작하는 R 모듈]: #r-modules-beginning-with-m-through-r
[S-Z로 시작하는 R 모듈]: #r-modules-beginning-with-s-through-z


###A-E로 시작하는 R 모듈

| 패키지 이름 | 패키지 설명 |
| ------------ | ------------------- |
| abc | ABC(Approximate Bayesian Computation)의 도구 |
| abind | 다차원 배열 결합 |
| 보험 통계 | 보험 통계 함수 |
| ade4 | 생태계 데이터 분석: 환경 과학의 예비 및 유클리드 메서드 |
| AdMit | Adaptive Mixture of Student-t 분산 |
| aod | Analysis of Overdispersed Data |
| ape | Analyses of Phylogenetics and Evolution |
| 근사기 | 복잡한 컴퓨터 코드의 Bayesian 예측 |
| arm | 회귀 및 여러 수준/계층 구조 모델을 사한 데이터 분석 |
| arules | 연결 규칙 및 자주 발생하는 항목 집합 마이닝 |
| arulesViz | 연결 규칙 및 자주 발생하는 항목 집합 시각화 |
| ash | David Scott ASH 루틴 |
| assertthat | 쉬운 사전 및 사후 어설션 |
| AtelieR | 통계 추론의 기본 개념을 가르치고 기본적인 Bayesian 테스트를 수행하는 GTK GUI |
| BaBooN | Bayesian 부트스트랩 예측 평균 일치 - 불연속 데이터의 다중 및 단일 대체 |
| BACCO | BACCO(Bayesian Analysis of Computer Code Output) |
| BaM | Jeff Gill이작성한 서적의 함수 및 데이터 집합 |
| bark | Bayesian Additive Regresssion Kernels |
| BAS | Bayesian Adaptive Sampling을 사용한 Bayesian 모델 평균화 |
| 기본 | R 기본 패키지 |
| BayesDA | Bayesian 데이터 분석 서적의 함수 및 데이터 집합 |
| bayesGARCH | Student-t 혁신을 사용하는 GARCH(1,1) 모델의 Bayesian 추정 |
| bayesm | 마케팅/마이크로 계량 경제학의 Bayesian 인터페이스 |
| bayesmix | JAGS를 사용하는 Bayesian 혼합 모델 |
| bayesQR | Bayesian 변위치 회귀 |
| bayesSurv | 유연한 오차 및 임의 효과 분포를 사용하는 Bayesian 생존 회귀 |
| Bayesthresh | 범주형 데이터를 위한 Bayesian 임계값 혼합 효과 모델 |
| BayesTree | 트리 기반 모델을 위한 Bayesian 메서드 |
| BayesValidate | BayesValidate 패키지 |
| BayesX | 소프트웨어 패키지 BayesX를 수반하는 R 유틸리티 |
| BayHaz | Bayesian 위험 비율 추정을 위한 R 함수 |
| bbemkr | 가우스 오차를 사용하는 다변량 커널 회귀를 위한 Bayesian 대역폭 예측 |
| BCBCSF | 선택한 기능으로 편향 수정된 Bayesian 분류 |
| BCE | Bayesian 컴퍼지션 평가자: 생물 지표 데이터에서 샘플(분류) 컴퍼지션 예측 |
| bclust | 고차원 데이터를 클러스터링하는 데 적합한 스파이크 앤 슬래브(spike and slab) 계층 모델을 사용하는 Bayesian 클러스터링 |
| bcp | 변경 지점 문제의 Bayesian 분석을 수행하는 패키지 |
| BenfordTests | 벤포드 법칙 준수를 평가하기 위한 통계 테스트 |
| bfp | Bayesian 분수 다항식 |
| BH | Boost C++ 헤더 파일 |
| bisoreg | 번스타인 다항식을 사용하는(Bernstein Polynomials) Bayesian 등장의 회귀(Isotonic Regression) |
| bit | 1비트 부울 벡터의 클래스 |
| bitops | 비트 연산자 |
| BLR | Bayesian 선형 회귀 |
| BMA | Bayesian 모델 평균화 |
| Bmix | Stick-breaking Mixtures를 위한 Bayesian 샘플링 |
| BMS | Bayesian 모델 평균화 라이브러리 |
| bnlearn | Bayesian 네트워크 구조 학습, 매개 변수 학습 및 추론 |
| boa | MCMC용 BOA(Bayesian Output Analysis) 프로그램 |
| Bolstad | Bolstad 함수 |
| boot | 부트스트랩 함수(Angelo Canty가 S용으로 고안) |
| bootstrap | 부트스트랩에 대한 소개 서적을 위한 함수 |
| bqtl | Bayesian QTL 매핑 도구 키트 |
| BradleyTerry2 | Bradley-Terry 모델 |
| brew | 보고서 생성을 위한 템플릿 프레임워크 |
| brglm | 이항 응답 일반화 선형 모델의 편향 감소 |
| bspec | Bayesian 스펙트럼 유추 |
| bspmma | bspmma: 메타 분석을 위한 Bayesian 준모수 변수 모델 |
| BVS | Bayesian 변형 선택: 유전적 연결 연구를 위한 Bayesian 모델 불확실성 기술 |
| cairoDevice | 카이로 기반 크로스 플랫폼 안티앨리어싱 그래픽 장치 드라이버 |
| calibrator | 복잡한 컴퓨터 코드의 Bayesian 보정 |
| car | Companion to Applied Regression |
| caret | 분류 및 회귀 교육 |
| catnet | 범주형 Bayesian 네트워크 유추 |
| caTools | 도구: 이동 창 통계, GIF, Base64, ROC AUC 등. |
| chron | 날짜와 시간을 처리할 수 있는 시간순 개체 |
| class | 분류 함수 |
| 프로비전 | 클러스터 분석 확장 Rousseeuw 외. |
| clusterSim | 데이터 집합에 최적인 클러스터링 프로시저 검색 |
| coda | MCMC용 출력 분석 및 진단 |
| codetools | R용 코드 분석 도구 |
| coin | 순열 테스트 프레임워크의 조건부 유추 프로시저 |
| colorspace | 색상 공간 조작 |
| combinat | 조합론 유틸리티 |
| compiler | R 컴파일러 패키지 |
| corpcor | 공변성 및 (부분) 상관의 효율적 추정 |
| cslogistic | 조건부로 지정된 로지스틱 회귀 |
| ctv | CRAN 작업 보기 |
| cubature | 하이퍼큐브에 대한 적응 다변량 통합 |
| data.table | data.frame 확장 |
| datasets | R 데이터 집합 패키지 |
| date | 날짜 처리 함수 |
| dclone | 최대 유사도 메서드를 위한 데이터 복제 및 MCMC 도구 |
| deal | 혼합 변수를 사용하여 Bayesian 네트워크 학습 |
| Deducer | Deducer: R을 위한 데이터 분석 GUI |
| DeducerExtras | 추론 프로그램용 추가 대화 상자 및 함수 |
| deldir | Delaunay 삼각형 및 Dirichlet(Voronoi) 분할(Tessellation). |
| DEoptimR | 순수 R의 차등 진화 최적화 |
| deSolve | 일반 차등 방정식(ODE), 부분 차등 방정식(PDE), 차등 대수 방정식(DAE) 및 지연 차등 방정식(DDE)의 초기값 문제에 대한 일반 해 찾기 |
| devtools | R 코드 개발을 용이하게 하는 도구 |
| dichromat | Dichromats에 대한 색 구성표 |
| digest | R 개체의 암호화 해시 요약 만들기 |
| distrom | 분산 다항 회귀 |
| dlm | 동적 선형 모델의 Bayesian 및 유사도 분석 |
| doSNOW | snow 패키지의 Foreach 병렬 어댑터 |
| dplyr | dplyr: 데이터 조작 문법 |
| DPpackage | R에서 Bayesian 비모수 모델링 |
| dse | 동적 시스템 추정(시간 시리즈 패키지) |
| e1071 | 통계 부서의 기타 함수(e1071), TU Wien |
| EbayesThresh | 경험적 Bayes 임계 처리 및 관련 메서드 |
| ebdbNet | 동적 Bayesian 네트워크의 경험적 Bayes 추정 |
| 효과 | 선형, 일반화된 선형, 다항식 로짓(Multinomial-Logit), 비례 오즈 로짓(Proportional-Odds Logit) 모델 및 혼합 효과 모델의 효과 표시 |
| emulator | 컴퓨터 프로그램의 Bayesian 에뮬레이션 |
| ensembleBMA | Ensembles 및 Bayesian 모델 평균화를 사용하여 확률 예측 |
| entropy | 엔트로피, 상호 정보 및 관련 수량 추정 |
| EvalEst | 동적 시스템 추정 - 확장 |
| evaluate | 기본값보다 자세한 정보를 제공하는 구문 분석 및 평가 도구 |
| evdbayes | 극단적인 값 이론의 Bayesian 분석 |
| evora | 위험 예측 분석을 위한 후생 변수 이상값 |
| exactLoglinTest | 로그 선형 모델에 대한 몬테 칼로 정확 검정 |
| expm | 행렬 지수 |
| extremevalues | 일변량 이상값 검색 |


###F-L로 시작하는 R 모듈

| 패키지 이름 | 패키지 설명 |
| ------------ | ------------------- |
| factorQR | Bayesian 변위치 회귀 요인 모델 |
| faoutlier | 요인 분석 및 SEM에 영향을 주는 사례 검색 메서드 |
| fitdistrplus | 비중도절단 및 중도절단 데이터에 맞게 모수 분산 지원 |
| FME | 역모델링, 민감도, 식별성, Monte-Carlo 분석을 위한 유연한 모델링 환경 |
| foreach | R을 위한 Foreach 루프 구문 |
| forecast | 시계열 및 선형 모델의 예측 함수 |
| foreign | Minitab, S, SAS, SPSS, Stata, Systat, Weka, dBase 등으로 저장된 읽기 데이터 |
| formatR | 자동으로 R 코드 서식 지정 |
| Formula | 확장된 모델 수식 |
| fracdiff | 부분적으로 차별화된 ARIMA(ARFIMA(p,d,q) 모델이라고도 함) |
| gam | 일반화된 가산 모델 |
| gamlr | 감마 Lasso 회귀 |
| gbm | 일반화되고 향상된 회귀 모델 |
| gclus | 클러스터링 그래픽 |
| gdata | 데이터 조작을 위한 다양한 R 프로그래밍 도구 |
| gee | 일반화된 추정 수식 해 찾기 |
| genetics | 집단 유전학 |
| geoR | 지구통계학 데이터 분석 |
| geoRglm | geoRglm - 일반화된 선형 공간 모델의 패키지 |
| geosphere | 구면 삼각법 |
| ggmcmc | Bayesian 추론에서 마르코프 체인 몬테 칼로 시뮬레이션 분석을 위한 그래픽 도구 |
| ggplot2 | 그래픽 문법 구현 |
| glmmBUGS | WinBUGS, BRugs 또는 OpenBUGS를 사용하는 일반화 선형 혼합 모델 및 공간 모델 |
| glmnet | Lasso와 Elastic-Net 정칙 일반화 선형 모델 |
| gmodels | 모델 적합을 위한 다양한 R 프로그래밍 도구 |
| gmp | 여러 정밀 산술 |
| gnm | 일반화된 비선형 모델 |
| googlePublicData | Google의 공용 데이터 탐색기 DSPL 메타 데이터 파일을 빌드하는 R 라이브러리 |
| googleVis | R 및 Google 차트 간의 인터페이스 |
| GPArotation | GPA 요소 회전 |
| gplots | 데이터를 그래프에 표시하기 위한 다양한 R 프로그래밍 도구 |
| graphics | R 그래픽 패키지 |
| grDevices | R 그래픽 장치 및 색상과 글꼴 지원 |
| gregmisc | Greg의 기타 함수 |
| grid | 그리드 그래픽 패키지 |
| gridExtra | 그리드 그래픽의 함수 |
| growcurves | 추가로 여러 멤버 자격 임의 효과를 포함하는 Bayesian 준모수 및 비모수 성장 곡선 모델 |
| grpreg | 그룹화된 공변량이 있는 회귀 모델의 정규화 경로 |
| gsubfn | 문자열 및 함수 인수에 대한 유틸리티 |
| gtable | 테이블에 grobs 정렬 |
| gtools | 다양한 R 프로그래밍 도구 |
| gWidgets | 도구 키트 독립적 대화식 GUI를 구축하기 위한 gWidgets API |
| gWidgetsRGtk2 | RGtk2용 gWidgets의 도구 키트 구현 |
| haplo.stats | 연결 단계가 모호한 경우 특성과 공변량을 사용하는 단상형 통계 분석 |
| hbsae | 계층적 Bayesian 소지역 추정 |
| hdrcde | 밀도가 가장 높은 영역 및 조건부 밀도 추정 |
| heavy | 두꺼운 꼬리 분포를 사용하는 이상값 처리 패키지 |
| hflights | 2011년에 휴스턴을 출발한 항공편 |
| HH | 통계 분석 및 데이터 표시: 하이버거 및 네덜란드 |
| HI | 중첩된 초평면에서 지원하는 분산에서 시뮬레이션 |
| highr | R에 대한 구문 강조 |
| Hmisc | Harrell Miscellaneous |
| htmltools | HTML용 도구 |
| httpuv | HTTP 및 WebSocket 서버 라이브러리 |
| httr | URL 및 HTTP 작업을 위한 도구 |
| IBrokers | Interactive Brokers Trader Workstation에 대한 R API |
| igraph | 네트워크 분석 및 시각화 |
| intervals | 지점 및 간격에 대한 작업용 도구 |
| iplots | iPlots - R을 위한 대화형 그래픽 |
| ipred | 향상된 예측 변수 |
| irr | 평정자간 신뢰도 및 합치도의 다양한 계수 |
| iterators | R의 반복기 구문 |
| JavaGD | Java 그래픽 장치 |
| JGR | JGR - R용 Java GUI |
| kernlab | 커널 기반 기계 학습 랩 |
| KernSmooth | Wand 및 Jones를 위한 커널 평활법 함수(1995) |
| KFKSDS | 칼만 필터, 평활기 및 교란 평활기 |
| kinship2 | Pedigree 함수 |
| kknn | 가중치가 적용 k-최근접 이웃 |
| klaR | 분류 및 시각화 |
| knitr | R에서 동적 보고서 생성에 사용하는 범용 패키지 |
| ks | 커널 평활화 |
| labeling | 축 레이블 지정 |
| Lahman | Sean Lahman 야구 데이터베이스 |
| lars | 최소 각도 회귀, Lasso and Forward Stagewise |
| lattice | 격자 그래픽 |
| latticeExtra | 격자를 기반으로 하는 추가 그래픽 유틸리티 |
| lava | 선형 잠재 변수 모델 |
| lavaan | 잠재 변수 분석 |
| leaps | 회귀 하위 집합 선택 |
| LearnBayes | Bayesian 유추 학습을 위한 함수 |
| limSolve | 선형 역모델 해결 |
| lme4 | Eigen 및 S4를 사용하는 선형 혼합 효과 모델 |
| lmm | 선형 혼합 모델 |
| lmPerm | 선형 모델의 순열 검정 |
| lmtest | 선형 회귀 모델 검정 |
| locfit | 로컬 회귀, 유사도 및 밀도 추정 |
| lpSolve | Lp_solve v에 대한 인터페이스. 선형/정수 프로그램을 해결하기 위한 5.5 |


###M-R로 시작하는 R 모듈

| 패키지 이름 | 패키지 설명 |
| ------------ | ------------------- |
| magic | 마정방 만들기 및 조사 |
| magrittr | magrittr - R용 정방향 파이프 연산자 |
| mapdata | 추가 맵 데이터베이스 |
| mapproj | 맵 투영 |
| maps | 지리 지도 그리기 |
| maptools | 공간 개체를 읽고 처리하기 위한 도구 |
| maptree | 트리 모델 매핑, 정리 및 그래프 구성 |
| markdown | R용 마크다운 렌더링 |
| MASS | Venables 및 Ripley의 MASS를 위한 함수 및 데이터 집합 지원 |
| MasterBayes | Pedigree 재구성 및 분석을 위한 ML 및 MCMC 메서드 |
| Matrix | 스파스 및 덴스 행렬 클래스와 메서드 |
| matrixcalc | 행렬 계산을 위한 함수 컬렉션 |
| MatrixModels | 스파스 및 조밀 매트릭스를 사용하여 모델링 |
| maxent | 텍스트 분류 지원을 통한 부족 메모리 다항 로지스틱 회귀 |
| maxLik | 최대 유사도 추정 |
| mcmc | 마르코프 체인 몬테 칼로 |
| MCMCglmm | MCMC 일반화된 선형 혼합 모델 |
| MCMCpack | Markov 체인 몬테 칼로(MCMC) 패키지 |
| memoise | Memoise 함수 |
| methods | 공식 메서드 및 클래스 |
| mgcv | GCV/AIC/REML 평활화 추정을 사용하는 혼합 GEM 계산 도구 |
| mice | 연쇄 방정식으로 다변량 대체 |
| microbenchmark | 마이크로초 미만의 정확한 타이밍 함수 |
| mime | MIME 형식에 파일 이름 매핑 |
| minpack.lm | MINPACK에 있는 Levenberg-Marquardt 비선형 최소제곱법 알고리즘의 R 인터페이스와 범위 지원 |
| minqa | 이차근사를 통한 미분계수가 없는 최적화 알고리즘 |
| misc3d | 기타 3D 그림 |
| miscF | 기타 함수 |
| miscTools | 기타 도구 및 유틸리티 |
| mixtools | 한정된 혼합 모델을 분석하기 위한 도구 |
| mlbench | 기계 학습 벤치마크 문제 |
| mlogitBMA | 다항 로짓 모델의 Bayesian 모델 평균화 |
| mnormt | 다변량 정규 및 t 분포 |
| MNP | 다항 프로빗 모델에 맞추기 위한 R 패키지 |
| modeltools | 통계 모델용 도구 및 클래스 |
| mombf | 적률 및 역적률 Bayes 요인 |
| monomvn | 단조 결측을 사용하여 다변량 정규 및 Student-t 데이터 추정 |
| monreg | 비모수 단조 회귀 |
| mosaic | 프로젝트 MOSAIC(mosaic-web.org) 통계 및 수학 교육 유틸리티 |
| MSBVAR | 마르코프 전환, Bayesian, 벡터 자기회귀 모델 |
| msm | 연속 시간의 다중 상태 마르코프 및 숨겨진 마르코프 모델 |
| multcomp | 일반 모수 모델의 동시 추론 |
| multicool | cool-lex 순서로 된 다중 집합 순열 |
| munsell | Munsell 색 시스템 |
| mvoutlier | 강력한 메서드를 기반으로 하는 다변량 이상값 검색 |
| mvtnorm | 다변량 정규 및 t 분포 |
| ncvreg | SCAD- 및 MCP-벌점 회귀 모델의 정규화 경로 |
| nlme | 선형 및 비선형 혼합 효과 모델 |
| NLP | 자연어 처리 인프라 |
| nnet | 피드 정방향 신경망 및 다항 로그 선형 모델 |
| numbers | 숫자-이론 함수 |
| numDeriv | 정확한 숫자 파생물 |
| openNLP | Apache OpenNLP 도구 인터페이스 |
| openNLPdata | Apache OpenNLP Jars 및 Basic 영어 언어 모델 |
| OutlierDC | 중도절단 데이터의 분위수 회귀를 사용한 이상값 검색 |
| OutlierDM | 복제된 처리량이 높은 데이터의 이상값 검색 |
| outliers | 이상값 테스트 |
| pacbpred | PAC Bayesian 추정 및 스파스 가산 모델의 추정 |
| 병렬 | R에서 병렬 계산 지원 |
| partitions | 정수의 가산 파티션 |
| party | 재귀 분할을 위한 랩 |
| PAWL | PAWL 알고리즘 구현 |
| pbivnorm | 벡터화된 이변량 정규 CDF |
| pcaPP | 사영추적을 통한 강력한 PCA |
| permute | 제한된 데이터 순열을 생성하는 함수 |
| pls | 부분 최소제곱 및 주성분 회귀 |
| plyr | 데이터 분할, 적용 및 결합 도구 |
| png | PNG 이미지 읽기 및 쓰기 |
| polynom | 일변량 다항 조작을 위한 클래스를 구현하는 함수 컬렉션 |
| PottsUtils | Potts 모델의 유틸리티 함수 |
| predmixcor | 기능 선택 편향 수정이 포함된 Bayesian 혼합 모델을 기반으로 하는 분류 규칙 |
| PresenceAbsence | 현재 상태 부재 모델 평가 |
| prodlim | 제품 한계 추정. 중도절단 이벤트 기록(생존) 분석을 위한 카플란-마이어 및 Aalen-Johansson 메서드 |
| profdpm | 프로필 디리슈레 프로세스 혼합 |
| profileModel | 다양한 모델 클래스의 인터페이스 함수를 프로파일링하는 도구 |
| proto | 프로토타입 개체 기반 프로그래밍 |
| pscl | Political Science Computational Laboratory, Stanford University |
| psych | 심리학, 계량심리학 및 인성 연구 절차 |
| quadprog | 이차 프로그래밍 문제를 해결하는 함수 |
| quantreg | 분위수 회귀 |
| qvcalc | 통계 모델의 요인 효과를 위한 의사 분산 |
| R.matlab | R-MATLAB 연결과 함께 MAT 읽기 및 쓰기 |
| R.methodsS3 | S3 메서드를 정의하기 위한 유틸리티 함수 |
| R.oo | 참조 포함 또는 포함하지 않는 R 개체 지향 프로그래밍 |
| R.utils | 다양한 프로그래밍 유틸리티 |
| R2HTML | R 개체의 HTML 내보내기 |
| R2jags | R에서 jags를 실행하기 위한 패키지 |
| R2OpenBUGS | R에서 OpenBUGS 실행 |
| R2WinBUGS | R / S-PLUS에서 WinBUGS 및 OpenBUGS 실행 |
| ramps | RAMPS를 사용하는 Bayesian 지구통계학 모델링 |
| RandomFields | 임의 필드 분석 및 시뮬레이션 |
| randomForest | 분류 및 회귀에 대한 Breiman and Cutler의 임의 포리스트 |
| RArcInfo | Arc/Info V7.x 이진 포함에서 데이터를 가져오는 함수 |
| raster | 래스터: 지리적 데이터 분석 및 모델링 |
| rbugs | Fusing R, OpenBugs 및 Beyond |
| RColorBrewer | ColorBrewer 색상표 |
| Rcpp | 원활한 R 및 C++ 통합 |
| RcppArmadillo | Armadillo 템플릿 기반 선형 대수 라이브러리를 위한 Rcpp 통합 |
| rcppbugs | cppbugs에 대한 R 바인딩 |
| RcppEigen | 고유 템플릿 기반 선형 대수 라이브러리의 Rcpp 통합 |
| RcppExamples | R 및 C++에 대한 인터페이스로 Rcpp를 사용하는 예제 |
| RCurl | R에 대한 일반 네트워크(HTTP/FTP/...) 클라이언트 인터페이스 |
| relimp | 회귀 모델에 대한 효과의 상대적 기여도 |
| reshape | 유연하게 데이터 모양 변경 |
| reshape2 | 유연하게 데이터 모양 변경: 모양을 변경한 패키지 다시 부팅 |
| rgdal | 지리 공간 데이터 추상화 라이브러리에 대한 바인딩 |
| rgeos | GEOS(Geometry Engine - Open Source) 인터페이스 |
| rgl | 3D 시각화 장치 시스템(OpenGL) |
| RGraphics | R 그래픽 서적, 제2판의 데이터 및 함수 |
| RGtk2 | Gtk 2.8.0 이상을 위한 R 바인딩 |
| RJaCGH | CGH 배열 분석을 위한 가역 점프 MCMC |
| rjags | MCMC를 사용하는 Bayesian 그래픽 모델 |
| rJava | 하위 수준 R-Java 인터페이스 |
| RJSONIO | JSON(JavaScript Object Notation)에 맞게 R 개체 직렬화 |
| robCompositions | 구성 데이터에 대한 강력한 추정 |
| robustbase | 강력한 기본 통계 |
| RODBC | ODBC 데이터베이스 액세스 |
| rootSolve | 비선형 루트 찾기, 일반 차등 수식의 평형 및 안정적인 상태 분석 |
| roxygen | R에서 익숙하게 프로그래밍 |
| roxygen2 | R에 대한 소스 내 설명서 |
| rpart | 재귀 분할 및 회귀 트리 |
| rrcov | 높은 붕괴점을 통해 확장 가능한 강력한 추정량 |
| rscproxy | statconn: R에 이식 가능한 C-스타일 인터페이스 제공(StatConnector) |
| RSGHB | 계층 구조 Bayesian 추정을 위한 함수: 유연한 방법 |
| RSNNS | SNNS(Stuttgart Neural Network Simulator)를 사용하는 R의 신경망 |
| RTextTools | 감독된 학습을 통해 텍스트 자동 분류 |
| RUnit | R 단위 테스트 프레임워크 |
| runjags | JAGS의 MCMC 모델을 위한 인터페이스 유틸리티, 병렬 컴퓨팅 메서드 및 추가 배포 |
| Runuran | UNU.RAN 임의 변량 생성기를 위한 R 인터페이스 |
| rworldmap | 글로벌 데이터, 벡터 및 래스터 매핑 |
| rworldxtra | 높은 해상도에서 국가 경계 |


###S-Z로 시작 하는 R 모듈

| 패키지 이름 | 패키지 설명 |
| ------------ | ------------------- |
| SampleSizeMeans | 정규 평균값을 위한 샘플 크기 계산 |
| SampleSizeProportions | 두 이항 비율 간의 차이 추정할 때 샘플 크기 요구 사항 계산 |
| sandwich | 강력한 공분산 행렬 추정량 |
| sbgcop | 준모수적 Bayesian 가우스 연결 동사 추정 및 대체 |
| scales | 그래픽의 배율 함수 |
| scapeMCMC | MCMC 진단 그림 |
| scatterplot3d | 3D 산점도 |
| sciplot | 계승 디자인의 과학 그래프 작성 함수 |
| segmented | 중단점/변경점 추정을 사용하는 회귀 모델의 조각화된 관계 |
| sem | 구조방정식 모델 |
| seriation | 순차 배열을 위한 인프라 |
| setRNG | (일반) 난수 생성기 및 초기값 설정 |
| sgeostat | S+의 지구통계학 모델링을 위한 개체 지향 프레임워크 |
| shapefiles | ESRI 셰이프 파일 읽기 및 쓰기 |
| shiny | R용 웹 응용 프로그램 프레임워크 |
| SimpleTable | 측정되지 않은 혼선 존재 에서 2 x 2 및 2 x 2 x K 테이블에서 인과 효과를 위한 Bayesian 추론 및 민감도 분석 |
| slam | 스파스 경량 배열 및 행렬 |
| smoothSurv | 평활된 오차 분포를 사용하는 생존 회귀 |
| sna | 소셜 네트워크 분석을 위한 도구 |
| snow | 워크스테이션의 간단한 네트워크(Simple Network of Workstations) |
| SnowballC | C libstemmer UTF-8 라이브러리를 기반으로 하는 스노우볼 형태소 분석기 |
| snowFT | 워크스테이션의 내결함성이 있는 간단한 네트워크 |
| sp | 공간 데이터의 클래스 및 메서드 |
| spacetime | 공간 시간 데이터의 클래스 및 메서드 |
| SparseM | 스파스 선형 대수 |
| Spatial | 크리깅 및 점 패턴 분석을 위한 함수 |
| spBayes | 일변량 및 다변량 공간 시간 모델링 |
| spdep | 공간 종속성: 가중치 구성표, 통계 및 모델 |
| spikeslab | 스파이크 및 슬래브 회귀를 사용한 추정 및 변수 선택 |
| splancs | 공간 및 시공간 점 패턴 분석 |
| splines | 회귀 스플라인 함수 및 클래스 |
| spTimer | R을 사용하는 공간 시간 Bayesian 모델링 |
| stats | R 통계 패키지 |
| stats4 | S4 클래스를 사용하는 통계 함수 |
| stochvol | SV(Stochastic Volatility) 모델을 위한 효율적인 Bayesian 추론 |
| stringr | 문자열에 대한 작업을 용이화 |
| strucchange | 구조 변경 테스트, 모니터링 및 날짜 지정 |
| stsm | 구조적 시계열 모델 |
| stsm.class | 구조적 시계열 모델의 클래스 및 메서드 |
| SuppDists | 보조 분포 |
| survival | 생존 분석 |
| svmpath | svmpath: SVM 경로 알고리즘 |
| tau | 텍스트 분석 유틸리티 |
| tcltk | Tcl/Tk 인터페이스 |
| tcltk2 | Tcl/Tk 추가 |
| TeachingDemos | 교육 및 학습을 위한 데모 |
| tensorA | 명명된 인덱스로 고급 텐서 산술 |
| testthat | Testthat 코드. 테스트를 재미있게 만드는 도구 |
| textcat | N-Gram 기반 텍스트 분류 |
| textir | 텍스트 분석을 위한 역회귀 |
| tfplot | 시간 프레임 사용자 유틸리티 |
| tframe | 시간 프레임 코딩 커널 |
| tgp | Bayesian 트리 가우스 프로세스 모델 |
| TH.data | TH의 데이터 보관 |
| timeDate | Rmetrics - 시간 및 달력 개체 |
| tm | 텍스트 마이닝 패키지 |
| 도구 | 패키지 개발을 위한 도구 |
| translations | R 번역 패키지 |
| tree | 분류 및 회귀 트리 |
| tseries | 시계열 분석 및 재무 계산 |
| tsfa | 시계열 요소 분석 |
| tsoutliers | 시계열에서 이상값을 자동으로 검색 |
| TSP | 출장 중인 영업 사원 문제(TSP) |
| UsingR | 소개 통계를 위해 R을 사용하는 텍스트의 데이터 집합 |
| utils | R 유틸리티 패키지 |
| varSelectIP | 목적 Bayes 모델 선택 |
| vcd | 범주 데이터 시각화 |
| vegan | 커뮤니티 생태 패키지 |
| VGAM | 벡터 일반화 선형 및 가산 모델 |
| VIF | VIF 회귀: 대형 데이터를 위한 빠른 회귀 알고리즘 |
| whisker | R용 {{mustache}}, 로직이 없는 템플릿 |
| wordcloud | Word 클라우드 |
| XLConnect | R용 Excel 커넥터 |
| XML | R 및 S-Plus에서 XML의 구문을 분석하고 생성하기 위한 도구 |
| xtable | LaTeX 또는 HTML로 테이블 내보내기 |
| xts | 확장 가능한 시계열 |
| YAML | R 데이터를 YAML로 변환하거나 반대로 변환하는 메서드 |
| zic | 영(0) 확장 개수 모델을 위한 Bayesian 추론 |
| zipfR | 단어 빈도 분포를 위한 통계 모델 |
| zoo | 정칙 및 비정칙 시계열(Z의 순서 지정된 관찰)을 위한 S3 인프라 |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
 

<!---HONumber=July15_HO2-->