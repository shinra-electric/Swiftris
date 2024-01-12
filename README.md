# Swiftris
Swiftris is a "Falling Blocks" clone written in Swift using UIKit.
The original code was by [Bloc](https://github.com/Bloc/swiftris). However it was originally written in `Swift 3` which modern versions of Xcode are not able to build.

The fork by [hiddenviewer](https://github.com/hiddenviewer/Swiftris) was written in `Swift 4.1`. This version is based on that fork, and I was able to use Xcode to migrate it to use `Xcode 15` and `Swift 5`.


# UIKit Architecture
- ViewController.swift
- Swiftris.swift
- GameView.swift  
	- GameBoard.swift
	- GameScore.swift
	- NextBrick.swift
	- Brick.swift
- GameTimer.swift
- SoundManager.swift


## GameBoard
- The GameBoard is 22 rows by 10 columns, Two-dimensional array of UIColor. 
  
```  
class GameBoard: UIView {
	static let rows = 22
    static let cols = 10
    
    ...
	var board = [[UIColor]]()
	...
}
  
```  




## Bricks
- 7 different types of bricks. I, J, L, T, Z, S, O.
- Each brick has a unique colour.

```  
enum BrickType {
    case I(UIColor)
    case J(UIColor)
    case L(UIColor)
    case T(UIColor)
    case Z(UIColor)
    case S(UIColor)
    case O(UIColor)
}  
  
class Brick: NSObject {
    
	...
    static var bricks = [
        BrickType.I(UIColor(red:0.40, green:0.64, blue:0.93, alpha:1.0)),
        BrickType.J(UIColor(red:0.31, green:0.42, blue:0.80, alpha:1.0)),
        BrickType.L(UIColor(red:0.81, green:0.47, blue:0.19, alpha:1.0)),
        BrickType.T(UIColor(red:0.67, green:0.45, blue:0.78, alpha:1.0)),
        BrickType.Z(UIColor(red:0.80, green:0.31, blue:0.38, alpha:1.0)),
        BrickType.S(UIColor(red:0.61, green:0.75, blue:0.31, alpha:1.0)),
        BrickType.O(UIColor(red:0.88, green:0.69, blue:0.25, alpha:1.0))
    ]
	...    
}
  
```  

  
  
## Swiftris
- Game logic and Interaction.

```    
class Swiftris: NSObject {

	...
	
 	// Move the brick to the left, right and Rotate the brick.
    func touch(touch:UITouch!) {
        guard self.gameState == GameState.PLAY else { return }
        guard let curretBrick = self.gameView.gameBoard.currentBrick else { return }
        
        let p = touch.locationInView(self.gameView.gameBoard)
        
        let half = self.gameView.gameBoard.centerX
        
        let top  = curretBrick.top()
        let topY = CGFloat( (Int(top.y) + curretBrick.ty) * GameBoard.brickSize )

        if p.y > topY  {
            if p.x > half {
                self.gameView.gameBoard.updateX(1)
            } else if p.x < half {
                self.gameView.gameBoard.updateX(-1)
            }
        } else   {
            self.gameView.gameBoard.rotateBrick()
        }
    }
	
	// Drop the brick.
 	func longPressed(longpressGesture:UILongPressGestureRecognizer!) {
        if self.gameState == GameState.PLAY {

            if longpressGesture.state == UIGestureRecognizerState.Began {
                self.gameView.gameBoard.dropBrick()
            }
        }
    }

}
  
```  




## GameScore    
- Game score is depending on number of lines are cleared. 10, 30, 60, 100 points.

```    

class GameScore: UIView {

	...
    var scores = [0, 10, 30, 60, 100]  
    
    func lineClear(noti:NSNotification!) {
        if let userInfo = noti.userInfo as? [String:NSNumber] {
            if let lineCount = userInfo["lineCount"] {
                self.lineClearCount += lineCount.integerValue
                self.gameScore += self.scores[lineCount.integerValue]
                self.update()
            }
        }
    }
    
    ...
}
    
  
```  

  
## NextBrick  
- You can see up to the next three bricks in advance
  
  
```    
class Brick: NSObject {
	...
	static var nextBricks = [Brick]()
    static var nextBrickCount = 3
    
    // use the first brick from next brick queue and fill three.
    static func generate() -> Brick! {
        while self.nextBricks.count < self.nextBrickCount {
            self.nextBricks.append(self.newBrick())
        }
        let brick = self.nextBricks.removeAtIndex(0)
        self.nextBricks.append(self.newBrick())
        return brick
    }
    ...

}
  
```  


## SoundManager   
- Provides some basic sound effects.
- Background music, falling brick sound, game over sound.
  
```    
class SoundManager: NSObject {
   
    var bgmPlayer:AVAudioPlayer?
    var effectPlayer:AVAudioPlayer?
    var gameOverPlayer:AVAudioPlayer?
    ...
}
  
```  


# License
Swiftris is released under the MIT license. See LICENSE for details.


