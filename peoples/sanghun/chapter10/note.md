### chapter10 명시적인 매개변수

컬렉션 형식의 변수를 인자로 사용하는 함수의 예를 생각해보자. 함수 작성자의 입장에서는 변수의 이름이 명확한데 매개변수를 변경할까 싶지만, 시간이 지난 후에는 작성할 때의 의미가 퇴색 되기도 한다. 또는 창의적인 요구사항에 의해 창의적인 코드가 작성되거나. 

```csharp
internal class Program
{
    static void Main(string[] args)
    {
        AddressHelper addressHelper = new AddressHelper();
        List<AddressInfo> addressInfos = new List<AddressInfo>();

        var distances = addressHelper.DistanceEachother(
            addressInfos.Select(x => (x.Latitude, x.Longitude))
                        .ToList());
    }
}

public class AddressInfo
{
    public string BuildingName { get; set; }
    public string PostNumber { get; set; }
    public double Latitude { get; set; }
    public double Longitude { get; set; }
}

public class AddressHelper
{
    public List<double> DistanceEachother(
        List<AddressInfo> addressInfos)
    {
        //addressInfos 에서 위경도를 가져와서 사용.
        //함수 외부에선 함수에서 필요한 값을 알 수 없다.
        return default;
    }

    public List<double> DistanceEachother(
        List<(double Latitude, double Longitude)> locations)
    {
        //함수에서 필요한 값이 명확히 드러난다.
        return default;
    }
}
```