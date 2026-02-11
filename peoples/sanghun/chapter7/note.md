### chapter7 선언과 초기화를 함께 옮기기
변수를 사용할 때 신경 쓰이던 부분은 참조할 때 초기화 이후에 값이 변경되었는지 여부였다. 
선언과 초기화와 사용하는 코드가 가까이 묶여 있다면 이런 경우는 피할 수 있을 것 같다.

```csharp
var nextNodes = node.Next
    ?.Select(taskName => allNodes.Find(t => t.Name == taskName))
     .Where(n => n != null);
if (nextNodes != null || nextNodes?.Any() == false)
    node.NextNodes = new List<DagNode>(nextNodes!);

foreach (var nextNode in node.NextNodes ?? Enumerable.Empty<DagNode>())
{
    if (nextNode == null)
        continue;
    BuildDag(nextNode, allNodes);
}
```