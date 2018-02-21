
# [Batch 설명서](index.yml)

# 개요
## [Azure Batch란?](batch-technical-overview.md)

# 빠른 시작
## [Batch 작업 실행 - CLI](quick-create-cli.md)
## [Batch 작업 실행 - 포털](quick-create-portal.md)
## [Batch 작업 실행 - .NET](quick-run-dotnet.md)
## [Batch 작업 실행 - Python](quick-run-python.md)


# 자습서

## [병렬 파일 처리 - .NET](tutorial-parallel-dotnet.md)
## [병렬 파일 처리 - Python](tutorial-parallel-python.md)
## [Arnold를 사용하여 렌더링하는 장면](tutorial-rendering-cli.md)
## [병렬 R 시뮬레이션](tutorial-r-doazureparallel.md)

# 샘플
## 코드 샘플
### [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=batch)
### [Batch 샘플 리포지토리](https://github.com/Azure/azure-batch-samples)
## [Azure CLI](cli-samples.md)

# 개념
## [개발자 기능](batch-api-basics.md)
## [API 및 도구](batch-apis-tools.md)
## [할당량 및 한도](batch-quota-limit.md)

# 방법 가이드
## Batch 계정 관리
### [포털을 사용하여 Batch 계정 관리](batch-account-create-portal.md)
### [Batch 관리 .NET을 사용하여 Batch 계정 관리](batch-management-dotnet.md)

## Azure AD를 사용하여 인증
### [Batch 서비스 포함 Azure AD](batch-aad-auth.md)
### [Batch 관리 포함 Azure AD](batch-aad-auth-management.md)

## [응용 프로그램 패키지 만들기](batch-application-packages.md)

## 풀 만들기 및 관리
### [자동 크기 조정 컴퓨터 노드](batch-automatic-scaling.md)
### [계산 집약적 VM](batch-pool-compute-intensive-sizes.md)
### [계산 노드에 대한 액세스 구성](pool-endpoint-configuration.md)
### [사용자 지정 이미지로 풀 만들기](batch-custom-images.md)
### [가상 네트워크에서 풀 만들기](batch-virtual-network.md)
### [Linux 계산 노드](batch-linux-nodes.md)
### [우선 순위가 낮은 VM](batch-low-pri-vms.md)

## 작업 및 태스크 관리
### [작업 준비 및 완료 작업](batch-job-prep-release.md)
### [동시 노드 작업](batch-parallel-node-tasks.md)
### [상태별 작업 계수](batch-get-task-counts.md)
### [작업 종속성](batch-task-dependencies.md)
### [작업 실행을 위한 사용자 계정](batch-user-accounts.md)
### [코드 없이 Batch 작업 실행](batch-cli-templates.md)


## [작업 유지 및 태스크 출력](batch-task-output.md)
### [Batch API를 사용하여 출력 유지](batch-task-output-files.md)
### [파일 규칙 라이브러리를 사용하여 출력 유지](batch-task-output-file-conventions.md)

## 워크로드 실행
### [MPI 작업](batch-mpi.md)
### [컨테이너 워크로드](batch-docker-container-workloads.md)
### [렌더링](batch-rendering-service.md)
### [Batch 및 데이터 팩터리를 사용하여 데이터 처리](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)

## 리소스 모니터링
### [진단 로깅](batch-diagnostics.md)
### [효율적인 목록 쿼리](batch-efficient-list-queries.md)

## 개발자 도구 사용
### [Batch .NET 사용](batch-dotnet-get-started.md)
### [Batch Python 사용](batch-python-tutorial.md)
### [Batch Node.js 사용](batch-nodejs-get-started.md)
### [PowerShell Cmdlet 사용](batch-powershell-cmdlets-get-started.md)
### [Azure CLI 사용](batch-cli-get-started.md)

# 참고 자료

## [Azure CLI](/cli/azure/batch)
## [Azure PowerShell](/powershell/module/azurerm.batch)
## [.NET](/dotnet/api/microsoft.azure.batch)
## [Java](/java/api/com.microsoft.azure.batch)
## [Node.js](/nodejs/api/overview/azure/batch)
## [Python](/python/api/overview/azure/batch)
## REST (영문)
### [Batch 서비스](/rest/api/batchservice)
### [Batch 관리](/rest/api/batchmanagement/)
## [Batch 분석](batch-analytics.md)
### [풀 만들기 이벤트](batch-pool-create-event.md)
### [풀 삭제 시작 이벤트](batch-pool-delete-start-event.md)
### [풀 삭제 완료 이벤트](batch-pool-delete-complete-event.md)
### [풀 크기 조정 시작 이벤트](batch-pool-resize-start-event.md)
### [풀 크기 조정 완료 이벤트](batch-pool-resize-complete-event.md)
### [작업 시작 이벤트](batch-task-start-event.md)
### [작업 완료 이벤트](batch-task-complete-event.md)
### [작업 실패 이벤트](batch-task-fail-event.md)
## [Batch 계산 노드 환경 변수](batch-compute-node-environment-variables.md)
### [Resource Manager 템플릿](/templates/microsoft.batch/batchaccounts)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)
## [Batch 포럼](https://social.msdn.microsoft.com/Forums/en-us/home?forum=azurebatch)
## [Batch Shipyard](https://github.com/Azure/batch-shipyard)
## [블로그](https://blogs.technet.microsoft.com/windowshpc/)
## [Azure의 HPC 솔루션](../virtual-machines/linux/high-performance-computing.md?toc=%2fazure%2fbatch%2ftoc.json)
## [가격 책정](https://azure.microsoft.com/pricing/details/batch/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=batch&updatetype=&platform=)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-batch)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=batch)


