### 电子书阅读器如何分页

``` Swift
import UIKit
import SnapKit

class ChapterReaderViewController: UIPageViewController {
    
    lazy var textViews: [UITextView] = {
        let content = mockContent.map({ "\t" + $0 }).joined(separator: "\n")
        let paragraphStyle = NSMutableParagraphStyle()
        paragraphStyle.lineSpacing = 10.0
        let attr = NSAttributedString(string: content, attributes: [
            .font: UIFont.systemFont(ofSize: 18),
            .paragraphStyle: paragraphStyle
        ])
        
        let width = UIScreen.main.bounds.width
        let height = UIScreen.main.bounds.height - WallUI.safeAreaTop - WallUI.safeAreaBottom
        let textViews = paginateBookContent(attr, pageSize: CGSize(width: width, height: height))
        return textViews
    }()
    
    func getViewController(at index: Int) -> ContentPageViewController? {
        guard index >= 0 && index < textViews.count else {
            return nil // 索引越界
        }
        let contentVC = ContentPageViewController(pageIndex: index, textView: textViews[index])
        return contentVC
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        hidesBottomBarWhenPushed = true
        dataSource = self
        delegate = self
        
        if let initialViewController = getViewController(at: 0) {
            setViewControllers(
                [initialViewController],
                direction: .forward, // 初始显示方向
                animated: true,      // 带动画
                completion: nil
            )
        }
    }
    
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
    
    let mockContent: [String] = [
        "武道历四百年。",
        "夏国，江北行省，灵武市。",
        "叶家。",
        "“吃饭了，你还要在房间里面窝到什么时候？”",
        "门外，一道冷漠十着不耐烦的声音响起，吵醒了正在假寐的叶临渊。",
        "“看来，记忆中的事情并没有错。”",
        "叶临渊缓缓睁开眼睛，心中长叹一声。",
        "三年前，他穿越了。",
        "从前世的蓝星，穿越到了一个妖兽横行，星空出现裂缝的高武世界当中。",
        "并且，身份从一个刚刚毕业的学生，成为了江北省叶家的长子。",
        "父亲是一名大宗师级的武者，更是江北省镇魔军总兵，军部一星上将。",
        "在这个高武世界，立下过赫赫战功。",
        "因此，叶家在江北，乃至夏国都有着极高的地位。",
        "这样的身份，叶临渊可谓是妥妥的武二代，军二代。",
        "然而，面对这样一个足以欺男霸女的身份，叶临渊却怎么也高兴不起来。",
        "因为他穿越过来的时候，发现自己已经在前线的战场上了。",
        "和大多数人穿越后干的第一件事儿一样，叶临渊当即开始查看自己的记忆。",
        "这才弄清楚了事情的来龙去脉。",
        "当时，边关前线妖兽暴动，战况吃紧。",
        "因此，举国上下都下达了征兵的命令。",
        "只要是成为武者的人，都有几率被征兵！",
        "好巧不巧的是，这个征兵名单，同样送到了叶家，而叶临渊的弟弟叶白，正在其中。",
        "叶白作为叶家的武道天才，一直被父母当做继承人培养。",
        "如今突然被征兵去前线和妖兽搏杀，他们自然而然的放心不下。",
        "于是，就让身为哥哥的叶临渊替弟弟从军，前往前线战场。",
        "更让叶临渊不可思议的是，前身居然还答应了！",
        "要知道，之所以征兵门槛设立在正式武者。",
        "是因为成为武者之后，才有和妖兽对抗的能力，才有活下来的可能！",
        "然而，父母居然为了弟弟，让他这个哥哥去战场。",
        "当时的叶临渊脑子里面就一句话，弟弟的命是命，他这个哥哥的命就不是命了？",
        "心中虽然对素未谋面的父母好感全无，但叶临渊也没办法，因为当时已经在战场上了。",
        "逃跑的话，要背负一辈子逃兵的骂名，甚至被抓住还有可能丢了性命。",
        "无奈之下的叶临渊只能硬着头皮上了战场，不过，也算是因祸得福。",
        "叶临渊上了战场后同样绑定了一款系统，凭借着系统，他才能在战场上活下来。",
        "也因此，",
        "成为东部战区的一名大将。",
        "......",
        "叶临渊起身，打开门就看到了冷着脸站在门外的叶伶月。",
        "“爸妈今天为了你都回来了，看到你这个样子，他们要有多失望？”",
        "叶伶月一看见叶临渊，脸上的表情就多出来几分冷漠和嫌恶。",
        "在叶伶月的眼中叶临渊一直都是废物哥哥的形象，所以始终瞧不上他。",
        "因此，回来的这此天叶伶月也没有给他好脸色看。",
        "听见这番话，叶临渊的內心不由冷笑了一声。",
        "为了他全都回来了？",
        "要知道，他可是替弟弟从军了三年，在边关和妖兽异族在战场上厮杀了三年。",
        "回来都三天了，连父母的面都没见到。",
        "说的好像多关心自己一样。",
        "就如记忆中的一样，无论他再怎么努力，哪怕是他比弟弟优秀。",
        "父母就像是始终看不见一般。",
        "这也成为了前身心中的一个郁结，他百般努力，答应替弟弟从军，便是想要得到父母的认可。",
        "虽然叶临渊穿越过来，可这个郁结依然存在，导致叶临渊的武道路途越来越艰难。",
        "“你就是这么跟我说话的吗，在家里没有人教过你？”",
        "叶临渊面色平静，眸子深邃冰冷。",
        "“你...”",
        "叶伶月刚想反驳，对上叶临渊的犀利如刀的目光后，又硬生生的咽了下去。",
        "不知怎么地。",
        "她感觉下一刻，自己的精神力就要被烙印上什么可怕的背影。",
        "“懒得和你这种人多说，反正我已经来叫你了。”",
        "叶伶月冷着脸，转身离开。",
        "看着对方离开的背影，叶临渊叹了口气。",
        "说实在的，叶伶月对他的态度，叶临渊不怎么在意，因为他是一个穿越者，本来就对这家人没什么感情。",
        "若是父母也是如此，那他还是尽早离开，撇清关系为好，省得恶心自己。",
        "客厅，叶临渊来到这里的时候，桌子上面就已经摆满了丰盛的饭菜。",
        "主位上坐着一个身姿挺拔，面容刚毅的中年男人，而在他的旁边，则是坐着一个身着华丽的妇人。",
        "仅仅是看了一眼，一股记忆就在脑海中冒了出来。",
        "自己的父亲，江北省镇魔军总兵，叶泰，以及自己的母亲，江北省最大的基因药物研发公司负责人，苏蓉。",
        "“临渊，快来坐下吃饭。”",
        "见到叶临渊出来，苏蓉脸上露出一抹笑容，连忙说道。",
        "叶临渊没有说话，默默走到饭桌前，坐了下来。",
        "在叶临渊身边，除了叶伶月之外。",
        "还有一个年纪约莫十八九岁的少年，毫无疑问，这就是叶临渊的弟弟，叶白。",
        "让叶临渊意外的是，叶白同样和叶伶月一样，都是冷着脸对待他。",
        "甚至连一声问候都没有。",
        "这让叶临渊心中多少有此不爽了，老子替你当兵三年，回来你就这么对我？",
        "不过，还没等叶临渊作出反应，一旁的叶泰便开口询问：“这次回来，有什么打算？”",
        "“待一段时间，再回军区。”",
        "叶临渊淡淡的回应。",
        "“军区你就不要去了，军队那边我回去说，既然回来了就在家里好好休息。”",
        "叶泰沉吟片刻，方才开口道。",
        "“为什么？”",
        "叶临渊皱了皱眉，有此不解的问。",
        "“你弟弟马上参加武考，按照现在武考政策，有战功之人武考能获得额外加分。”",
        "“另外，我打算提拔你弟弟为军部少校，这需要一此功勋支撑，以后也好接手我的位置。”",
        "......"
      ]
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        navigationController?.setNavigationBarHidden(true, animated: false)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        navigationController?.setNavigationBarHidden(false, animated: false)
    }
}


extension ChapterReaderViewController: UIPageViewControllerDataSource {

    // 返回前一页的视图控制器
    func pageViewController(_ pageViewController: UIPageViewController, viewControllerBefore viewController: UIViewController) -> UIViewController? {
        guard let currentVC = viewController as? ContentPageViewController else {
            return nil
        }
        let previousIndex = currentVC.pageIndex - 1
        return getViewController(at: previousIndex)
    }

    // 返回后一页的视图控制器
    func pageViewController(_ pageViewController: UIPageViewController, viewControllerAfter viewController: UIViewController) -> UIViewController? {
        guard let currentVC = viewController as? ContentPageViewController else {
            return nil
        }
        let nextIndex = currentVC.pageIndex + 1
        return getViewController(at: nextIndex)
    }

    // MARK: - 可选：提供页面指示器 (在 .scroll 模式下显示的小圆点)
    // 如果你设置了这两个方法，UIPageViewController 会自动在底部显示小圆点指示器

    func presentationCount(for pageViewController: UIPageViewController) -> Int {
        return textViews.count // 总页数
    }

    func presentationIndex(for pageViewController: UIPageViewController) -> Int {
        // 返回当前显示的页面的索引
        if let currentVC = pageViewController.viewControllers?.first as? ContentPageViewController {
            return currentVC.pageIndex
        }
        return 0 // 默认返回第一页
    }
}

// MARK: - UIPageViewControllerDelegate (可选，用于监听翻页事件)

extension ChapterReaderViewController: UIPageViewControllerDelegate {

    // 当用户开始手势翻页时调用
    func pageViewController(_ pageViewController: UIPageViewController, willTransitionTo pendingViewControllers: [UIViewController]) {
        // print("即将翻页到: \((pendingViewControllers.first as? ContentPageViewController)?.pageIndex ?? -1)")
    }

    // 翻页动画完成时调用
    func pageViewController(_ pageViewController: UIPageViewController, didFinishAnimating finished: Bool, previousViewControllers: [UIViewController], transitionCompleted completed: Bool) {
        if completed { // 确保动画已完成且转换成功
            if let currentVC = pageViewController.viewControllers?.first as? ContentPageViewController {
                print("当前页面索引: \(currentVC.pageIndex)")
                // 在这里可以更新外部的页码指示器等 UI
            }
        }
    }
}
```
