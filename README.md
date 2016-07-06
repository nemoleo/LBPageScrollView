
#项目说明
创星子分支，电视台合作直播类项目。

##需要注意的点
__写完（改完Bug）代码一定要自己复测。不能老是同一个问题被打回来几次。__

__Push之前一定要先跑下代码，看看能不能跑起来。__    

__上架之前一定要确认2个事情， 1.RElEASE_VER宏有没有开，2.屏蔽开关有没有开。__  

__打包（发包测试，上架）之前，先跑起来看看，花几分钟都点一点。__

____

##编码规范  

__为了不影响阅读，一个类的代码行数尽量不要超过300行;  
禁止使用NSLog，使用SWLogD(V,E,W)代替。（log分级：worning，exception，debug，verbose）  
一个方法尽量不要超过30行。有超过的在重构的时候想办法分解。  
继承一定不要超过3代，多用组合替代继承。（类别）  
为了保证代码整齐，所有间隔占一行就好。  
宏定义统一放到Macros.h 里面。  
不要使用宏、static做常量定义，定义常量请使用 {类型} const {常量名};  
小写驼峰体：camelCase  
大写驼峰体：CamelCase__
  
1.	头文件.h
	-	每个.h文件最开始处用简短文字说明一下该类的功能逻辑。  
	最好做到每个方法都写上简短的注释（不强求）  

			//
			//  ManagerObj.h
			//  EZTV
			//
			//  Created by Sunni on 15/6/24.
			//  Copyright (c) 2015年 Joygo. All rights reserved.
			//
			// 简单说明两三句
			//
	-	引用（#import）其它类时，如果.h中用不到此类则在.m中头部引用（保证被最少的类引用）
	-	枚举定义写在 #import 后面，如果外部其他类用不到此枚举则定义在.m里面。
	-	常量如果外部需要访问则加上extern在头文件里申明，否则只写在.m里面。

			(.h声明)
			extern NSString* const audienceBlockReuseIdentifier;
			
			(.m声明，赋值)
			NSString* const audienceBlockReuseIdentifier = @"audienceBlockReuseIdentifier";
	-	类继承接口过多的时候接口之间要换行，系统预定义的接口写在前面，自定义的写在后面。同一个类的接口可以放在一行。(在此顺便约定所有数组，字典的书写。参照下面继承接口列表。)

			@interface LiveListVC : UIViewController
			<UITableViewDelegate, UITableViewDataSource,
			UIAlertViewDelegate,
			PopNavigationDelegate,
			HPGrowingTextViewDelegate,
			UMSocialUIDelegate,
			MessageClientDelegate,
			LianMaiViewDelegate,
			AnchorProfileViewDelegate,>
	-	成员定义声明
		-	接口，类名，类别名 都用大写驼峰体书写。
		-	属性名，方法名使用小写驼峰体。
		-	属性定义写在方法之前，属性块与方法块之间空一行。
		-	属性（方法）过多时，同类功能（模块）的属性（方法）放在一起，功能（模块）间隔一行。（参照NSObject 头文件定义。）  
		每个功能块可作简单说明，关键的或特殊的变量或方法单独作说明。注释放于变量声明上一行。
		-	@protocol、@interface(@implementation) 和#import 之间空一行。 和属性定义块之间也空一行。(具体空格换行书写参照下面示例)
		
				#import "Model.h"
				
				extern NSString* const keyOfClassObject;
				
				@protocol ManagerObjDelegate <NSObject>
				
				//Model Description
				@property (nonatomic,weak) Model *model;
				@property (nonatomic,weak) UINavigationController *navigationController;
				
				- (void)managerObjectCreated;
				
				@interface ManagerObj : NSObject
				
				@property (nonatomic,strong) Model *model;
				@property (nonatomic,weak) id<ManagerObjDelegate> delegate;
				
				- (void)initWithModel:(Model *)model;
				- (Model *)transformedModel;
				
				@end
				
				@implementation
			  
2.	implemetion文件.m 
	-	所有成员都以属性方式声明。赋值，取值若有逻辑加工。必须使用setter，getter。
	-	所有SubView的初始化写在Getter里面。frame使用Masnory定义，放在一个方法里面。在viewDidLoad最末尾调用。
	-	所有区块开头用#pragma mark 注释区域。区块间隔1行。方法之间隔1行。
	-	以 ViewController.m 为例，从上至下依次是  
	<常量的定义>  
	<继承接口声明>  
	<属性定义>  
	<重载的父类方法>(ViewDidLoad,ViewWillAppear...)  
	<Delegate方法实现>(同一个Delegate的方法一定要写在一起 然后一定在前面写上#pragma mark - Delegate正确的名称 方便读你代码的人可以点进去看这个delegate的定义)  
	<事件响应方法>(所有的Button，Gesture的响应方法)  
	<私有方法>  
	<Getter跟Setter>  
	
			#import "LiveListVC.h"
			
			NSString* const constValue = @"constValDefault";
			
			@interface LiveListVC()
			<UITableViewDelegate, UITableViewDataSource,
			UIAlertViewDelegate,
			PopNavigationDelegate,
			HPGrowingTextViewDelegate,
			UMSocialUIDelegate,
			MessageClientDelegate,
			LianMaiViewDelegate,
			AnchorProfileViewDelegate,>
			
			@property (nonatomic,strong) UITableView *tblLiveList;
			@property (nonatomic,strong) NSArray *arrayLive;
			
			@end
			
			@implementation
			
			- (void)viewDidLoad {
				[super viewDidLoad];
				[self.view addSubView:self.tblLiveList];
				
			}
			
			- (void)setSubViewConstraints {
				[self.tblLiveList mas_makeConstraints:^(MASConstraintMaker *make) { 
					make.edges.equalTo(superview).with.insets(padding); 
				}];
			}
				
			- (BOOL)viewWillAppear {
				[super viewWillAppear];
			}
			
			#pragma mark - UITableViewDataSource
			
			- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
				...
				...
			}
			
			- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
				...
				...
			}
			
			#pragma mark - UITableViewDelegate
			
			.......
			
			#pragma mark - CustomViewDelegate
			
			......
			
			#pragma mark - Event Response
			
			- (void)tapOnView:(id)gesture {
				...
			}
			
			#pragma mark - Getter & Setter
			
			- (UITableView *)tblLiveList {
				if (!_tblLiveList) {
					...
				}
				return _tblLiveList;
			}
			
			...
			
			- (void)setArrayLive:(NSArray *)arrayLive {
				if ([arrayLive count] > 0) {
					_arrayLive = arrayLive;
				}
			}
			
			...
			
			@end
			
3.	关于属性名以及类名书写规范。对于系统提供的以及继承至之组件加上以下固定前(后)缀：  
	-	前缀  
		UIImage:img  
		UIImageView:imgView  
		UILabel:lbl  
		UIButton:btn  
		UITextField:textField  
		UITextView:textView  
		NSArray:array  
		NSMutableArray:mArray  
		NSDictionary:dict  
		NSMutableDictionary:mDict  
		NSString:str  
		NSMutableString:mStr
		
	-	后缀
		UIViewController:VC  
		UIView:View

4.	方法名定义：言简意赅，参照OC系统类的方法定义来写。