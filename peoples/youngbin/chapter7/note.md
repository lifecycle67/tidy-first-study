# Chapter 7: 선언과 초기화를 함께 옮기기

선언과 초기화 뿐만 아니라, 필요한 경우에는 실제 선언하고 초기화 한 것을 사용하는 코드와도 가까이 두어야 하지 않을까 하는 생각을 했다. 선언과 동시에 초기화를 하고, 이후 함수에서 한참 뒤에 이를 사용하는 루틴을 본 적이 여러번 있다. 이런 부분에서 오류가 나서 디버깅 할 때 "이 변수는 어디서 온 건지" 찾기가 힘들어서 디버깅이 오래 걸린 경험이 많은 것 같다.

```php
public function train_processing_result(Request $request){
    try{
        
        ...
        
        $image_count_by_category        = [];

        // 중간 156줄 정도 있음...
        
        if($task_type == 'classification') {
            ...

            $image_count_by_category = $image_count_query->where( ... )
                ->groupBy( ... )
                ->orderBy( ... )
                ->select(
                    ...
                )->get();
        }
        ...
    } catch (\Exception $e){
      ...
    }
}
```

```php
public function train_processing_result(Request $request){
    try{
        
        ...
        
        

        // 중간 156줄 정도 있음...
        $image_count_by_category        = [];
        if($task_type == 'classification') {
            ...

            $image_count_by_category = $image_count_query->where( ... )
                ->groupBy( ... )
                ->orderBy( ... )
                ->select(
                    ...
                )->get();
        }
        ...
    } catch (\Exception $e){
      ...
    }
}
```
