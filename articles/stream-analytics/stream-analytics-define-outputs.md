<properties
	pageTitle="스트림 분석 출력: 저장소에 대한 옵션, 분석 | Microsoft Azure"
	description="분석 결과에 대한 Power BI를 포함하는 스트림 분석 데이터 출력 옵션을 대상으로 하는 방법을 알아봅니다."
	keywords="데이터 변환, 분석 결과, 데이터 저장소 옵션"
	services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
	documentationCenter="" 
	authors="jeffstokes72"
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/29/2016"
	ms.author="jeffstok"/>

# 스트림 분석 출력: 저장소에 대한 옵션, 분석

스트림 분석 작업을 만들 때는 결과 데이터를 어떤 방식으로 사용할지를 고려해야 합니다. 스트림 분석 작업 결과를 어떻게 보고 어디에 저장하시겠습니까?

다양한 응용 프로그램 패턴을 사용할 수 있도록 Azure 스트림 분석은 출력을 저장하고 분석 결과를 표시하는 다양한 방법을 제공합니다. 따라서 간편하게 작업 출력을 확인하고, 데이터 웨어 하우징 및 기타 용도로 작업 출력을 유연하게 사용하고 저장할 수 있습니다. 작업에서 구성된 모든 출력은 작업 시작 및 이벤트 전송이 시작되기 전에 존재해야 합니다. 예를 들어, 출력으로 Blob 저장소를 사용한 경우, 작업은 저장소 계정을 자동으로 만들지 않습니다. ASA 작업이 시작되기 전에 사용자가 만들어야 합니다.

## Azure Data Lake Store

