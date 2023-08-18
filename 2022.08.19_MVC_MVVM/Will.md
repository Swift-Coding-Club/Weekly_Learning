# Architecture

## 1. MVC
`Model`, `View`, `Controller` 세 가지 논리 구성 요소로 이루어져 있는 아키텍처

### Model
앱의 `Business logic`을 담당하고 있고 **`View`에 표시할 데이터를 저장**하고 있습니다.

### View
앱의 `Presentation logic`을 담당하고 있고 **`Model`의 데이터를 표시**합니다.

### Controller
**`View`와 `Model`을 포함**하고 있고 **View에 대한 인터렉션을 어떻게 처리할 지(handling)에 대한 로직을 정의**합니다. `View`와 `Model`은 `Controller`의 멤버(변수, 함수)에 대해서 알지 못합니다.

도식화하면 다음과 같습니다.

<img src="https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Art/cocoa_mvc.gif" width=50%>

1. View에서 이벤트 발생
2. Controller에서 Model 변경
3. Model이 변경된 것을 Controller에 알림
4. Controller가 변경된 Model 데이터로 View를 핸들링(업데이트)

<br>

<details><summary>예시 코드</summary>


```swift
// 목적: MVC를 코드로 직접 작성하면서 어떤 특징이 있는지 파악하기위함
// 예시: 텍스트 필드에 값을 입력하고 입력 버튼을 클릭하면 입력된 값이 라벨에 표시됨.

// SearchModel
import Foundation

struct SearchModel {
  var name: String
  
  init(name: String = "") {
    self.name = name
  }
}

// HomeView
import UIKit

final class HomeView: UIView {
  
  let stackView: UIStackView = {
    let sv = UIStackView()
    sv.axis = .horizontal
    sv.spacing = 16
    sv.translatesAutoresizingMaskIntoConstraints = false
    return sv
  }()
  
  let textField: UITextField = {
    let tf = UITextField()
    tf.textColor = .black
    tf.attributedPlaceholder = NSAttributedString(string: "입력하시오.",
                                                  attributes: [NSAttributedString.Key.foregroundColor: UIColor.systemGray])
    tf.translatesAutoresizingMaskIntoConstraints = false
    return tf
  }()
  
  let searchButton: UIButton = {
    let bt = UIButton()
    bt.setTitle("입력", for: .normal)
    bt.setTitleColor(.black, for: .normal)
    bt.setTitleColor(.systemGray, for: .highlighted)
    bt.layer.cornerRadius = 30
    bt.layer.borderWidth = 1
    bt.translatesAutoresizingMaskIntoConstraints = false
    return bt
  }()
  
  let resultLabel: UILabel = {
    let l = UILabel()
    l.textColor = .black
    l.translatesAutoresizingMaskIntoConstraints = false
    return l
  }()
  
  init() {
    super.init(frame: .zero)
    setupView()
  }
  
  required init?(coder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }
  
  private func setupView() {
    translatesAutoresizingMaskIntoConstraints = false
    
    addSubview(stackView)
    stackView.addArrangedSubview(textField)
    stackView.addArrangedSubview(searchButton)
    addSubview(resultLabel)
    
    NSLayoutConstraint.activate([
      stackView.centerYAnchor.constraint(equalTo: centerYAnchor),
      stackView.centerXAnchor.constraint(equalTo: centerXAnchor),
      
      textField.widthAnchor.constraint(equalToConstant: 100),
      
      searchButton.widthAnchor.constraint(equalToConstant: 80),
      searchButton.heightAnchor.constraint(equalToConstant: 60),
      
      resultLabel.topAnchor.constraint(equalTo: stackView.bottomAnchor,
                                       constant: 40),
      resultLabel.centerXAnchor.constraint(equalTo: centerXAnchor),
    ])
  }
}

// ViewController
import UIKit

class ViewController: UIViewController {
  
  var searchModel = SearchModel() // Model 객체 생성
  let homeView = HomeView() // View 객체 생성
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
    view.backgroundColor = .white
    
    setupUI()
    addAction()
  }
  
  private func setupUI() {
    view.addSubview(homeView)
    NSLayoutConstraint.activate([
      homeView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
      homeView.leftAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leftAnchor),
      homeView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
      homeView.rightAnchor.constraint(equalTo: view.safeAreaLayoutGuide.rightAnchor)
    ])
  }
  
  // 버튼 클릭 이벤트 핸들링
  private func addAction() {
    homeView.searchButton.addAction(UIAction(handler: { _ in
      tappedSearchButton()
    }), for: .touchUpInside)
  }

  func tappedSearchButton() {
    // View에서 이벤트 발생 -> Model 업데이트
    self.searchModel.name = self.homeView.searchTextField.text ?? ""
    // Model 업데이트 -> View 업데이트
    self.homeView.resultLabel.text = self.searchModel.name
  }
}
```
</details>

