### 电子书阅读器如何分页

``` Swift
    func paginateBookContent(_ attributedText: NSAttributedString, pageSize: CGSize) -> [UITextView] {
        // 1. 总文本
        let textStorage = NSTextStorage(attributedString: attributedText)
        
        // 2. 公用布局器
        let layoutManager = NSLayoutManager()
        textStorage.addLayoutManager(layoutManager)
        
        var textViews: [UITextView] = []
        var index = 0

        while index < layoutManager.numberOfGlyphs {
            // 3. 分页容器
            let textContainer = NSTextContainer(size: pageSize)
            textContainer.lineFragmentPadding = 8
            layoutManager.addTextContainer(textContainer)
            
            // 4. 配套 UITextView
            let textView = UITextView(frame: CGRect(origin: .init(x: 0, y: WallUI.safeAreaTop), size: pageSize), textContainer: textContainer)
            textView.isEditable = false
            textView.isScrollEnabled = false
            textView.backgroundColor = .clear
            textView.contentInsetAdjustmentBehavior = .never
            
            textViews.append(textView)
            
            // 5. 判断是否到最后一页
            let range = layoutManager.glyphRange(for: textContainer)
            index = NSMaxRange(range)
        }

        return textViews
    }
```
