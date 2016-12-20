# 개요

## [소개](storage-introduction.md)

# 시작

## [빠른 시작 가이드](storage-getting-started-guide.md)
## [저장소 계정을 만드는](storage-create-storage-account.md)

## Blob 저장소
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.JS](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Queue Storage
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.JS](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## 테이블 저장소
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.JS](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## File Storage
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

# 방법
## [저장소 계정을 만드는](storage-create-storage-account.md)
## Blob 사용
### [서비스 개요](https://msdn.microsoft.com/library/dd179376.aspx)
### [핫 및 쿨 계층](storage-blob-storage-tiers.md)
### [사용자 지정 도메인](storage-custom-domain-name.md)
### [Blob에 대한 익명 액세스](storage-manage-access-to-resources.md)
### [샘플](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## 큐 사용
### [개념](https://msdn.microsoft.com/library/dd179353.aspx)
### [샘플](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## 테이블 사용
### [개요](https://msdn.microsoft.com/library/dd179463.aspx)
### [테이블 디자인 가이드](storage-table-design-guide.md)
### [샘플](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## 파일 사용
### [개요](https://msdn.microsoft.com/en-us/library/dn166972.aspx)
### [Azure 파일 문제 해결](storage-troubleshoot-file-connection-problems.md)
### [샘플](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## 가상 컴퓨터 디스크 사용
### Premium Storage
#### [VM 작업을 위한 고성능 저장소](storage-premium-storage.md)
#### [프리미엄 저장소로 마이그레이션](storage-migration-to-premium-storage.md)
#### [고성능을 위한 디자인](storage-premium-storage-performance.md)
### 표준 저장소
#### [증분 스냅숏을 사용하여 VM 디스크 백업](storage-incremental-snapshots.md)
## 계획 및 디자인
### [복제](storage-redundancy.md)
### [확장성 및 성능 대상](storage-scalability-targets.md)
### [성능 및 확장성 검사 목록](storage-performance-checklist.md)
### [동시성](storage-concurrency.md)
## 개발
### [샘플](storage-samples.md)
### [연결 문자열 구성](storage-configure-connection-string.md)
### [저장소 에뮬레이터 사용](storage-use-emulator.md)
### [속성 및 메타데이터 설정 및 검색](storage-properties-metadata.md)
## 관리
### [PowerShell](storage-powershell-guide-full.md)
### [Azure CLI](storage-azure-cli.md)
### [Azure Automation](automation-manage-storage.md)
## 보안
### [보안 지침](storage-security-guide.md)
### [미사용 데이터 암호화](storage-service-encryption.md)
### [공유 키 인증](https://msdn.microsoft.com/library/dd179428.aspx)
### [공유 액세스 서명(SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [자습서: Azure Key Vault를 사용하여 Blob 암호화 및 해독](storage-encrypt-decrypt-blobs-key-vault.md)
### 클라이언트 쪽 암호화
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## 모니터링 및 문제 해결
### 메트릭 및 로깅
#### [저장소 분석](storage-analytics.md)
#### [메트릭 사용 및 보기](storage-enable-and-view-metrics.md)
#### [모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)
#### [문제 해결 자습서](storage-e2e-troubleshooting.md)
### 디스크 삭제 오류 문제 해결
#### [Resource Manager 배포](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [클래식 배포](storage-cannot-delete-storage-account-container-vhd.md)
### [파일 저장소 문제 해결](storage-troubleshoot-file-connection-problems.md)
### [재해 복구 지침](storage-disaster-recovery-guidance.md)
## 데이터 전송
### [저장소로 또는 반대로 데이터 이동](storage-moving-data.md)
### [AzCopy 명령줄 유틸리티](storage-use-azcopy.md)
### [Blob Storage에 대한 가져오기/내보내기 서비스](storage-import-export-service.md)

# 참조
## [PowerShell](/powershell/storage)
## [Azure CLI](/cli/azure/storage)
## .NET
### [리소스 관리자](/dotnet/api/microsoft.azure.management.storage)
### [데이터 이동](https://msdn.microsoft.com/en-us/library/azure/mt684990.aspx)
### [Blob, 큐, 테이블 및 파일](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](/java/api/)
## [Node.JS](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](https://github.com/Azure/azure-storage-ios)
## [Android](http://azure.github.io/azure-storage-android)
## [REST]
### [Blob, 큐, 테이블 및 파일](/rest/api/storageservices/importexport/storage-import-export-service-rest-api-reference)
### [리소스 공급자](/rest/api/storagerp)
### [가져오기/내보내기](/rest/api/storageservices/importexport/storage-import-export-service-rest-api-reference)

# 관련 항목
## 클래식 포털
### [저장소 계정 만들기](storage-create-storage-account-classic-portal.md)
### [메트릭 사용 및 보기](storage-enable-and-view-metrics-classic-portal.md)
### [모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [문제 해결 자습서](storage-e2e-troubleshooting-classic-portal.md)

# 리소스
## [가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Azure Storage 클라이언트 도구](storage-explorers.md)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=storage)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure Storage 탐색기
### [저장소 탐색기(미리 보기)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [저장소 탐색기(미리 보기)를 사용하여 Blob 관리](../vs-azure-tools-storage-explorer-blobs.md)

## Nuget 패키지
### [.NET용 Azure 저장소 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Azure Storage 데이터 이동 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## 소스 코드
### .NET
#### [Blob, 큐, 테이블 및 파일](https://github.com/Azure/azure-storage-net)
#### [데이터 이동](https://github.com/Azure/azure-storage-net-data-movement)
#### [리소스 공급자](https://github.com/Azure/azure-sdk-for-net)
### [Node.JS](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)


<!--HONumber=Dec16_HO2-->


