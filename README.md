# Card Game

<br/>

## STEP 9-1. 카드 클래스

<br/>

### 📝 요구사항

- 카드 객체 인스턴스를 만들어서 출력한다.

<br/>

### 🏗 전체 구조

- class `Card`
  - enum `Suit`, enum `Rank` 를 갖고 있다.
- enum `Suit`
  - 카드 모양: ❤️, ♦️, ♠️, ♣️
- enum `Rank`
  - 카드 숫자: 1 ~ 13, ace(1), jack(11), queen(12), king(13)

<br/>

## STEP 9-2. 카드덱 구현

<br>

### 📝 요구사항

- 카드 객체 인스턴스를 갖고 있는 카드덱을 만든다.
- 카드덱에는 count, shuffle, removeOne, reset 기능이 있다.

<br/>

### 🏗 전체 구조

-  `InputView`: 메뉴 출력 및 입력

<br>

- `CardGame`: 전달받은 메뉴로 `CardDeck` 을 이용한 game play
  - CardDeck에서 reset()을 할 때 Card 인스턴스를 이용해서 생성을 하게 된다.
  - `CardMaker` 를 이용해서 CardDeck에서는 Card를 알지 않고 CardMaker가 생성해주는 카드들을 받는다.
- `CardGameResult`: 게임 play 결과

<br>

- `OutputView`: play 결과에 따라 출력

<br/>



### Protocol에 Generic 사용 - associatedtype

```swift
protocol Deck {
    associatedtype Element
    var count: Int { get }
    func reset()
    func shuffle()
    func removeOne() -> Element?
}

class CardDeck: Deck {
    typealias Element = Card
    
```

<br>

## STEP 9-3. 포커 딜러 출력

<br>

### 📝 요구사항

- 포커 딜러 출력을 위한 새로운 입력 메뉴를 만든다. 
  - 7카드 스터드, 5카드 스터드 방식을 입력받는다
  - 참여자 수를 입력받는다.
- 참여자는 딜러를 제외하고 1명에서 4명까지 참여할 수 있다.
- 카드가 남으면 게임을 계속 진행하고,  부족하면 게임을 종료한다.

<br/>

### 🏗 전체 구조

-  `InputView`: 메뉴 출력 및 입력
- `GameInputView`: 게임 메뉴 출력 및 카드게임 종류와 참여자 수를 입력받는다.

<br>

- `Player`: `Playerable` 프로토콜을 채택, 자신의 순서와 카드들을 갖고 있다.
  - `Playerable` 프로토콜
  - 딜러가 나눠주는 카드를 `take()` 한다.
- `Dealer`: `Playerable` 프로토콜을 채택, 카드덱을 갖고 있음
  - `Dealerable` 프로토콜
  -  플레이어에게 카드를 `deal()` 나눠준다.
  - 필요한 카드 수에 대해 덱에 카드가 충분한지 확인한다.
  - 덱을 관리한다.
- `GameInfo`: `GameInfoable` 프로토콜을 채택, 게임모드와 참여자 수를 갖고 있다.
- `CardGame`: `OutputViewPrintable` 프로토콜을 채택, SettingResult를 클로저로 반환한다.
  -  `GameInfo`, `Dealer`, `Player` 를 이용해 게임을 세팅하고 진행한다.
  - 딜러에게 카드의 개수가 충분한지 물어보고 충분하면 게임을 진행하고 충분하지 않으면 게임을 종료한다.
  - 참여자의 수만큼 Player 인스턴스를 생성한다. Dealer 인스턴스 또한 생성한다.
  - Player들에게 카드를 나눠준다. 
  - 게임을 run한다.

<br>

- `OutputView`
  - 게임의 `printSettingResult` 를 이용해 플레이어타입에 따라 "딜러", "참가자 # order" 와 카드를 출력한다.

<br>

## STEP 9-4. 승자 확인하기

<br>

### 📝 요구사항

- **원페어** = 가진 카드 중 두 카드 숫자가 같은 경우
- **투페어** = 가진 카드 중 두 카드 숫자가 같은 경우가 두 가지 이상
- **트리플** = 가진 카드 중 세 카드 숫자가 같은 경우
- **스트레이트** = 가진 카드 중 다섯 카드 숫자가 연속 번호인 경우 
- **포카드** = 가진 카드 중 네 카드 숫자가 같은 경우
- 페어가 없을 경우 또는 같은 핸즈인 경우 숫자가 높은 카드를 가진 사람이 우승한다.