스트림 분석은 [Azure Data Lake 저장소](https://azure.microsoft.com/services/data-lake-store/)를 지원합니다. 이 저장소를 사용하면 작동 및 예비 분석에 대해 모든 크기, 형식 및 수집 속도의 데이터를 저장할 수 있습니다. 이때 Azure 클래식 포털에서만 Data Lake 저장소 출력을 만들고 구성할 수 있습니다. 또한 스트림 분석에는 Data Lake 저장소에 액세스할 수 있는 권한이 필요합니다. 권한 부여 및 Data Lake 저장소 미리 보기에 등록하는 방법에 대한 세부 정보는 (필요한 경우) [데이터 레이크 출력 문서](stream-analytics-data-lake-output.md)에서 설명합니다.

### Azure Data Lake 저장소 권한 부여

Azure 관리 포털에서 출력으로 Data Lake 저장소를 선택하는 경우 기존 Data Lake 저장소에 대한 연결을 허가할지 묻는 메시지가 표시됩니다.

![Data Lake 저장소 권한 부여](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

아래와 같이 Data Lake 저장소 출력에 대한 속성을 완료합니다.

![Data Lake 저장소 권한 부여](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

다음 테이블에 Data Lake 저장소 출력을 만드는 데 필요한 속성 이름 및 해당 설명을 나열합니다.

<table>
<tbody>
<tr>
<td><B>속성 이름</B></td>
<td><B>설명</B></td>
</tr>
<tr>
<td>출력 별칭</td>
<td>쿼리 출력을 이 Data Lake 저장소로 직접 보내기 위해 쿼리에서 사용되는 식별 이름입니다.</td>
</tr>
<tr>
<td>계정 이름</td>
<td>출력을 보내는 Data Lake 저장소 계정의 이름 포털에 로그인한 사용자가 액세스할 수 있는 Data Lake 저장소 계정이 드롭다운 목록으로 나타납니다.</td>
</tr>
<tr>
<td>경로 접두사 패턴[<I>선택 사항</I>]</td>
<td>지정된 Data Lake 저장소 계정 내에서 파일을 작성하는 데 사용되는 파일 경로입니다. <BR>{date}, {time}<BR>예제 1: folder1/logs/{date}/{time}<BR>예제 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>날짜 형식[<I>선택 사항</I>]</td>
<td>접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD</td>
</tr>
<tr>
<td>시간 형식[<I>선택 사항</I>]</td>
<td>접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다.</td>
</tr>
<tr>
<td>이벤트 직렬화 형식</td>
<td>출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다.</td>
</tr>
<tr>
<td>인코딩</td>
<td>CSV 또는 JSON 형식인 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.</td>
</tr>
<tr>
<td>구분 기호</td>
<td>CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.</td>
</tr>
<tr>
<td>형식</td>
<td>JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다.</td>
</tr>
</tbody>
</table>

### Data Lake 저장소 권한 부여 갱신

작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우에 Data Lake 저장소 계정을 다시 인증해야 합니다.

![Data Lake 저장소 권한 부여](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)


## SQL 데이터베이스

기본적으로 관계형 데이터 또는 관계형 데이터베이스에 호스트된 내용에 종속된 응용 프로그램에 대한 출력으로 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)를 사용할 수 있습니다. Azure SQL 데이터베이스의 기존 테이블에 스트림 분석 작업을 기록합니다. 테이블 스키마가 작업에서 출력되는 필드 및 해당 유형과 정확히 일치해야 합니다. [Azure SQL 데이터 웨어하우스](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)를 SQL 데이터베이스 출력 옵션을 통한 출력으로 지정할 수도 있습니다(미리 보기 기능). 다음 테이블은 SQL 데이터베이스 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
|---------------|-------------|
| 출력 별칭 | 쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름 |
| 서버 이름 | SQL 데이터베이스 서버 이름 |
| 사용자 이름 | 데이터베이스에 쓸 수 있는 액세스 권한이 있는 사용자 이름 |
| 암호 | 데이터베이스에 연결하는 암호 |
| 테이블 | 출력을 쓸 테이블 이름입니다. 테이블 이름은 대/소문자를 구분하며 이 테이블의 스키마는 작업 출력에서 생성되는 필드 및 형식의 수와 정확하게 일치해야 합니다. |

> [AZURE.NOTE] 현재, 스트림 분석의 작업 출력에 대해 Azure SQL 데이터베이스 제품을 사용할 수 있습니다. 그러나 데이터베이스가 연결된 SQL Server를 실행하는 Azure 가상 컴퓨터는 지원되지 않습니다. 후속 릴리스에서는 변경될 수 있습니다.

## Blob 저장소

클라우드에서 대량의 구조화되지 않은 데이터를 저장하는 경우 Blob 저장소는 비용 효율적이고 확장성 있는 솔루션을 제공합니다. Azure Blob 저장소 및 사용법에 대한 소개 내용은 [Blob 사용 방법](../storage/storage-dotnet-how-to-use-blobs.md) 설명서를 참조하세요.

다음 테이블은 Blob 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

<table>
<tbody>
<tr>
<td>속성 이름</td>
<td>설명</td>
</tr>
<tr>
<td>출력 별칭</td>
<td>쿼리 출력을 이 Blob 저장소로 보내기 위해 쿼리에서 사용되는 이름입니다.</td>
</tr>
<tr>
<td>저장소 계정</td>
<td>출력을 보내는 저장소 계정의 이름</td>
</tr>
<tr>
<td>저장소 계정 키</td>
<td>저장소 계정과 연결된 비밀 키입니다.</td>
</tr>
<tr>
<td>저장소 컨테이너</td>
<td>컨테이너는 Microsoft Azure Blob 서비스에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.</td>
</tr>
<tr>
<td>경로 접두사 패턴 [선택 사항]</td>
<td>지정된 컨테이너 내에서 Blob를 작성하는 데 사용되는 파일 경로입니다.<BR>경로 내에서 Blob가 작성된 빈도를 지정하기 위해 2개의 변수 인스턴스 중 하나 이상을 사용하도록 선택할 수도 있습니다.<BR>{date}, {time}<BR>예제 1: cluster1/logs/{date}/{time}<BR>예제 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>날짜 형식[선택 사항]</td>
<td>접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD</td>
</tr>
<tr>
<td>시간 형식[선택 사항]</td>
<td>접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다.</td>
</tr>
<tr>
<td>이벤트 직렬화 형식</td>
<td>출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다.</td>
</tr>
<tr>
<td>인코딩</td>
<td>CSV 또는 JSON 형식인 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.</td>
</tr>
<tr>
<td>구분 기호</td>
<td>CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.</td>
</tr>
<tr>
<td>형식</td>
<td>JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다.</td>
</tr>
</tbody>
</table>

## 이벤트 허브

[이벤트 허브](https://azure.microsoft.com/services/event-hubs/)는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수 백만의 이벤트를 수집할 수 있습니다. 출력으로 이벤트 허브를 사용하는 한 가지 예는 스트림 분석 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다.

이벤트 허브 데이터 스트림을 출력으로 구성하는 데 필요한 몇 개의 매개 변수가 있습니다.

| 속성 이름 | 설명 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 출력 별칭 | 쿼리 출력을 이 이벤트 허브로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 서비스 버스 네임스페이스 | 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 만들었습니다. |
| 이벤트 허브 | 이벤트 허브 출력의 이름 |
| 이벤트 허브 정책 이름 | 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 서비스 버스 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키 |
| 파티션 키 열 [선택 사항] | 이 열에는 이벤트 허브 출력에 대한 파티션 키가 포함됩니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다. |
| 인코딩 | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 형식에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. |

## Power BI

[Power BI](https://powerbi.microsoft.com/)를 스트림 분석 작업의 출력으로 사용하여 분석 결과에 대한 풍부한 시각화 환경을 제공할 수 있습니다. 작업 대시보드, 보고서 생성 및 메트릭 제어 보고에 이 기능을 이용할 수 있습니다.

### Power BI 계정 권한 부여

1.	Azure 관리 포털에서 Power BI를 출력으로 선택하는 경우 메시지가 표시되어 기존 Power BI 사용자 권한을 부여하거나 새 Power BI 계정을 만듭니다.

    ![Power BI 사용자 권한 부여](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	아직 없다면 새 계정을 만들고 지금 권한 부여를 클릭합니다. 다음과 같은 화면이 표시됩니다.

    ![Azure 계정 Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	이 단계에서 Power BI 출력에 대한 권한을 부여할 회사 또는 학교 계정을 제공합니다. 아직 Power BI에 등록하지 않은 경우 지금 등록을 선택합니다. Power BI에 사용하는 회사 또는 학교 계정은 로그인하고 있는 Azure 구독 계정과 다를 수 있습니다.

### Power BI 출력 속성 구성

Power BI 계정의 인증을 설정하면 사용자가 Power BI 출력에 대한 속성을 구성할 수 있습니다. 다음 테이블은 속성 이름 및 해당 설명의 목록으로 Power BI 출력을 구성합니다.

| 속성 이름 | 설명 |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 출력 별칭 | 쿼리 출력을 이 PowerBI 출력으로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 그룹 작업 영역 | 다른 Power BI 사용자와 데이터를 공유할 수 있게 하려면 Power BI 계정 내에서 그룹을 선택하거나 그룹에 작성하지 않으려면 "내 작업 영역"을 선택할 수 있습니다. 기존 그룹을 업데이트하려면 Power BI 인증을 갱신해야 합니다. | 
| 데이터 집합 이름 | Power BI 출력에 사용할 데이터 집합 이름을 제공합니다. |
| 테이블 이름 | Power BI 출력의 데이터 집합 아래에 테이블 이름을 제공합니다. 현재, 스트림 분석 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다. |

Power BI 출력 및 대시보드 구성에 대한 연습은 [Azure스트림 분석 및 Power BI](stream-analytics-power-bi-dashboard.md) 문서를 참조하세요.

> [AZURE.NOTE] Power BI 대시보드에 명시적으로 데이터 집합 및 테이블을 만들지 마세요. 작업을 시작하고 작업이 Power BI에 출력을 펌프하기 시작하는 경우 데이터 집합 및 테이블은 자동으로 채워집니다. 작업 쿼리가 결과 생성하지 않으면 데이터 집합 및 테이블은 생성되지 않은 것입니다. 또한 이 스트림 분석 작업에서 제공한 이름과 동일한 이름의 데이터 집합과 테이블이 Power BI에 이미 있는 경우에는 기존 데이터를 덮어씁니다.

### Power BI 권한 부여 갱신

작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우에 Power BI 계정을 다시 인증해야 합니다. MFA(Multi-Factor Authentication)가 AAD(Azure Active Directory) 테넌트에 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 이 문제의 증상은 작업 출력이 없으며 작업 로그에 "사용자 인증 오류"가 표시됩니다.

  ![Power BI 새로 고침 토큰 오류](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다. "권한 부여 갱신" 링크를 클릭하고 마지막 중지 시간부터 작업을 다시 시작하여 데이터 손실을 방지합니다.

  ![Power BI 갱신 권한 부여](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## 테이블 저장소

[Azure 테이블 저장소](../storage/storage-introduction.md)는 가용성이 높고 확장성이 큰 저장소를 제공하므로, 응용 프로그램이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. 테이블 저장소는 스키마에 대한 제약 조건이 적은 구조화된 데이터에 활용할 수 있는 Microsoft의 NoSQL 키/특성 저장소입니다. Azure 테이블 저장소는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다.

다음 테이블은 테이블 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 출력 별칭 | 쿼리 출력을 이 테이블 저장소로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 저장소 계정 | 출력을 보내는 저장소 계정의 이름 |
| 저장소 계정 키 | 저장소 계정과 연결된 선택키입니다. |
| 테이블 이름 | 테이블의 이름입니다. 테이블이 존재하지 않는 경우 생성됩니다. |
| Partition Key | 파티션 키를 포함하는 출력 열의 이름입니다. 파티션 키는 엔터티 기본 키의 첫 번째 부분을 형성하는 지정된 테이블 내에서 분할에 고유한 식별자입니다. 크기가 최대 1KB인 문자열 값입니다. |
| Row Key | 행 키를 포함하는 출력 열의 이름입니다. 행 키는 주어진 파티션 내 엔터티의 고유 식별자입니다. 엔터티의 기본 키에서 두 번째 부분을 형성합니다. 행 키는 크기가 최대 1KB인 문자열 값입니다. |
| 배치 크기 | 배치 작업에 대한 레코드 수입니다. 일반적으로 대부분의 작업은 기본값으로 충분합니다. 이 설정을 수정하는 방법에 대한 자세한 내용은 [테이블 배치 작업 사양](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)을 참조하세요. |

## 서비스 버스 큐

[서비스 버스 큐](https://msdn.microsoft.com/library/azure/hh367516.aspx)는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다. 일반적으로 메시지가 큐에 추가된 임시 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다.

다음 테이블은 큐 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 출력 별칭 | 쿼리 출력을 이 서비스 버스 큐로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 서비스 버스 네임스페이스 | 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. |
| 큐 이름 | 서비스 버스 큐 이름 |
| 큐 정책 이름 | 또한 큐를 만들 때 큐 구성 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 큐 정책 키 | 서비스 버스 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키 |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다. |
| 인코딩 | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 형식에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다. |

## 서비스 버스 토픽

서비스 버스 큐는 보낸 사람에서 받는 사람으로의 일대일 통신 방법을 제공하는 반면, [서비스 버스 항목](https://msdn.microsoft.com/library/azure/hh367516.aspx)은 일대다 형태의 통신을 제공합니다.

다음 테이블은 테이블 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

| 속성 이름 | 설명 |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 출력 별칭 | 쿼리 출력을 이 서비스 버스 항목으로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 서비스 버스 네임스페이스 | 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 만들었습니다. |
| 토픽 이름 | 토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 다양한 장치 및 서비스에서 이벤트 스트림을 수집하도록 설계됩니다. 토픽을 만들 때 특정 이름도 지정됩니다. 구독을 만들지 않으면 토픽에 전송된 메시지를 사용할 수 없으므로 토픽 아래에 구독이 하나 이상 있는지 확인합니다. |
| 토픽 정책 이름 | 또한 토픽을 만들 때 토픽 구성 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 토픽 정책 키 | 서비스 버스 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 선택키 |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다. |
| 인코딩 | CSV 또는 JSON 형식인 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |

## DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)는 완벽하게 관리되는 NoSQL 문서 데이터베이스 서비스로, 스키마 없는 데이터에 대한 쿼리 및 트랜잭션, 예측 가능하고 신뢰할 수 있는 성능 및 신속한 개발을 제공합니다.

다음 테이블은 DocumentDB 출력을 만들기 위한 속성 이름 및 해당 설명을 나열합니다.

<table>
<tbody>
<tr>
<td>속성 이름</td>
<td>설명</td>
</tr>
<tr>
<td>계정 이름</td>
<td>DocumentDB 계정의 이름입니다. 계정에 대한 끝점이 될 수도 있습니다.</td>
</tr>
<tr>
<td>계정 키</td>
<td>DocumentDB 계정에 대한 공유 선택키입니다.</td>
</tr>
<tr>
<td>데이터베이스</td>
<td>DocumentDB 데이터베이스 이름입니다.</td>
</tr>
<tr>
<td>컬렉션 이름 패턴</td>
<td>사용할 컬렉션에 대한 컬렉션 이름 패턴입니다. 컬렉션 이름 형식은 선택적 {partition} 토큰을 사용하여 구성할 수 있으며 파티션은 0부터 시작합니다.<BR>예: 다음은 유효한 입력입니다.<BR>MyCollection{partition}<BR>MyCollection<BR>컬렉션은 스트림 분석 작업이 시작하기 전에 존재해야 하며 자동으로 만들어지지 않습니다.</td>
</tr>
<tr>
<td>Partition Key</td>
<td>컬렉션에서 출력 분할을 위한 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.</td>
</tr>
<tr>
<td>문서 ID</td>
<td>삽입 또는 업데이트 작업이 기반으로 하는 기본 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.</td>
</tr>
</tbody>
</table>


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계
사물 인터넷에서 발생한 데이터에 대한 스트리밍 분석용 관리 서비스, 스트림 분석에 대해 소개하였습니다. 이 서비스에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0907_2016-->