<br>

미리보기는 다음과 같습니다.

<img src="https://github.com/Minny27/TIL_PUBLIC/assets/68800789/59811e4d-63bb-4c8f-9395-4b54390d9eea" width=25%>



예시에서도 보면 알 수 있듯이 `Model`은 `View`에 표시할 데이터를 저장하고 있고,`View`는 `Model`의 데이터를 화면에 표시합니다.  
또한 `Controller`는 `Model`과 `View`를 객체를 생성하고 `View`에서의 Event를 핸들링(어떻게 처리)합니다.
`Model`은 비즈니스 로직을 포함하고 있고 `View`는 프레젠테이션 로직을, `Controller`는 이 모두를 가지는 것을 알 수 있습니다.  
`Controller`는 `View`와 `Model` 모두를 포함하고 있기 때문에 한 화면에 에 `View`가 많아질 수록 포함해야하는 `View`와 `Model`많아지게 됩니다. 이 때문에 `Controller`가 비대해질 수 있다는 것을 알 수 있습니다.

단순하고 구현이 쉽다는 장점이 있지만 `Controller`의 비중이 높아지는 단점이 있습니다.
또한 이벤트 핸들링을 `Controller`에서 구현하다보니 테스트 코드 작성도 힘들 것으로 예상됩니다.
이 부분은 추후에 다시 설명하겠습니다.

## MVVM

`Model`과 `View`는 MVC 패턴과 유사합니다.

### ViewModel
`ViewModel`은 `View`에 이벤트가 발생했을 때, `View`를 어떻게 변경할 지에 대한 `비즈니스 로직을 포함`하고 있습니다. 또한 `View`에 이벤트가 발생했을 때, `Model`을 거쳐서 `View`에 알림을 보냅니다.  
`MVC` 패턴의 `Controller`와 유사하지만, `ViewModel`은 `View`에 대한 코드를 포함하고 있지 않습니다.  
Presentation logic(addSubview(), 제약조건 설정 등)과 분리되어 있습니다.

도식화하면 다음과 같습니다.

<img src="https://miro.medium.com/v2/resize:fit:1400/format:webp/1*fQ0LXptWrJIbGS9LZE8p6g.png" width=50%>

1. View에 이벤트 발생
2. ViewModel을 통해서 View와 Model을 Binding
3. Model 업데이트
4. Model이 변경된 것을 View Model에게 알림
5. View Model이 View에게 Model이 변경된 것을 알림
6. View 업데이트

<br>

<details><summary>예시 코드</summary>

