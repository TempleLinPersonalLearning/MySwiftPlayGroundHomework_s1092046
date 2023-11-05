<h1>HW 2</h1>

```swift

import Foundation
import SwiftUI

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

struct SelfRotatingImgBtn: View {
    
    @State var targetDegrees = 10.0
    @Binding var show: Bool
    
    
    let imgName: String
    let rotateSpeed: Double
    let onClick: (String) -> Void
    let size: CGFloat
    
    ///
    ///- Parameters:
    /// - rotate:
    ///     Use "_" in front, in order to assign value to Binding variable.
    init(_ targetDegrees: Double = 10.0, size: CGFloat = 100, imgName: String, show: Binding<Bool>, rotateSpeed: Double, onClick: @escaping (String) -> Void) {
        self.targetDegrees = targetDegrees
        self.size = size
        self.imgName = imgName
        self._show = show
        self.rotateSpeed = rotateSpeed
        self.onClick = onClick
    }
    
    
    
    var body: some View {
        Button {
            onClick(self.imgName)
        } label: {
            if (show) {
                Image(imgName)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: size)
                    .rotationEffect(.degrees(targetDegrees))
                    .onAppear {
                        withAnimation(.easeInOut(duration: rotateSpeed)
                            .repeatForever(autoreverses:true)) {
                                targetDegrees = -targetDegrees
                            }
                        
                    }
            }
        }
    }
}

struct ColorfulText: View {
    
    private var text:String
    private var width: CGFloat
    private var height: CGFloat
    private var fontSize: CGFloat
    
    public init(text: String, width: CGFloat, height: CGFloat, fontSize: CGFloat) {
        self.text = text
        self.width = width
        self.height = height
        self.fontSize = fontSize
    }
    
    var body: some View {
        LinearGradient(
            colors: [.red, .blue, .green, .yellow],
            startPoint: .leading,
            endPoint: .trailing
        )
        .frame(width: self.width, height: self.height)
        .mask(
            VStack (content: {
                Text(text)
                    .font(Font.system(size: self.fontSize, weight: .bold))
                    .multilineTextAlignment(.center)
            })
        )
    }
}

enum AttackType {
    case rock, paper, scissors
}

enum BattleResult {
    case userWon
    case botWon
    case tie
    case ongoing
}

enum CurrentPageMode {
    case landingPage, gameplayPage
}

struct ContentView: View {
    
    @State private var currentPageMode = CurrentPageMode.landingPage
    
    var body: some View {
        switch(currentPageMode) {
        case .landingPage:
            LandingPageView(
                onConfirmCallback: self.landingPageConfirmed
            )
        case .gameplayPage:
            GameplayPageView()
        }
    }
    
    func landingPageConfirmed() {
        currentPageMode = CurrentPageMode.gameplayPage
    }
}

struct GameplayPageView: View {
    
    @State var botCurrentAttackTypeIndex = 0
    @State var userSelectedAttackTypeIndex = -1
    @State var showUserBtns = [true, true, true]
    @State var showBotBtn = true
    @State var currentBottomHint = 0
    @State var battleResult: BattleResult = .ongoing
    
    let allAttackTypes: [String] = ["RockPaperScissors_Rock", "RockPaperScissors_Paper", "RockPaperScissors_Scissors"]
    
    let allBottomHints = ["（請三選一出擊！）"]
    
    let timer = Timer.publish(every: 0.5, on: .main, in: .common).autoconnect()
    
    let userWinningCombinations: [[Int]] = [[0, 2], [1, 0], [2, 1]]
    
    var body: some View {
        VStack {
            battleResultDisplay()
            
            SelfRotatingImgBtn(size: 180, imgName: allAttackTypes[botCurrentAttackTypeIndex], show: $showBotBtn, rotateSpeed: 1.0) { value in }
                .onReceive(timer, perform: { _ in
                    if userSelectedAttackTypeIndex != -1 {
                        return
                    }
                    botCurrentAttackTypeIndex = (botCurrentAttackTypeIndex + 1) % allAttackTypes.count
                })
                .padding(.bottom, 20)
            
            HStack {
                SelfRotatingImgBtn(imgName: allAttackTypes[0], show: $showUserBtns[0], rotateSpeed: 1.0, onClick: onPlayerSelectedAttackBtn)
                SelfRotatingImgBtn(imgName: allAttackTypes[1], show: $showUserBtns[1], rotateSpeed: 1.5, onClick: onPlayerSelectedAttackBtn)
                SelfRotatingImgBtn(imgName: allAttackTypes[2], show: $showUserBtns[2], rotateSpeed: 1.2, onClick: onPlayerSelectedAttackBtn)
            }
            .padding(.bottom, 20)
            
            battleInfoDisplay()
        }
    }
    
    
    func onPlayerSelectedAttackBtn(_ attackType: String) -> Void {
        if userSelectedAttackTypeIndex != -1 {
            return
        }
        
        print("User attack: " + attackType)
        
        
        let randomInt = Int.random(in: 0..<3)
        botCurrentAttackTypeIndex = randomInt
        
        let userSelectedIndex = allAttackTypes.firstIndex(of: attackType)
        userSelectedAttackTypeIndex = userSelectedIndex ?? -1
        
        for i in 0...2 {
            if i == userSelectedIndex {
                showUserBtns[i] = true
            } else {
                showUserBtns[i] = false
            }
        }
        
        self.battleResult = checkBattleResult()
    }
    
    func battleInfoDisplay() -> some View {
        Group {
            switch self.battleResult {
            case .ongoing:
                Text("請出擊，剪刀..石頭...")
                    .font(Font.system(size: 23, weight: .bold))
                    .multilineTextAlignment(.center)
                    .padding(.bottom, 20)
            case .botWon, .userWon, .tie:
                HStack {
                    Text("再來一場？")
                        .font(Font.system(size: 23, weight: .bold))
                        .multilineTextAlignment(.center)
                    
                    Button(action: restartGame, label: {
                        Text("再來一場")
                            .foregroundColor(.white)
                            .padding()
                            .background(Color.blue)
                            .cornerRadius(8)
                    })
                }
            }
        }
    }
    
    func battleResultDisplay() -> some View {
        Group {
            switch self.battleResult {
            case .userWon:
                ColorfulText(text: "使用者獲勝！", width: .infinity, height: 80, fontSize: 46)
            case .botWon:
                ColorfulText(text: "電腦獲勝！", width: .infinity, height: 80, fontSize: 46)
            case .tie:
                ColorfulText(text: "平手！", width: .infinity, height: 80, fontSize: 46)
            case .ongoing:
                EmptyView()
            }
        }
    }
    
    func checkBattleResult() -> BattleResult {
        print("Chcking battle result...")
        if userWinningCombinations.contains([userSelectedAttackTypeIndex, botCurrentAttackTypeIndex]) {
            return .userWon
        } else if userSelectedAttackTypeIndex == botCurrentAttackTypeIndex {
            return .tie
        }
        return .botWon
    }
    
    func restartGame() {
        battleResult = .ongoing
        userSelectedAttackTypeIndex = -1
        botCurrentAttackTypeIndex = 0
        for i in 0...2 {
            showUserBtns[i] = true
        }
    }
}

struct LandingPageView: View {
    
    @State private var confirmBtnIsRotating = 0.0
    
    var onConfirmCallback: () -> Void
    
    var body: some View {
        VStack {
            ColorfulText(text: "剪刀石頭布", width: .infinity, height: 150, fontSize: 46)
            
            Text("準備好了嗎？按下按鈕！")
                .font(Font.system(size: 23, weight: .bold))
                .multilineTextAlignment(.center)
            
            Button {
                onConfirmCallback();
            } label: {
                Image("ConfirmBtn")
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: 80)
                    .rotationEffect(.degrees(confirmBtnIsRotating))
                    .onAppear {
                        withAnimation(.linear(duration: 3)
                            .repeatForever(autoreverses:false)) {
                                confirmBtnIsRotating = 360.0
                            }
                    }
            }
        }
        .padding()
    }
}

```
![HW2 Recording](./HW2_Recording.gif)
