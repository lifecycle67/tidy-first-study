## 10장. 명시적인 매개변수

- 루틴을 나눠라. **앞부분에서 매개변수 값을 채운 후, 뒷부분에서 명시적으로 전달한다**
    ```js
    // 1️⃣ AS-IS: 하나의 루틴에 다 들어있음

    // user 전체를 받음. 무엇에 의존하는지 선언부에서 안 보임
    function sendEmail(user) {  
        // 내부에서 필요한 값을 꺼냄
        const address = user.email;
        const name = user.name;
        const isActive = user.active;

        if (!isActive) return;

        mailer.send(address, `Hello ${name}`);
    }
    ```

    ```js
    // 2️⃣ TO-BE: 루틴을 나눈다

    // ✅ 앞부분: 매개변수 값 채우기
    function sendEmail(user) {
        sendEmailBody(user.email, user.name, user.active);
    }
    
    // ✅ 뒷부분: 명시적으로 전달
    function sendEmailBody(email, name, active) {
        if (!active) return;

        mailer.send(email, `Hello ${name}`);
    }
    ```

- map에서 매개변수가 블록으로 전달되는 경우가 흔하다. 이렇게 하면, 코드를 읽으면서도 어떤 데이터가 필요한지 알기 어렵다. ⇒ map에 블록(익명 함수, 람다)을 전달 X, **그 블록이 실제로 필요한 값만 명시적으로 전달하라**
    ```js
    // ❌ AS-IS: 큰 덩어리를 그대로 넘김
    const users = [
        { name: "Tom", age: 20, email: "tom@test.com" },
        { name: "Jane", age: 17, email: "jane@test.com" }
    ];

    const adultNames = users.map(user => {
        if (user.age >= 18) {
            return user.name;
        }
    });

    // ✅ TO-BE 1: 필요한 값만 전달
    const adultNames = users.map(({ name, age }) => {
        if (age >= 18) {
            return name;
        }
    });

    // ✅ TO-BE 2: 더 명시적으로 분리
    const adultNames = users.map(user =>
        toAdultName(user.name, user.age)
    );

    function toAdultName(name, age) {
        if (age >= 18) {
            return name;
        }
    }
    ```
- 명시적인 매개변수가 필요한 또 다른 경우는, 중괄호가 여러 개로 둘러싸인 코드에서 <u>환경 변수를 사용하는 경우</u>이다. **매개변수를 명시적으로 드러나게 만든 다음, 함수를 연쇄적으로 호출할 수 있게 준비하자**
    - *환경 변수: 전역 변수, 클로저 변수(바깥 함수의 변수), 설정 값, 숨겨진 외부 의존성*
- **함수가 필요로 하는 값은 전부 매개변수로 드러내자**