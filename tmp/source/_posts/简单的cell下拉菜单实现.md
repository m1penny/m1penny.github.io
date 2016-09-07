---
title: 简单的cell下拉菜单实现
date: 2016-05-24 15:41:57
tags:
---
![](http://o7awmhryr.bkt.clouddn.com/2016-05-24%2016_00_38.gif)
实现思路：
点击cell按钮回调，判断是否下拉
1.若下拉
将所有cell置为关闭状态，若有展开的项，删除掉相关的数据，删掉相应的行，展开点击的项，记录点击的行数
2.若收回
根据记录的行数删除数据，收回点击的项
<!-- more -->
```ios
//展开要展开的一栏
((PullCell *)cell).isOpen = YES;
[((PullCell *)cell).pullBtn setTitle:@"点我合上" forState:UIControlStateNormal];
NSIndexPath *clickIndex = [tableView indexPathForCell:cell];
//记录所选行数
self.row                = clickIndex.row;
CustomModel *insertModel  = [[CustomModel alloc]init];
insertModel.type          = 2;
//插入并刷新那一行，用type来区分
[self.arr insertObject:insertModel atIndex:self.row + 1];
NSIndexPath *path       = [NSIndexPath indexPathForRow:self.row + 1 inSection:0];
NSArray *indexPaths     = @[path];
[tableView beginUpdates];
[tableView insertRowsAtIndexPaths:indexPaths withRowAnimation:UITableViewRowAnimationFade];
[tableView endUpdates];
[tableView scrollToRowAtIndexPath:path atScrollPosition:UITableViewScrollPositionNone animated:YES];
```
```ios
//删除下拉行，将所选行数置为默认9999
NSIndexPath *pullCellIndex = [NSIndexPath indexPathForRow:self.row + 1 inSection:0];
UITableViewCell *checkCell = [tableView cellForRowAtIndexPath:pullCellIndex];
if ([checkCell isKindOfClass:[DownCell class]]) {
	[self.arr removeObjectAtIndex:self.row + 1];
	NSIndexPath *path = [NSIndexPath indexPathForRow:self.row + 1 inSection:0];
	NSArray *indexPaths = @[path];
	[tableView beginUpdates];
	[tableView deleteRowsAtIndexPaths:indexPaths withRowAnimation:UITableViewRowAnimationFade];
	[tableView endUpdates];
}
self.row = 9999;
```
Demo地址：<https://github.com/m1penny/PullCellTest/>
Swift版本：<https://github.com/m1penny/PullCellTestSwift/>