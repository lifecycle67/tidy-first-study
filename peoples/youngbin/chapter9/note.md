# Chapter 9: 설명하는 상수

고정된 숫자를 리터럴로 그냥 넣는 경우가 많이 있는데, 자주 쓰는 것이라면 의미 있는 상수로 만들어 두고 사용해야 하는 것으로 이해 하였다. 상수로 만들어 써도 되고, 아니면 프레임워크나 라이브러리에서 제공하는 상수나 Enum 등을 활용하는 것도 괜찮을 것 같다는 생각을 했다. 그 외에 그냥 숫자로 무심코 넣어두는데, 숫자로만 봐선 무슨 용도인지 모르겠는 것들, 이런것도 상수로 만들어 둬야 한다는 생각을 했다.

```cs
public async Task<IEnumerable<AwsProductResponse>> GetProductsForRDSRegion(string regionCode)
{
    ...
    
    var products = responseList != null ?
        responseList.GroupBy(x => new { x.DBInstanceClass, x.ProductDescription })
        .Select(g =>
        {
            ...
            return new AwsProductResponse
            {
                ...
                ReservedTerms = offerings.Select(o => new AwsRIPurchaseOption
                {
                    LeaseContractLength = o.Duration switch
                    {
                        31536000 => "1 year",
                        94608000 => "3 years",
                        _ => $"{o.Duration / 31536000} years"
                    },
                    PurchaseOption = o.OfferingType
                }).Distinct().ToList()
            };
        })
        : new List<AwsProductResponse>();
    return products;
}
```
```cs
public async Task<IEnumerable<AwsProductResponse>> GetProductsForRDSRegion(string regionCode)
{
    ...
    var oneYear = 31536000; // 1년 값 상수
    var products = responseList != null ?
        responseList.GroupBy(x => new { x.DBInstanceClass, x.ProductDescription })
        .Select(g =>
        {
            ...
            return new AwsProductResponse
            {
                ...
                ReservedTerms = offerings.Select(o => new AwsRIPurchaseOption
                {
                    LeaseContractLength = o.Duration switch
                    {
                        oneYear => "1 year",
                        oneYear * 3 => "3 years",
                        _ => $"{o.Duration / oneYear} years"
                    },
                    PurchaseOption = o.OfferingType
                }).Distinct().ToList()
            };
        })
        : new List<AwsProductResponse>();
    return products;
}
```
