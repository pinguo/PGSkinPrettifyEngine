# PGSkinPrettifyEngine(iOS版)
品果视频美肤引擎iOS版


###一、使用概览：
品果视频美肤引擎(PGSkinPrettifyEngine)的总体使用步骤分为四个部分：

* [创建并初始化引擎](#创建并初始化引擎)。 
* [设置相关参数并进行美肤处理](#设置相关参数并进行美肤处理)。
* [显示及获取美肤结果](#显示及获取美肤结果)。
* [销毁引擎](#销毁引擎)。

以下对这四个部分分别进行详细的介绍。

###二、<b id='创建并初始化引擎'>创建并初始化引擎</b>
####2.1 相关接口

	/**
 	* 描述：初始化引擎
 	* 参数：pKey，字符串
 	* 返回值：成功返回 YES，失败或已经初始化过返回 NO
 	* 参数：无
	*/ 
	- (BOOL) InitEngineWithKey:(NSString*) pKey;


####2.2 接口描述
此接口会初始化引擎必须的所有组件，包括创建 OpenGL ES 上下文及各种缓冲区等等。

####2.3 调用示例

	// 创建美肤引擎对象
	PGSkinPrettifyEngine* m_pPGSkinPrettifyEngine = [[PGSkinPrettifyEngine alloc] init];
	// 初始化美肤引擎
	NSString *pKey = @"KDpbdC/aXRvikWVkD1bT6FB/+dCAFt67nHDFut3P+q+c8YeFVh79PWntsGq0JfVGXLHaODWjq+n2h9EgX4FRw4oHuq6GiuV6LL3p0/55rJ9uWBRV+P/Uo5XJuNWz7CEdl6Ebs9CiCFaghqxgfvswFkpt+ah1C427XLs7PQGcaM0=";
	[m_pPGSkinPrettifyEngine InitEngineWithKey:pKey];


###三、<b id='设置相关参数并进行美肤处理'>设置相关参数并进行美肤处理</b>
####3.1 相关接口

	/**
 	* 描述：设置输入帧
 	* 返回值：无
 	* 参数：pInputPixel - 相机回调所给的预览帧，sFaceRect - 帧所对应的人脸信息，用于人形虚化及基准肤色计算，若没有人脸则置为(0,0,0,0)，此时会对全图进行美肤，并关闭人形虚化
 	*/
	- (void) SetInputFrameByCVImage:(CVPixelBufferRef)pInputPixel FaceRect:(CGRect)sFaceRect;

	/**
 	* 描述：设置一个方向，用于校正输入的预览帧
 	* 返回值：无
 	* 参数：eAdjustInputOrient - 方向值
 	*/
	- (void) SetOrientForAdjustInput:(PGOrientation)eAdjustInputOrient;

	/**
 	* 描述：设置一个尺寸，用于调整输入帧的宽高，也是最终输出帧的宽高
 	* 返回值：无
 	* 参数：sSize - 宽和高
 	*/
	- (void) SetSizeForAdjustInput:(CGSize)sSize;

	/**
 	* 描述：设置美肤步骤中磨皮的强度
 	* 返回值：无
 	* 参数：iSoftenStrength - 磨皮强度，范围 0 - 100
 	*/
	- (void) SetSkinSoftenStrength:(int)iSoftenStrength;

	/**
 	* 描述：设置人形虚化的强度
 	* 返回值：无
 	* 参数：iBlurStrength - 虚化强度，范围 0 - 100，videoOrientation 当前相机的预览方向
 	*/
	- (void) SetPortraitBlurStrength:(int)iBlurStrength VideoOrient:(AVCaptureVideoOrientation)videoOrientation;

	/** 
 	* 描述：设置美肤步骤中的肤色调整参数
 	* 返回值：无
 	* 参数：fPinking - 粉嫩程度， fWhitening - 白晰程度，fRedden - 红润程度，范围都是0.0 - 1.0
 	*/
	- (void) SetSkinColor:(float)fPinking Whitening:(float)fWhitening Redden:(float)fRedden;

	/** 
 	* 描述：设置美肤结果的输出方向
 	* 返回值：无
 	* 参数：eOutputOrientation - 方向值
 	*/
	- (void) SetOutputOrientation:(PGOrientation)eOutputOrientation;

	/** 
 	* 描述：设置美肤结果的输出方向
 	* 返回值：无
 	* 参数：eOutFormat - 输出的色彩格式
 	*/
	- (void) SetOutputFormat:(PGPixelFormat)eOutFormat;

	/** 
 	* 描述：根据所设置的参数，运行引擎
 	* 返回值：成功返回 YES, 失败返回 NO
 	* 参数：无
 	*/
	- (BOOL) RunEngine;


####3.2 接口描述
#####3.2.1 关于人脸信息
为了更细致和精确的美肤效果，所以在使用 SetInputFrameByCVImage 接口设置输入图像时，引擎需要使用当前输入帧中的人脸信息，iOS 系统本身有提供人脸框检测的功能，具体使用方法可参考完整 Demo 源码，也可以使用第三方人脸识别库进行识别，但需要转换为 iOS 系统所给的人脸坐标格式后再传给引擎。

#####3.2.2 关于输出方向
引擎提供了两个用于调整方向的接口，SetOrientForAdjustInput 用于调整输入帧的方向，此接口会影响到 PGOglView 所显示的内容，及输出缓冲区中的内容。另外，由于坐标系原点的不同，输出缓冲区中的帧可能和显示的不一样，所以要想显示的和输出的一致，还需要使用 SetOutputOrientation 接口对输出缓冲区进行调整。这两个接口的参数类型为方向枚举，引擎目前提供了如下七个方向：

	// 用于控制输出旋转方向的枚举
	typedef enum
	{
    	PGOrientationNormal = 0,           // 原样输出
    	PGOrientationRightRotate90,        // 右旋90度输出（注意改变输出宽高）
    	PGOrientationRightRotate180,       // 右旋180度输出
    	PGOrientationRightRotate270,       // 右旋270度输出（注意改变输出宽高）
    	PGOrientationFlippedMirrored,      // 翻转并镜像输出
    	PGOrientationFlipped,              // 上下翻转输出
    	PGOrientationMirrored              // 左右镜像输出
	} PGOrientation;


#####3.3.3 关于输出格式
引擎目前支持3种输出格式，以方便后续的视频编码，分别为如下三种格式

	// 用于控制输出帧数据格式的枚举
	typedef enum
	{
    	PGPixelFormatRGBA = 0,  // 输出 kCVPixelFormatType_32BGRA 格式的 PixelBuffer, 内部数据为 RGBA
    	PGPixelFormatBGRA,      // 输出 kCVPixelFormatType_32BGRA 格式的 PixelBuffer, 内部数据为 BGRA
    	PGPixelFormatYUV420     // 输出 kCVPixelFormatType_420YpCbCr8BiPlanarVideoRange, 内部数据为 YUV420
	} PGPixelFormat;

####3.3 调用示例

	// 设置美肤引擎输出的帧宽高
	[m_pPGSkinPrettifyEngine SetSizeForAdjustInput:CGSizeMake(size.width, size.height)];
	// 将原始输入帧右旋（顺时针）90 度
	[m_pPGSkinPrettifyEngine SetOrientForAdjustInput:PGOrientationRightRotate90];
	// 设置美肤引擎的输出格式为BGRA
	[m_pPGSkinPrettifyEngine SetOutputFormat:PGPixelFormatBGRA];
	// 设置美肤引擎输出方向为翻转并镜像
	[m_pPGSkinPrettifyEngine SetOutputOrientation:PGOrientationFlippedMirrored];
	// 设置美肤强度为80
	[m_pPGSkinPrettifyEngine SetSkinSoftenStrength:80];
	// 设置背景模糊强度为80，此时需要同时告诉引擎目前的视频相机方向，用于引擎修正人脸坐标
	[m_pPGSkinPrettifyEngine SetPortraitBlurStrength:80 	VideoOrient:AVCaptureVideoOrientationLandscapeLeft];
	// 设置输入图像，以及图像上的人脸信息
	[m_pPGSkinPrettifyEngine SetInputFrameByCVImage:imageBuffer FaceRect:m_sOrigFaceRect];
	// 启用肤色红润效果
	[m_pPGSkinPrettifyEngine SetSkinColor:0.5 Whitening:0.5 Redden:0.5];
	// 运行引擎，进行美肤处理
	[m_pPGSkinPrettifyEngine RunEngine];

###四、<b id='显示及获取美肤结果'>显示及获取美肤结果</b>
####4.1 相关接口

	/** 
 	* 描述：设置美肤结果的输出回调
 	* 返回值：无
 	* 参数：outputCallback - 委托
 	*/
	- (void) SetSkinPrettifyResultDelegate:(id <PGSkinPrettifyDelegate>)outputCallback;

	/** 
 	* 描述：主动获取美肤结果
 	* 返回值：无
 	* 参数：pResultBuffer - 指向 CVPixelBufferRef 的指针
 	*/
	- (void) GetSkinPrettifyResult:(CVPixelBufferRef *)pResultBuffer;

	/** 
 	* 描述：创建一个预览美肤效果的 View ,返回的 View 会在 DestroyEngine 时销毁，不需要外部销毁
 	* 返回值：所创建的 PGOglView 指针
 	* 参数：View 的尺寸
 	*/
	- (PGOglView *) PGOglViewCreateWithFrame:(CGRect)sFrame;

	/** 
 	* 描述：将美肤结果刷新到 PGOglView
 	* 返回值：成功返回 YES，引擎未初始化，或 View 未成功创建返回 NO
 	* 参数：View 的尺寸
 	*/
	- (BOOL) PGOglViewPresent;

	/** 
 	* 描述：将显示内容左右镜像
 	* 返回值：无
 	* 参数：无
 	*/
	- (void) PGOglViewMirrored;

	/** 
 	* 描述：外部更改了 PGOglView 的 Size 后通过调用此方法通知引擎更新 PGOglView 相关的组件
 	* 返回值：无
 	* 参数：无
 	*/
	- (void) PGOglViewSizeChanged;


####4.2 接口描述
#####4.2.1 关于美肤结果获取
引擎提供了两种获取美肤输出的方式，一种是通过 SetSkinPrettifyResultDelegate 接口设置一个回调，当引擎完成了一帧美肤后，会调用所设置的回调，传入美肤结果。另一种是通过 GetSkinPrettifyResult 主动获取美肤结果缓冲区，此缓冲区的具体格式通过 SetOutputFormat 接口设置，外部无需释放所返回的缓冲区，引擎会自行管理。

#####4.2.2 关于美肤预览
预览美肤的方式也有两种，一种是通过引擎提供的 PGOglViewCreateWithFrame 接口创建一个 UIView ，并添加到 App 的 View 层级中，当有美肤结果后，调用 PGOglViewPresent 可将美肤结果呈现到 View 中，外部也无需释放此 View，引擎会自行管理。另一种方式是，通过 4.2.1 中的方式获取到缓冲区，然后开发者自行将此缓冲区显示出来。 

####4.3 调用示例

	CVPixelBufferRef pResultBuffer;
	// 主动获取输出结果
	[m_pPGSkinPrettifyEngine GetSkinPrettifyResult:&pResultBuffer];
	// 设置美肤引擎的输出回调
	[m_pPGSkinPrettifyEngine SetSkinPrettifyResultDelegate:self];
	// 将美肤结果呈现到 PGOglView
	[m_pPGSkinPrettifyEngine PGOglViewPresent];


###五、<b id='销毁引擎'>销毁引擎</b>
####5.1 相关接口

	/**
 	* 描述：销毁引擎
 	* 返回值：无
 	* 参数：无
 	*/
	- (void) DestroyEngine;

####5.2 接口描述
销毁引擎时，会释放和删除引擎所创建一切组件，包括各种缓冲区，以及预览 View，所以销毁引擎后，不应该再对这些对象进行任何操作。
####5.3 调用示例

	// 销毁美肤引擎
	[m_pPGSkinPrettifyEngine DestroyEngine];

