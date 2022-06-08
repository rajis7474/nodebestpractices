# 자식 프로세스를 다룰 땐 특히 조심해라

### 한문단 요약

아무리 자식 프로세스가 성능이 좋더라도 주의해서 사용해야 한다. 사용자의 입력은 필터링이 되어있지 않다면 자식 프로세스의 사용을 금해야 한다.
시스템 수준의 로직을 실행하는 필터링 되지 않은 입력의 위험성은 무한하다. 이러한 위험성에는 원격 코드 실행, 민감한 시스템 데이터 노출 및 데이터 손실까지 있다. 이를 예방하기 위한 체크리스트는 다음과 같다.

- 모든 경우에 사용자의 입력을 피하고, 그렇지 않다면 유효성을 검사하고 삭제해라
- 사용자/그룹  ID를 사용하여 상위 및 하위 프로세스의 사용을 제한해라
- 다른 준비가 실패할 경우 원치 않는 부작용을 방지하기 위해 격리된 환경 내에서 프로세스를 실행해라

### 예시 코드: 필터링 되지 않은 자식 프로세스 실행의 위험성

```javascript
const { exec } = require('child_process');

...

// 두개의 인자를 갖는 스크립트를 사용하고 그중 하나는 필터링 되지 않은 사용자의 입력이다.
exec('"/path/to/test file/someScript.sh" --someOption ' + input);

// -> 사용자가 단순히 '&& rm -rf --no-preserve-root /' 이렇게 입력하면 어떤 일이 일어날 수 있는지 생각해봐라
// 당신은 원치않는것을 경험할 것이다.
```

### 추가 내용

Node.js 자식 프로세스 [문서](https://nodejs.org/dist/latest-v8.x/docs/api/child_process.html#child_process_child_process_exec_command_options_callback)로 부터:

> 이 함수에 필터링 되지 않은 사용자의 입력을 전달하지 마라. 쉘 메타 문자를 포함하는 모든 입력은 임의의 명령 실행을 시작하는데 사용할 수 있다.