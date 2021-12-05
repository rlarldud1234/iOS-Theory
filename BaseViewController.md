## BaseViewController

### BaseViewController란?

#### BaseViewController는 커스텀 뷰컨트롤러이다.

#### UIViewController에 우리가 사용하기 편하도록 추가로 BaseViewControllerf라는걸 만들어줘서 UIViewController 대신 상속시켜준다.

### BaseViewController 사용 이유

#### 우리가 코딩을 하다 보면 중복적으로 사용하게 되는 부분들이 있다. 예를 들어

- ##### 내가 만드는 앱의 뷰의 배경화면이 거의 흰색이라던가

- ##### 뷰컨트롤러마다 Snapkit, Then라이브러리를 다 import 해줘야 한다던가

- ##### RxSwift를 사용한다면 disposebag등 항상 필요한 변수가 있다던가

#### 이런 부분들을 BaseViewController를 따로 정의를 해주고 BaseViewController를 상속받아 중복 없이 코딩을 하게 된다.

#### 그리고 협업을 하다보면, 누구는 ViewDidLoad()메소드에서 다 작성하는 사람이 있는가 하면 누구는 LoadView()에 다 작성해주거나 섞어서 작성하는 사람도 있다.

##### 이런 부분들을 커스텀뷰컨트롤러인 BaseViewController를 만들어주고, 따로 메소드로 정의를 해주면 통일성 있고, 가독성있게 코딩을 해줄 수 있다.

#### 예

- ##### 뷰넣기: configureUI() 메소드에서

- ##### 제약사항: setupConstraints() 메소드에서

```swift
import UIKit// UIViewController를 상속받기 때문에 UIKit은 필수입니다

// 추가로 프레임워크 추가
import RxSwift
import RxCocoa
import Then
import SnapKit

//BaseViewController는 UIViewController를 상속습니다.

class BaseViewController: UIViewController{
  
  //자주 사용하는 프로퍼티
  
  var disposebag = DisposeBag()
  
  init() {
    super.init(nibName: nil, bundle: nil)
  }
  
  required init?(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }
  
  override func viewDidLoad() {
    self.view.backgroundColor = .white
    self.view.setNeedsUpdateConstraints
  }
  
  override func viewDidLayoutSubviews() {
    self.configureUI()
    self.setupConstraints()
  }
  
  override func updateViewConstraints() {
    if !self.didSetupConstraints {
      self.setupConstraints()
      self.didSetupConstraints = true
    }
  }
  
  func configureUI() {
    //Override addView
  }
  
  func setupConstraints() {
    //Override point
  }
}
```

#### 이런 방법으로 셀도 가능하다

```swift
import UIKit

import Snapkit
import Then


class BaseTableViewCell: UITableViewCell {
  override init(style: UITableViewCell.Cellstyle reuseIdentifier: String?) P
  super.init(style: style, reuseIdentifier: reuseIdentifier)
  self.configureUI()
  self.setupConstraints()
}
```

#### BaseTableViewCell 사용하기

```swift
import UIKit

class TestCell: BaseTableViewCell {
  private let titleLabel = UILabel()
  private let detailLabel = UILabel()
  
  override func configureUI(){
    [titleLabel, detailLabel].forEach{
      view.addSubview($0)
    }
  }
  
  override func setupConstraints() {
    titleLabel.snp.makeConstraints {
      $0.centerX.equalToSuperview()
      $0.top.equalToSuperview().inset(100)
    }
    
    detailLabel.snp.makeConstraints {
      $0.top.equalTo(titleLabel.snp.bottom).inset(50)
      $0.centerX.equalToSuperview()
    }
  }
}
```

