# Architecture

## 1. MVC
`Model`, `View`, `Controller` 세 가지 논리 구성 요소로 이루어져 있는 아키텍처

### Model
앱의 `Business logic`을 담당하고 있고 **`View`에 표시할 데이터를 저장**하고 있습니다.

### View
앱의 `Presentation logic`을 담당하고 있고 **`Model`의 데이터를 표시**합니다.

### Controller
**`View`와 `Model`을 포함**하고 있고 **View에 대한 인터렉션을 어떻게 처리할 지(handling)에 대한 로직을 정의**합니다. `View`와 `Model`은 `Controller`의 멤버(변수, 함수)에 대해서 알지 못합니다.

예시를 들어보겠습니다.


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
      self.searchModel.name = self.homeView.textField.text ?? ""
      self.homeView.resultLabel.text = self.searchModel.name
    }), for: .touchUpInside)
  }
}
```

미리보기는 다음과 같습니다.

<img src="https://github.com/Minny27/TIL_PUBLIC/assets/68800789/59811e4d-63bb-4c8f-9395-4b54390d9eea" width=25%>



예시에서도 보면 알 수 있듯이 `Model`은 `View`에 표시할 데이터를 저장하고 있고,`View`는 `Model`의 데이터를 화면에 표시합니다.
또한 `Controller`는 `Model`과 `View`를 객체를 생성하고 `View`에서의 Event를 핸들링(어떻게 처리)합니다.
`Model`은 비즈니스 로직을 포함하고 있고 `View`는 프레젠테이션 로직을, `Controller`는 이 모두를 가지는 것을 알 수 있습니다.
`Controller`는 `View`와 `Model` 모두를 포함하고 있기 때문에 한 화면에 에 `View`가 많아질 수록 포함해야하는 `View`와 `Model`많아지게 됩니다. 이 때문에 `Controller`가 비대해질 수 있다는 것을 알 수 있습니다.

단순하고 구현이 쉽다는 장점이 있지만 `Controller`의 비중이 높아지는 단점이 있습니다.
또한 이벤트 핸들링을 Controller에서 구현하다보니 테스트 코드 작성도 힘들 것으로 예상됩니다.
이 부분은 추후에 다시 설명하겠습니다.

## MVVM