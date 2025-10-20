## 선언적으로 코드 작성하기

프로그래밍에서 **선언적으로 코드를 작성한다**는 말은,
"무엇을 할지(What)"를 명시하고, "어떻게 할지(How)"는 최소화하는 것을 뜻한다.
즉, **로직의 흐름을 직접 제어하기보다 규칙과 목표를 정의하는 방식**이다.
그렇다면 반대로, 코드를 선언적으로 작성하지 않으면 어떤 일이 벌어질까?

> 모든 조건과 절차를 직접 기술해야 하고,
> 코드가 점점 '규칙을 서술하는 문장'이 아니라 '명령의 나열'로 변한다.
> 이렇게 되면 코드의 길이는 길어지고, 수정 시 실수가 발생하기 쉬워지며,
> 전체 구조를 한눈에 이해하기가 어려워진다.

**📝 코드 예시**

```javascript
const getMessageText = (message) => {
  const { category, status } = message;

  const defaultValue = '새로운 메시지';

  // 긴급한 시스템 메시지인 경우
  if (category === 'SYSTEM' && status === 'URGENT') {
    return '[긴급] 중요한 시스템 공지';
  }

  // 긴급한 사용자 메시지인 경우
  if (category === 'USER' && status === 'URGENT') {
    return '사용자로부터 온 중요한 메시지';
  }

  return defaultValue;
};
```

**👃 코드 냄새 맡아보기**

위 코드는 모든 조건을 직접 if문으로 나열하며 절차를 제어하고 있다.
새로운 메시지 유형이 추가될 때마다 if 문이 계속 늘어나고,
결국 함수는 점점 길어지며 복잡해진다.

조건이 많아질수록 "이 함수가 어떤 분기들을 처리하는지"를 파악하기 위해
코드를 위아래로 오가며 읽어야 한다.
이는 유지보수시 인지 부하를 높이고, 실수를 유발하기 쉽다.

**✏️ 개선해보기**

```javascript
const getMessageText = (message) => {
  const { category, status } = message;

  const defaultValue = "새로운 메시지";

  // 특별한 처리가 필요한 경우를 '규칙'으로 정의
  const messageFormatterMap = {
    SYSTEM: {
      URGENT: "[긴급] 중요한 시스템 공지",
    },
    USER: {
      URGENT: "사용자로부터 온 중요한 메시지",
    },
  } as const;

  // 규칙에 맞는 함수를 찾아 실행
  const formatted = messageFormatterMap[category]?.[status];

  if (formatted) {
    return formatted;
  }

  return defaultValue;
};
```

위 코드는 로직의 흐름보다 규칙과 의미를 정의하는 데 초점을 맞추고 있다.
관계를 단순히 데이터로 표현한 것이다.
개선 포인트는 아래와 같다.

> 새로운 조건이 생기더라도 messageFormatterMap에 한 줄만 추가하면 된다.
> 함수의 주요 로직을 수정하지 않아도 된다.
> 규칙이 명시적으로 드러나기 때문에 코드를 읽는 사람이 무엇이 정의되어 있는지를 한눈에 파악할 수 있다.

👀 추가적으로

messageFormatterMap을 외부로 분리해야 할까?

messageFormatterMap이 함수내에 존재한다면, 응집도가 높아 코드의 맥락을 파악하기가 용이하다.
하지만 아래 두가지 경우에는 messageFormatterMap을 외부로 분리하는것이 바람직하다.

1. messageFormatterMap이 너무 길어져 함수의 가독성을 해치는 경우
2. messageFormatterMap이 다른 함수나 컴포넌트에서도 재사용되어야 하는 경우
