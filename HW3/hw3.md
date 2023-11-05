<h1>HW 3</h1>

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

struct ImageWithLabel: View {
    
    let imgName: String
    let width: CGFloat
    let height: CGFloat
    
    var body: some View {
        ZStack {
            Image(imgName)
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(width: width, height: height)
            Text(imgName)
                .font(.system(size: 18))
                .foregroundColor(.white)
                .background(.black)
        }
    }
}

struct FrameByFrameView: View {
    
    @State var currentFrameName: String = ""
    
    var framePrefix = ""
    
    let startFrame: Int
    let endFrame: Int
    
    
    init(currentFrameName: String, startFrame: Int, endFrame: Int) {
        self.currentFrameName = "\(currentFrameName)\(0)"
        self.framePrefix = currentFrameName
        self.startFrame = startFrame
        self.endFrame = endFrame
    }
    
    var body: some View {
        Image(currentFrameName)
            .resizable()
            .onAppear(perform: timerUpdate)
    }
    
    func timerUpdate() {
        
        var currentIndex: Int = 0
        
        _ = Timer.scheduledTimer(withTimeInterval: 0.08, repeats: true) { (Timer) in
            currentFrameName = "\(framePrefix)\(currentIndex)"
            currentIndex = (currentIndex + 1) % (endFrame + 1)
        }
    }
}

struct ContentView: View {
    
    @State var rotationDegrees = 0.0
    
    let multiColStack1ImgLabels = [
        ["ArmorBoot",
         "BodyArmor",
         "Gauntlet",],
        ["GreekHelmet",
         "KnightShield",
         "SpartanHelmet",],
        ["armor",
         "armor-2",
         "bulletproof-vest",],
    ]
    
    let timer = Timer.publish(every: 0.2, on: .main, in: .common).autoconnect()
    
    
    var body: some View {
        ZStack {
            FrameByFrameView(currentFrameName: "fire-", startFrame: 0, endFrame: 120)
                .frame(width: .infinity, height: .infinity)
                .opacity(0.9)
            
            VStack {
                ForEach(multiColStack1ImgLabels, id: \.self) { multiColStack1ImgLabel in
                    HStack {
                        ForEach(multiColStack1ImgLabel, id: \.self) { imgLabel in
                            ImageWithLabel(imgName: imgLabel, width: 100, height: 100)
                                .rotation3DEffect(.degrees(rotationDegrees), axis: (x: 0, y: 1, z: 0))
                                .onReceive(timer, perform: { _ in
                                    rotationDegrees += 2.5
                                    // Exactly 90 degrees will make the image disappear.
                                    if (rotationDegrees == 90) {
                                        rotationDegrees = 92.5
                                    }
                                })
                        }
                    }
                }
            }
            
        }
        .padding()
    }
}

```