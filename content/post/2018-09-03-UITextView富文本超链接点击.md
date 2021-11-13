---
layout: post

title: UITextView富文本超链接点击

date: 2018-09-03 18:40:00

categories: ["iOS"]

tags: ["UITextView"]

mathjax: true
---

用`UITextView`显示富文本的时候，如果富文本中有`超链接`，有时候需要单独处理这个链接的点击。

可以继承`UITextView`重写`hitTest`方法，拦截点击的内容，然后进行跳转。

首先重写`canPerformAction`方法，取消`UITextView`的一些弹出菜单的响应。

```objectivec
- (BOOL)canPerformAction:(SEL)action withSender:(id)sender {
    //一旦双击，里面禁用。取消第一响应者，设置不可交互。
    [self resignFirstResponder];
//    self.userInteractionEnabled = NO;
    if ([UIMenuController sharedMenuController]) {
        [UIMenuController sharedMenuController].menuVisible = NO;
    }
    return NO;
}
```

重写`hitTest`方法，拦截点击的字符串

```objectivec
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    
    // 获取点击的字母的位置
    NSInteger characterIndex = [self.layoutManager characterIndexForPoint:point inTextContainer:self.textContainer fractionOfDistanceBetweenInsertionPoints:NULL];
    
    // 获取单词的范围。range 由起始位置和长度构成。
    NSRange range = [self getWordRange:characterIndex];
    
    NSAttributedString *clickedString = [self.attributedText attributedSubstringFromRange:range];
    __block BOOL isLink = NO;
    [clickedString enumerateAttributesInRange:NSMakeRange(0, clickedString.length) options:1 usingBlock:^(NSDictionary<NSAttributedStringKey,id> * _Nonnull attrs, NSRange range, BOOL * _Nonnull stop) {
        [attrs enumerateKeysAndObjectsUsingBlock:^(NSAttributedStringKey  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
            if ([key isEqualToString:@"NSLink"]) {
                if (((NSURL *)obj).absoluteString.length > 0) {

                    isLink = YES;
                    *stop = YES;
                }
            }
        }];
    }];
    if (isLink) {
        return self;
    }
    return self.superview;
}
```

过滤点击的字符

```objectivec
- (NSRange)getWordRange:(NSInteger)characterIndex {
    NSInteger left = characterIndex - 1;
    NSInteger right = characterIndex + 1;
    NSInteger length = 0;
    NSString *string = self.attributedText.string;
    
    // 往左遍历直到空格
    while (left >=0) {
        NSString *s=[string substringWithRange:NSMakeRange(left, 1)];
        
        if ([self isLetter:s]) {
            left --;
        } else {
            break;
        }
    }
    
    // 往右遍历直到空格
    while (right < self.text.length) {
        NSString *s=[string substringWithRange:NSMakeRange(right, 1)];
        
        if ([self isLetter:s]) {
            right ++;
        } else {
            break;
        }
    }
    
    // 此时 left 和 right 都指向空格
    left ++;
    right --;
    length = right - left + 1;
    NSRange range = NSMakeRange(left, length);
    
    return range;
}
```

判断是否字母

```objectivec
- (BOOL)isLetter:(NSString *)str {
    char letter = [str characterAtIndex:0];

    if ((letter >= 'a' && letter <='z') || (letter >= 'A' && letter <= 'Z')) {

        return YES;
    }

    return NO;
}

```



**因为我们的需求是做一个评论内容列表，`UITextView`是放在`UITableviewCell`中，如果是超链接，就会用`Safari`打开，如果不是，对cell的点击，依旧是cell响应。**