<h1>HW 1</h1>

```swift

import SwiftUI

@main
struct MyApp: App {
    @AppStorage("isDarkMode") private var isDarkMode = false
    
    var body: some Scene {
        WindowGroup {
            ContentView(toggleDarkMode: {
                isDarkMode = !isDarkMode
            })
            .preferredColorScheme(isDarkMode ? .dark : .light)
        }
    }
}

extension String {
    subscript(offset: Int) -> Character {
        self[index(startIndex, offsetBy: offset)]
    }
}

struct ContentView: View {
    
    @State private var beginningText = "";
    @State private var showDetail = false
    
    private let beginningFinishedText = "您好，請點選我的圖片，檢視我的自我介紹。"
    private let myName = "林天牧"
    private let selfIntroDetails = "元智大學資訊傳播學系科技組 S1092046"
    private let selfQuote = "蘋果不是一天造成的"
    
    var toggleDarkMode: () -> Void
    
    var body: some View {
        VStack(spacing: 30) {
            Button (action: {
                if (!showDetail) {
                    if (beginningText != beginningFinishedText) {
                        return
                    }
                }
                withAnimation(.easeInOut(duration: 1.0)) {
                    showDetail.toggle()
                    toggleDarkMode()
                }
            }) {
                Image("SelfImage")
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: 200, height: 200)
                    .clipShape(Circle(), style: FillStyle())
                    .rotationEffect(.degrees(showDetail ? 360 : 0))
                    .scaleEffect(showDetail ? 1.2 : 1)
                    .padding()
            }
            
            showIntroContent()
        }
    }
    
    ///
    /// "AnyView": SwiftUI ships with a special view called AnyView, which can be used as a type erased wrapper to enable multiple view types to be returned from a single function or computed property, or to let us reference a view without having to know its underlying type.
    ///
    func showIntroContent() -> some View {
        if (showDetail) {
            return AnyView(
                VStack(spacing: 20) {
                    Text(myName)
                        .font(.system(size: 36))
                    HStack {
                        Image(systemName: "cloud.sun")
                        Text(selfIntroDetails)
                        Image(systemName: "cloud.sun")
                    }
                    Text(selfQuote)
                        .foregroundStyle(Color(.red))
                }
            )
        } else {
            return AnyView(
                Text(beginningText)
                    .onAppear(perform: {
                        typeWriter()
                    })
            )
        }
    }
    
    func typeWriter(at position: Int = 0) {
        if position == 0 {
            beginningText = ""
        }
        if position < beginningFinishedText.count {
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.1) {
                beginningText.append(beginningFinishedText[position])
                typeWriter(at: position + 1)
            }
        }
    }
}

```



https://github.com/TempleLinPersonalLearning/MySwiftPlayGroundHomework_s1092046/assets/78478355/94b5f4d3-2497-4cd1-9afe-9d6b55e81150


