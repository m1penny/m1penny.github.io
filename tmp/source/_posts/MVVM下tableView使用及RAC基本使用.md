---
title: MVVM下tableView使用及RAC基本使用
date: 2016-05-23 14:56:51
tags:
---
MVVM下，将tableView的协议及数据源放在ViewModel层；
网络请求也放在相应的ViewModel下。
<!-- more -->
控制器得到简化，如下：
```ios
- (void)viewDidLoad {
    [super viewDidLoad];
    
    _viewModel = [[CustomViewModel alloc]init];
    [_viewModel loadData];
    
    self.tableView.delegate = _viewModel;
    self.tableView.dataSource = _viewModel;
    
    @weakify(self);
    [RACObserve(_viewModel, arr) subscribeNext:^(id x) {
        
        @strongify(self);
        
        [self.tableView reloadData];
        
    }];
    
    // Do any additional setup after loading the view, typically from a nib.
}
```
RAC的基本使用：
1.绑定VM和存储数据的arr,arr变化，刷新tableView
```ios
@weakify(self);
    [RACObserve(_viewModel, arr) subscribeNext:^(id x) {
        
        @strongify(self);
        
        [self.tableView reloadData];
        
    }];
```
2.cell上按钮的点击事件，AlertView的点击事件
```ios
cell.rightBtn.rac_command = [[RACCommand alloc]initWithSignalBlock:^RACSignal *(id input) {
        NSLog(@"test");
        return [RACSignal empty];
    }];
cell.rightSecondBtn.rac_command = [[RACCommand alloc]initWithSignalBlock:^RACSignal *(id input) {
        UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"arcTest" message:nil delegate:self cancelButtonTitle:@"取消" otherButtonTitles:@"确定", nil];
        [alert show];
        [alert.rac_buttonClickedSignal subscribeNext:^(id x) {
            switch ([x intValue]) {
                case 0:
                    NSLog(@"取消");
                    break;
                case 1:
                    NSLog(@"确定");
                    break;
                default:
                    break;
            }
        }];
//        UIAlertController *alertC = [UIAlertController alertControllerWithTitle:@"arcTest" message:nil preferredStyle:UIAlertControllerStyleAlert];
//        UIAlertAction *cancelAction = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:^(UIAlertAction * _Nonnull action) {
//            
//        }];
//        [alertC addAction:cancelAction];
//        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
//            
//        }];
//        [alertC addAction:okAction];
        
        return [RACSignal empty];
    }];
```
3.遇到的坑

loadData中，对存储数据的数组赋值时，不能用_arr = xxx 的形式，因为self方法实际上是用了get和set方法间接调用，下划线方法是直接对变量操作。
```ios
[session GET:url parameters:nil progress:^(NSProgress * _Nonnull downloadProgress) {
    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        NSArray *arr = [CustomModel mj_objectArrayWithKeyValuesArray:responseObject];
        self.arr = [NSMutableArray arrayWithArray:arr];
        NSLog(@"%@",self.arr);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        if(error) {
            NSLog(@"%@", error);
        }
    }];
```
Demo地址：<https://github.com/m1penny/RACTest/>
Swift版本：<https://github.com/m1penny/RACTestSwift/>（用通知，闭包替代了RAC）