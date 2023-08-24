함수형 프로그래밍은 자료처리를 `수학적 함수( f(x)=y )의 계산`으로 취급하고 **상태와 데이터 변경을 피하는 프로그래밍 패러다임**의 하나입니다.

Swift에서 `함수형 프로그래밍`이란 데이터를 다룰 때 `var` 대신 `let`을 사용하는 것을 의미합니다. 이것은 주로 `함수형 코드`가 `명령형 코드`보다 버그가 적고 이해하기 쉽다는 이점이 있습니다. `명령형 프로그래밍`은 `함수형 프로그래밍`의 반대이고, **프로그램의 상태를 변경하는 명령문을 사용하는 패러다임**입니다.

함수형 프로그래밍의 핵심은 코드를 프로젝트 전체에서 `여러 번 사용할 수 있는` 더 작고 `pure functions`으로 나누는 것입니다. `Pure function`은 프로그램 전체에 `side effects가 없는` 함수입니다. 즉, 프로그램에 참여하는 다른 함수를 돕기 위해 존재한다는 의미입니다.

명령형 프로그래밍으로 문제를 어떻게 문제를 해결하는지 보고, 함수형 프로그래밍에서는 그 문제를 어떻게 해결할 수 있는지 예시로 확인하겠습니다.

명령형 프로그래밍 예시
```swift
let numbers = [1, 2, 3, 4, 5]
var evenNumbers = [Int]()

for i in 0..<numbers.count {
    let number = numbers[i]
    if number % 2 == 0 {
        evenNumbers.append(number)
    }
}
```
numbers 배열에서 짝수만 추출해서 evenNumbers에 저장하는 과정입니다.

함수형 프로그래밍 예시
```swift
let evenNumbers = [1, 2, 3, 4, 5].filter { (number) -> Bool in
    if number % 2 == 0 {
        return true
    } else {
        return false
    }
}

// 더 짧은 구현
let evenNumbers = [1, 2, 3, 4, 5].filter { $0 % 2 == 0 }
```

우리는 본질적으로 8줄의 코드를 1줄로 바꿨고, 코드가 가독성있고 이해하기 쉽게 만들었습니다.

다른 예시도 있습니다.
`map`과 `compactMap` 함수가 있습니다.
`map`은 각 요소를 연산해서 새로운 array를 리턴하는 함수입니다. `nil`을 포함하기 때문에 옵셔널 값을을 포함합니다.
`compactMap`은 `map`과 비슷하지만 `nil`을 포함하지 않습니다. 그래서 옵셔널 값을 포함하지 않습니다.

코드는 다음과 같습니다.
```swift
let arrayWithOptionals = ["1", "two", "3"].map { Int($0) }
print(arrayWithOptionals) // [Optional(1), nil, Optional(3)]

let arrayWithNoOptionals = ["1", "two", "3"].compactMap { Int($0) }
print(arrayWithNoOptionals) // [1, 3]
```

<br>

※ 참고출처  
[민소네](http://minsone.github.io/programming/functional-programming-with-example-in-swift)  
[sdrzn](https://medium.com/@sdrzn/functional-programming-in-swift-221a8cabb8c)