<br>

### 🏗 전체 구조

- `InputView`: 메뉴 출력 및 입력
- `GameInputView`: 게임 메뉴 출력 및 카드게임 종류와 참여자 수를 입력받는다.

<br>

- `HandRanking`: hand(족보)들의 순위를 나타낸다. 
- `Card`
  - `Comparable`:  `Rank` 를 이용해 값을 비교한다.
  - `Hashable`: `rank` 가 유일하게 값을 비교하는 프로퍼티이므로 이를 이용해 hashable을 준수하게 한다.

- `Player`: `Playerable` 프로토콜을 채택, 자신의 순서와 카드들을 갖고 있다.
  - `Playerable` 프로토콜
  - 딜러가 나눠주는 카드를 `take()` 한다.
- `Dealer`: `Player` 를 상속받는다.  `Dealerable` 프로토콜을 채택, 카드덱을 갖고 있다.
  - `Dealerable` 프로토콜
  - 플레이어에게 카드를 `deal()` 나눠준다.
  - 필요한 카드 수에 대해 덱에 카드가 충분한지 확인한다.
  - 덱을 관리한다.
- `PlayersInfo`: [Player] 정보를 갖고 있다. 
  - 플레이어들을 생성한다.
  - 플레이어들에게 카드를 나눠준다.
  - 세팅 결과를 클로저를 통해 넘겨준다.
  - 승자를 클로저를 통해 넘겨준다. 
  - 클로저를 이용했기 때문에 외부에서는 내부의 정보를 알 필요가 없다.
- `CardsInfo`: [Card] 정보를 갖고 있다. 
  - cards의 rank를 세기 위해 딕셔너리 타입에 reduce 한다. [Card: Int]
  - [Card: HandRanking] 타입의 `Hands` 를 만든다.
  - `HandDecider`를 이용해 핸드를 생성한다.
  -  `bestHand` 를 계산한다. 
  -  `HandRanking` 의 값을 이용해 `Comparable` 을 준수한다.
    - Hands 중 HandRankig이 큰 값
    - HandRanking이 같다면 Card가 큰 값
- `HandDecider`: 카드와 중복개수를 이용해 `HandRanking` 을 생성한다. 
  - 생성된 HandRanking을 이용해 투페어와 스트레이트를 판단한다.
    - 투페어: 원페어 * 2
    - 스트레이트: highcard, 숫자 5개 연속
- `GameInfo`: `GameInfoable` 프로토콜을 채택, 게임모드와 참여자 수를 갖고 있다.
- `CardGame`: `OutputViewPrintable` 프로토콜을 채택, SettingResult를 클로저로 반환한다.
  - `GameInfo`, `Dealer`, `Player` 를 이용해 게임을 세팅하고 진행한다.
  - 딜러에게 카드의 개수가 충분한지 물어보고 충분하면 게임을 진행하고 충분하지 않으면 게임을 종료한다.
  - 참여자의 수만큼 Player 인스턴스를 생성한다. Dealer 인스턴스 또한 생성한다.
  - Player들에게 카드를 나눠준다. 
  - 게임을 run한다.

<br>

- `OutputView`
  - 게임의 `printSettingResult` 를 이용해 플레이어타입에 따라 "딜러", "참가자 # order" 와 카드를 출력한다.
  - 게임의 `printWinner` 를 이용해 플레이어 타입에 따라 ""게임의 승자는 "딜러", "참가자 # order"" 문구를 출력한다.
  - 클로저를 위한 `playersAndCardsForm` 과 `winnerForm` 이 있다. 



#### Comparable한 Type들

- Card의 `Rank`
- `Card`
- `CardsInfo`
- `HandRanking`
- `Player`

`Rank`를 이용해 `Card`의 대소를 비교한다.

플레이어 `cards`의 `besthand`를 이용해 `CardsInfo` 간의 대소를 비교한다.

`Player`가 가진 `CardsInfo`를 이용해 플레이어들 간의 대소를 비교한다. (winner 결정)

`HandRanking`의 ` rawValue`를 이용해 대소를 비교한다. (Hand 우선 순위)





### ⛑ Test

- `HandTests`: HandRanking이 잘 생성되는가?
- `CardGameTests`: winner가 제대로 나오는가?
  - Hand가 높은 플레이어 선택
  - Hand가 같을 때 Card Rank가 높은 플레이어 선택