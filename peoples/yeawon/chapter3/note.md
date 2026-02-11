## 3장. 대칭으로 맞추기

- **일관성, 패턴, 한 가지 방식**을 선택해서 정하기
    - ex) 초기화 지연(lazy initialization)의 다양한 구현
        
        ```python
        # 가장 직관적
        foo()
        		if foo is nil   # foo가 아직 없으면(nil)이면
        			foo := ...    # foo를 계산해서 저장
        		return foo      # foo 반환
        
        foo() 
        	return foo if foo not nil 
        	foo := ... 
        	retrun foo
        
        # 기교를 가한 코드 
        foo() 
        	return foo not nil 
        		? foo := ... 
        		: foo := ...
        
        # 할당문이 수식이면 더욱 까다로움 
        foo() 
        	return foo := foo not nil 
        		? foo 
        		: ... 
        
        # 조건이 잘 드러나지 않은 수준의 코드 
        foo() 
        	return foo := foo || ...
        ```
        
- 비슷해 보이지만 **같지 않은 루틴을 찾아 분리**하라
- **의도가 한눈에 보이는, 반복 가능한 모양**