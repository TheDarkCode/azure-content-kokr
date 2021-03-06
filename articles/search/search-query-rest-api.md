<properties
    pageTitle="REST API를 사용하여 Azure 검색 인덱스 쿼리 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 검색에서 검색 쿼리를 작성하고 검색 매개 변수를 사용하여 검색 결과를 필터링하고 정렬합니다."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# REST API를 사용하여 Azure 검색 인덱스 쿼리
> [AZURE.SELECTOR]
- [개요](search-query-overview.md)
- [포털](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST (영문)](search-query-rest-api.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스를 쿼리하는 방법을 보여줍니다.

이 연습을 시작하기 전에 [Azure 검색 인덱스를 만들고](search-what-is-an-index.md) [데이터로 채워야](search-what-is-data-import.md) 합니다.

## I. Azure 검색 서비스의 쿼리 API 키 식별
Azure 검색 REST API에 대한 모든 검색 작업의 주요 구성 요소는 프로비전한 서비스에 대해 생성한 *API 키*입니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 응용 프로그램과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

1. 서비스의 API 키를 찾으려면 [Azure 포털](https://portal.azure.com/)에 로그인해야 합니다.
2. Azure 검색 서비스의 블레이드로 이동합니다.
3. "키" 아이콘을 클릭합니다.

서비스에는 *관리 키* 및 *쿼리 키*가 있습니다.

 - 기본 및 보조 *관리 키*는 서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다. 두 개의 키가 있으므로 기본 키를 다시 생성하려는 경우 보조 키를 사용하여 계속할 수 있고 반대도 가능합니다.
 - *쿼리 키*는 인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 실행하는 클라이언트 응용 프로그램에 배포됩니다.

인덱스를 쿼리하기 위해 쿼리 키 중 하나를 사용할 수 있습니다. 또한 쿼리에 관리 키를 사용할 수 있지만 [최소 권한의 원칙](https://en.wikipedia.org/wiki/Principle_of_least_privilege)에 따라 응용 프로그램 코드에 쿼리 키를 사용해야 합니다.

## II. 쿼리 작성
[REST API를 사용하여 인덱스를 검색](https://msdn.microsoft.com/library/azure/dn798927.aspx)하는 두 가지 방법이 있습니다. 한 가지 방법은 쿼리 매개 변수를 요청 본문의 JSON 개체에 정의한 위치에 HTTP 게시 요청을 발급하는 것입니다. 다른 방법은 쿼리 매개 변수를 요청 URL 내에 정의한 위치에 HTTP 가져오기 요청을 발급하는 것입니다. 게시는 가져오기보다 쿼리 매개 변수의 크기에서 [제한을 완화](https://msdn.microsoft.com/library/azure/dn798927.aspx)합니다. 따라서 GET을 사용하는 것이 보다 편리한 특별한 경우가 아니라면 게시를 사용하는 것이 좋습니다.

게시 및 가져오기 모두의 경우 요청 URL에서 *서비스 이름*, *인덱스 이름* 및 적절한 *API 버전*을 제공해야 합니다(이 문서를 게시할 때 현재 API 버전은 `2015-02-28`임). 가져오기의 경우 URL의 끝의 *쿼리 문자열*은 쿼리 매개 변수를 제공하는 위치입니다. URL 형식은 아래를 참조하세요.

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

게시에 대한 형식이 동일하지만 쿼리 문자열 매개 변수에서 API 버전입니다.



#### 예제 쿼리

다음은 "호텔"이라는 인덱스에 대한 몇 가지 예제 쿼리입니다. 이러한 쿼리는 가져오기 및 게시 형식으로 표시됩니다.

전체 인덱스에서 용어 '예산'을 검색하고 `hotelName` 필드를 반환합니다.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

인덱스에 필터를 적용하여 하루에 150 달러가 저렴한 호텔을 찾고 `hotelId` 및 `description`를 반환합니다.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

전체 인덱스를 검색하고, 특정 필드(`lastRenovationDate`)를 내림차순으로 정렬하며, 상위 두 개의 결과를 가지고, `hotelName` 및 `lastRenovationDate`를 표시합니다.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## III. HTTP 요청 제출
쿼리를 HTTP 요청 URL(가져오기의 경우) 또는 본문(게시의 경우)의 일부로 작성했으므로 요청 헤더를 정의하고 쿼리를 제출할 수 있습니다.

#### 요청 및 요청 헤더
가져오기에 두 개의 요청 헤더 또는 게시에 세 개의 요청 헤더를 정의해야 합니다.
1. 위의 I 단계에서 찾은 쿼리 키로 `api-key` 헤더를 설정해야 합니다. 또한 관리 키를 `api-key` 헤더로 사용할 수 있지만 인덱스 및 문서에 대한 읽기 전용 액세스 권한을 단독으로 부여하기 때문에 쿼리 키를 사용하는 것이 좋습니다.
2. `Accept` 헤더를 `application/json`으로 설정해야 합니다.
3. 게시의 경우에만 `Content-Type` 헤더를 `application/json`으로 설정해야 합니다.

"모텔"이라는 용어를 검색하는 간단한 쿼리를 사용하여 Azure 검색 REST API를 사용하는 "호텔" 인덱스를 검색하는 HTTP 가져오기 요청은 아래를 참조하세요.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

이번에는 HTTP 게시를 사용하는 동일한 예제 쿼리입니다.

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

성공적인 쿼리 요청은 `200 OK`인 상태 코드를 발생시키고 검색 결과는 응답 본문에서 JSON 형식으로 반환됩니다. 다음은 위의 쿼리 모양에 대한 결과이며 "호텔" 인덱스가 [REST API를 사용하여 Azure 검색에서 데이터 가져오기](search-import-data-rest-api.md)의 샘플 데이터로 채워진다고 가정합니다(명확하게 하기 위해 JSON 형식을 지정함).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

자세한 내용은 [문서 검색](https://msdn.microsoft.com/library/azure/dn798927.aspx)의 "응답" 섹션을 방문합니다. 오류가 발생한 경우 반환될 수 있는 기타 HTTP 상태 코드에 대한 자세한 내용은 [HTTP 상태 코드(Azure 검색)](https://msdn.microsoft.com/library/azure/dn798925.aspx)를 참조하세요.

<!---HONumber=AcomDC_0831_2016-->