```swift
// 목적: MVVM을 코드로 직접 작성하면서 어떤 특징이 있는지 파악하기위함
// 예시: 텍스트 필드에 값을 입력하고 입력 버튼을 클릭하면 입력된 값이 라벨에 표시됨.

// SearchModel
import Foundation

struct SearchModel {
    var name: String
    
    init(name: String = "") {
        self.name = name
    }
}

// SearchViewModel
final class SearchViewModel {
    
    var searchModel = SearchModel()
    
    // Model Update
    func tappedSearchButton(text: String) {
        self.searchModel.name = text
    }
}

//  HomeView.swift
import UIKit

final class HomeView: UIView {
    
    let stackView: UIStackView = {
        let sv = UIStackView()
        sv.axis = .horizontal
        sv.spacing = 16
        sv.translatesAutoresizingMaskIntoConstraints = false
        return sv
    }()
    
    let searchTextField: UITextField = {
        let tf = UITextField()
        tf.textColor = .black
        tf.attributedPlaceholder = NSAttributedString(string: "입력하시오.",
                                                      attributes: [NSAttributedString.Key.foregroundColor: UIColor.systemGray])
        tf.translatesAutoresizingMaskIntoConstraints = false
        return tf
    }()
    
    let searchButton: UIButton = {
        let bt = UIButton()
        bt.setTitle("입력", for: .normal)
        bt.setTitleColor(.black, for: .normal)
        bt.setTitleColor(.systemGray, for: .highlighted)
        bt.layer.cornerRadius = 30
        bt.layer.borderWidth = 1
        bt.translatesAutoresizingMaskIntoConstraints = false
        return bt
    }()
    
    let resultLabel: UILabel = {
        let l = UILabel()
        l.textColor = .black
        l.translatesAutoresizingMaskIntoConstraints = false
        return l
    }()
    
    init() {
        super.init(frame: .zero)
        setupView()
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    private func setupView() {
        translatesAutoresizingMaskIntoConstraints = false
        
        addSubview(stackView)
        stackView.addArrangedSubview(searchTextField)
        stackView.addArrangedSubview(searchButton)
        addSubview(resultLabel)
        
        NSLayoutConstraint.activate([
            stackView.topAnchor.constraint(equalTo: topAnchor, constant: 100),
            stackView.centerXAnchor.constraint(equalTo: centerXAnchor),
            
            searchTextField.widthAnchor.constraint(equalToConstant: 100),
            
            searchButton.widthAnchor.constraint(equalToConstant: 80),
            searchButton.heightAnchor.constraint(equalToConstant: 60),
            
            resultLabel.topAnchor.constraint(equalTo: stackView.bottomAnchor,
                                             constant: 40),
            resultLabel.centerXAnchor.constraint(equalTo: centerXAnchor),
        ])
    }
}

//  ViewController.swift
import UIKit

final class ViewController: UIViewController {
    
    let viewModel = SearchViewModel()
    let homeView = HomeView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.backgroundColor = .white
        
        setupUI()
        addAction()
    }
    
    private func setupUI() {
        view.addSubview(homeView)
        NSLayoutConstraint.activate([
            homeView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            homeView.leftAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leftAnchor),
            homeView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
            homeView.rightAnchor.constraint(equalTo: view.safeAreaLayoutGuide.rightAnchor)
        ])
        
        homeView.searchTextField.accessibilityIdentifier = AccessibilityIdentifier.searchTextField
        homeView.searchButton.accessibilityIdentifier = AccessibilityIdentifier.searchButton
        homeView.resultLabel.accessibilityIdentifier = AccessibilityIdentifier.resultLabel
    }
    
    private func addAction() {
        homeView.searchButton.addAction(UIAction(handler: { _ in
            // View에서 이벤트 발생 및 ViewModel에게 명령 전달
            self.viewModel.tappedSearchButton(text: self.homeView.searchTextField.text ?? "")
            
            // View Model이 View와 Model을 Binding
            self.homeView.resultLabel.text = self.viewModel.searchModel.name
        }), for: .touchUpInside)
    }
}
```
</details>

### UI Test
UI 테스트 코드는 `MVVM`에도 동일하게 `View` 논리 컴포넌트가 있어서 코드는 비슷하게 작성됩니다.

<br>

<details><summary>예시 코드</summary>

```swift
//  SwiftGitTestUITests
import XCTest
import Foundation
@testable import SwiftGitTest

final class SwiftGitTestUITests: XCTestCase {
    
    private var app: XCUIApplication!
    private var searchTextField: XCUIElement!
    private var searchButton: XCUIElement!
    private var resultLabel: XCUIElement!
    
    override func setUpWithError() throws {
        try super.setUpWithError()
        app = XCUIApplication()
        searchTextField = app.textFields["searchTextField"]
        searchButton = app.buttons["searchButton"]
        resultLabel = app.staticTexts["resultLabel"]
        
        app.launch()
        
        continueAfterFailure = false
    }
    
    override func tearDownWithError() throws {
        app = nil
        try super.tearDownWithError()
    }
    
    func testSearchSuccess() throws {
        let input = "will"
        
        searchTextField.tap()
        
        input.forEach {
            app.keys[String($0)].tap()
        }
        
        self.searchButton.tap()
        
        Thread.sleep(forTimeInterval: 1)
        
        XCTAssertEqual(input, resultLabel.label)
    }
    
    func testSearchTextDoNotMatchResultlabel() throws {
        let input = "will"
        
        searchTextField.tap()
        
        input.forEach {
            app.keys[String($0)].tap()
        }
        
        self.searchButton.tap()
        
        Thread.sleep(forTimeInterval: 1)
        
        XCTAssertNotEqual(input, "error")
    }
}
```
</details>

