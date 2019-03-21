# Custom SmartTouchPad with the plist located at

`IOKitPersonalities->Smart-Pad->Preferences`

## Tow fingers single tap
- 2FingersTapAction: 21
- Acts as right click button action by default. It can configured to other actions by editing plist with supported gesture actions value.

## Three fingers swipe
### up
- 3FingerSwiperUpAction: 4
- Mission Control

#### Ps: It do not work with value 3 or 4 >>>3FingerSwiperUpAction: 1 App Switch

### down
- 3FingerSwiperDownAction: 2
- App close


## Four fingers swipe
### up
- 4FingerSwiperUpAction: 10
- Toggle Full Screen Switch

### down
- Nothing Change
- Minimize app

### left
- 4FingerSwiperLeftAction: 6
- Left Space/Full Screen apps switch

### right
- 4FingerSwiperRightAction: 7
- Right Space/Full Screen apps switch

## Scrolling
- Scrolling->2FScroll->2FContScrollingMaxDelta
- Synaptics:15
