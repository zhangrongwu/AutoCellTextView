# AutoCellTextView
面试题：UITableview中添加UITextView,不去处理textView文字让tableview高度自动添加
原理：
1、使用UITableView+FDTemplateLayoutCell和Masonry结合自动布局扩展高度
2、通过cell中的textView的内容变化代理让tableView重新处理高度

主要代码实现
```
// cell中textView内容代理到控制器
- (void)cellTextViewDidChange:(UITextView *)textView {
    CGSize maxSize = CGSizeMake(textView.bounds.size.width, CGFLOAT_MAX);
    CGSize newSize = [textView sizeThatFits:maxSize];
    CGRect bounds = textView.bounds;
    bounds.size = newSize;
    textView.bounds = bounds;
    [self.tableView beginUpdates];
    [self.tableView endUpdates];
}
```

```
// tableViewCell高度处理
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    AutoTableViewCell *cell = [tableView cellForRowAtIndexPath:indexPath];
    CGRect rect = cell.textview.bounds;
    if (rect.size.height != 0) {
        return rect.size.height;
    }
    return [tableView fd_heightForCellWithIdentifier:identifier cacheByIndexPath:indexPath configuration:^(AutoTableViewCell *cell) {
        cell.data = @{};
    }];
}
```


```
// FDTemplateLayoutCell和Masonry结合自动布局扩展高度
   [self.textview mas_remakeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(self.contentView.mas_top);
        make.left.equalTo(self.contentView.mas_left);
        make.right.equalTo(self.contentView.mas_right);
        make.height.lessThanOrEqualTo(@300);
        make.height.greaterThanOrEqualTo(@150);
        make.bottom.equalTo(self.contentView.mas_bottom);
    }];
```

            