### Unit Test
`MVC`와 `MVVM`의 유닛 테스트 코드는 유사하게 작성되는 것처럼 보이지만 생각보다 그렇지 않았습니다.

<details><summary>MVC 예시 코드</summary>

```swift
//  MVCSearchTest
import XCTest
@testable import SwiftGitTest

final class MVCSearchTest: XCTestCase {

    var viewController = ViewController()

    func testSearchSuccess() throws {
        let input = "will"
        viewController.homeView.searchTextField.text = input
        viewController.tappedSearchButton()
        
        XCTAssertEqual(input, viewController.searchModel.name)
    }

    func testSearchFailure() throws {
        let input = "will"
        viewController.homeView.searchTextField.text = "error"
        viewController.tappedSearchButton()
        
        XCTAssertNotEqual(input, viewController.searchModel.name)
    }
}

```
</details>

<br>

<details><summary>MVVM 예시 코드</summary>

```swift
//  MVVMSearchTest
import XCTest
@testable import SwiftGitTest

final class MVVMSearchTest: XCTestCase {
    
    var viewModel = SearchViewModel()

    func testSearchSuccess() throws {
        let input = "will"
        viewModel.tappedSearchButton(text: input)
        
        XCTAssertEqual(input, viewModel.searchModel.name)
    }

    func testSearchFailure() throws {
        let input = "will"
        viewModel.tappedSearchButton(text: "error")
        
        XCTAssertNotEqual(input, viewModel.searchModel.name)
    }
}
```
</details>

<br>

유닛 테스트는 `tappedSearchButton()` 메서드를 실행해서 search할 텍스트가 Model의 name과 일치하는지 확인하는 테스트입니다.  

`MVC`에서는 `Controller`에서 `Model`을 포함하고 있기 때문에 `text`라는 파라미터로 받지 않고 사용할 수 있습니다. 그래서 `MVC`로 구현했을 때는 `tappedSearchButton()`의 파라미터가 없습니다.  
파라미터가 없는 함수이기 때문에 homeView의 textfield의 text에 값을 할당한 후에 함수를 호출해서 테스트했습니다. 이것만 봐도 MVVM에서는 text 파라미터에 값을 할당해서 바로 테스트할 수 있습니다.  
만약 `MVC`에서 `controller`에 `view`, `model`, 그리고 이벤트 핸들링 코드가 엄청 많아진다고 가정한다면 일일이 그 구조를 이해해서 테스트하기 쉽지 않을 것 같습니다.

### 🤔 윌's 생각
`MVC`, `MVVM` 등을 코드로 작성해보면서 좀 더 구조적으로 어떻게 다른지 이해할 수 있는 시간이었습니다.  
`MVC`를 왜 요즘에 잘 사용하지 않는지, `MVVM`, `Data Binding`이 뭔지 좀 더 심층적으로 이해할 수 있었습니다.  
처리해야할 화면과 데이터가 많아지면 많아질수록 아키텍처의 역할이 중요하겠다는 생각이 들었습니다.  
단순히 아키텍처를 사용하는 것에 그치는 것이 아니라 왜 사용하고 어떻게 더 구조적으로 사용해야할 지에 대한 설계가 중요한 것 같습니다. :)

### ※ 참고 출처
[Document Archive](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)  
[hcleedev](https://medium.com/hcleedev/ios-swiftui%EC%9D%98-mvvm-%ED%8C%A8%ED%84%B4%EA%B3%BC-mvc%EC%99%80%EC%9D%98-%EB%B9%84%EA%B5%90-8662c96353cc)  
[vadimbulavin](https://www.vadimbulavin.com/modern-mvvm-ios-app-architecture-with-combine-and-swiftui/)