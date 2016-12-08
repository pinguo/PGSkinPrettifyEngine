#PGSkinPrettifyEngine(Android版)
品果视频美肤引擎Android版，最低SDK版本15
###一、使用概览：
品果视频美肤引擎（PGSkinPrettifyEngine）的总体使用步骤分为五个部分：

  * [创建并初始化引擎](#创建并初始化引擎)
  * [设置相关参数并进行美肤处理](#设置相关参数并进行美肤处理)
  * [显示及获取美肤结果](#显示及获取美肤结果)
  * [销毁引擎](#销毁引擎)
  * [接入步骤](#接入步骤)
  
以下对这五个部分分别进行详细的介绍。

###二、<b id='创建并初始化引擎'>创建并初始化引擎</b>
####2.1 相关接口

	/** 
 	* 描述：初始化引擎
 	* 返回值：成功返回 true，失败或已经初始化过了返回 false
 	* 参数：context 上下文
 	* 参数：key 字符串
 	* 参数：bInitEGL - 是否在 Native 层自己初始化 EGLContex
	*/
	public boolean InitialiseEngine(Context context, String key, boolean bInitEGL);



####2.2 接口描述
此接口会初始化引擎必须的所有组件，包括创建 OpenGL ES 上下文及各种缓冲区等等。

####2.3 调用示例

	// 创建美肤引擎对象
	PGSkinPrettifyEngine m_pPGSkinPrettifyEngine = new PGSkinPrettifyEngine();
	// 初始化美肤引擎
	String key = "S4JWSqgZKtQwTOmSYV/K+lPfT5EF3DhdWzc9h5Mf1I7gY1SHhaGokAVpTaTSWXbs6AEmO+Wmx7tS6MrX4KxHy4g752TfC4x4KibafcUtrIiCfvmnPTCeAT6K9J0sYcPycmwJFbYUgyn0zEc76WYGsGOPJCe4zNFQ2iWD6H4+fGA="
	m_pPGSkinPrettifyEngine.InitialiseEngine(this, key, false);


###三、<b id='设置相关参数并进行美肤处理'>设置相关参数并进行美肤处理</b>
####3.1 相关接口

	/** 
 	* 描述：设置输入帧
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：iTextureID - 相机回调所给的预览帧，iTextureWidth - 帧宽度，iTextureHeight - 帧高度
 	*/
	public boolean SetInputFrameByTexture(int iTextureID, int iTextureWidth, int iTextureHeight);

	/** 
 	* 描述：设置输入帧的人脸信息
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：sFaceRect - 帧所对应的人脸信息，用于人形虚化及基准肤色计算，若没有人脸则置为(0,0,0,0)，此时会对全图进行美肤，并关闭人形虚化
 	*/
	public boolean SetInputFrameFaceInfo(Rect sFaceRect);

	/** 
 	* 描述：设置一个方向，用于校正输入的预览帧
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：eAdjustInputOrient - 方向值
 	*/
	public boolean SetOrientForAdjustInput(PG_Orientation eAdjustInputOrient);

	/** 
 	* 描述：设置一个尺寸，用于调整输入帧的宽高，也是最终输出帧的宽高
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：iAdjustWidth - 输出帧宽度，iAdjustHeight - 输出帧高度
 	*/
	public boolean SetSizeForAdjustInput(int iAdjustWidth, int iAdjustHeight);

	/** 
 	* 描述：设置美肤步骤中磨皮的强度
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：iSoftenStrength - 磨皮强度，范围 0 - 100
 	*/
	public boolean SetSkinSoftenStrength(int iSoftenStrength);

	/** 
 	* 描述：设置人形虚化的强度
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：iBlurStrength - 虚化强度，范围 0 - 100，iVideoOrientation 当前相机的预览方向
 	*/
	public boolean SetPortraitBlurStrength(int iBlurStrength, int iVideoOrientation);

	/** 
 	* 描述：设置美肤步骤中的肤色调整参数
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：fPinking - 粉嫩程度， fWhitening - 白晰程度，fRedden - 红润程度，范围都是0.0 - 1.0
 	*/
	public boolean SetSkinColor(float fPinking, float fWhitening, float fRedden);

	/** 
 	* 描述：设置美肤结果的输出方向
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：eOutputOrient - 方向值
 	*/
	public boolean SetOutputOrientation(PG_Orientation eOutputOrient);

	/** 
 	* 描述：设置美肤结果的输出格式
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：eOutFormat - 输出的色彩格式
 	*/
	public boolean SetOutputFormat(PG_PixelFormat eOutFormat);

	/** 
 	* 描述：根据所设置的参数，运行引擎
 	* 返回值：成功返回 YES, 失败返回 NO
 	* 参数：无
 	*/
	public void RunEngine();


####3.2 接口描述
#####3.2.1 关于人脸信息
为了更细致和精确的美肤效果，所以在使用 SetInputFrameByTexture 接口设置输入图像时，引擎需要使用当前输入帧中的人脸信息，部分 Android  系统本身有提供人脸框检测的功能，具体使用方法可参考完整 Demo 源码，也可以使用第三方人脸识别库进行识别，但需要转换为 Android 系统所给的人脸坐标格式后再传给引擎。

#####3.2.2 关于输出方向
引擎提供了两个用于调整方向的接口，SetOrientForAdjustInput 用于调整输入帧的方向，此接口会影响到 Surface 所显示的内容，及输出缓冲区中的内容。另外，由于坐标系原点的不同，输出缓冲区中的帧可能和显示的不一样，所以要想显示的和输出的一致，还需要使用 SetOutputOrientation 接口对输出缓冲区进行调整。这两个接口的参数类型为方向枚举，引擎目前提供了如下七个方向：


	// 用于控制输出旋转方向的枚举
	public enum PG_Orientation
	{
    	PG_OrientationNormal(0),            /* 原样输出 */
    	PG_OrientationRightRotate90(1),     /* 右旋90度输出（注意改变输出宽高） */
    	PG_OrientationRightRotate180(2),    /* 右旋180度输出 */
    	PG_OrientationRightRotate270(3),    /* 右旋270度输出（注意改变输出宽高） */
    	PG_OrientationFlippedMirrored(4),   /* 翻转并镜像输出 */
    	PG_OrientationFlipped(5),           /* 上下翻转输出 */
    	PG_OrientationMirrored(6),;         /* 左右镜像输出 */
	}


#####3.3.3 关于输出格式
引擎目前支持3种输出格式，以方便后续的视频编码，分别为如下三种格式

	// 用于控制输出帧数据格式的枚举
	public enum PG_PixelFormat
	{
    	PG_Pixel_RGBA(0),       /*输出 RGBA 格式 */
    	PG_Pixel_BGRA(1),       /*输出 BGRA 格式 */
    	PG_Pixel_YUV420(2);     /*输出 YUV420 格式 */
	}

####3.3 调用示例

	// 设置美肤引擎输出的帧宽高
	m_pPGSkinPrettifyEngine.SetSizeForAdjustInput(m_sCameraPreviewFrameSize.height, m_sCameraPreviewFrameSize.width);
	// 将原始输入帧右旋（顺时针）270 度
	m_pPGSkinPrettifyEngine.SetOrientForAdjustInput(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationRightRotate270);
	// 设置美肤引擎的输出格式为BGRA
	m_pPGSkinPrettifyEngine.SetOutputFormat(PGSkinPrettifyEngine.PG_PixelFormat.PG_Pixel_BGRA);
	// 设置美肤引擎输出方向为翻转并镜像
	m_pPGSkinPrettifyEngine.SetOutputOrientation(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationFlippedMirrored);
	// 设置美肤强度为80
	m_pPGSkinPrettifyEngine.SetSkinSoftenStrength(80);
	// 设置背景模糊强度为80，此时需要同时告诉引擎目前的视频相机方向，用于引擎修正人脸坐标
	m_pPGSkinPrettifyEngine.SetPortraitBlurStrength(80, 270);
	// 设置输入图像，以及图像上的人脸信息
	m_pPGSkinPrettifyEngine.SetInputFrameFaceInfo(m_FaceRect);
	// 设置肤色高速参数，粉嫩，白皙，红润均为 0.5
	m_pPGSkinPrettifyEngine.SetSkinColor(0.5f, 0.5f, 0.5f);
	// 运行引擎，进行美肤处理
	m_pPGSkinPrettifyEngine.RunEngine();


###四、<b id='显示及获取美肤结果'>显示及获取美肤结果</b>
####4.1 相关接口

	/** 
 	* 描述：将显示内容左右镜像
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：bDislpayMirrored - 为 true 时显示内容会左右镜像
 	*/
	public boolean SetDisplayMirroredEnable(boolean bDislpayMirrored);

	/** 
 	* 描述：将美肤结果刷新到 Surface
 	* 返回值：成功返回 true，失败返回 false
 	* 参数：iScreenWidth - Surface的宽，iScreenHeight - Surface的高
 	*/
	public boolean GetOutputToScreen(int iScreenWidth, int iScreenHeight);

	/** 
 	* 描述：手动获取美肤结果
 	* 返回值：用于存放结果的缓冲区，数据格式为初始化美肤引擎所设置的格式
 	* 参数：无 
 	*/
	public ByteBuffer SkinSoftenGetResult();


####4.2 接口描述
#####4.2.1 关于美肤结果获取
使用者通过调用 GetSkinPrettifyResult 主动获取美肤结果缓冲区，此缓冲区的具体格式通过 SetOutputFormat 接口设置，需要注意的是，当输出格式设置为 BGRA 和 YUV 时，缓冲区的长度是不一样的。外部无需释放所返回的缓冲区，引擎会自行管理。

#####4.2.2 关于美肤预览
预览美肤的方式有多种，一种是通过引擎提供的 PGGLContextManager 类，将 SurfaceView 或 TextureView 的 Surface 添加到类中，并在适当的地方进行切换 GL 上下文，当有美肤结果后，调用 GetOutputToScreen 可将美肤结果呈现到 Surface 中。也可以使用 GLSurfaceView，此时无需使用 PGGLContextManager 来管理 GL 上下文，需要注意的是，对引擎所有接口的调用都需要放在 GL 线程中进行，否则会出现干扰主界面显示，或作图不正确的问题。

####4.3 调用示例

	// 主动获取输出结果
	ByteBuffer pResult = m_pPGSkinPrettifyEngine.SkinSoftenGetResult();
	// 将美肤结果呈现到 Surface
	m_pPGSkinPrettifyEngine.GetOutputToScreen(m_iSurfaceWidth, m_iSurfaceHeight);
	m_pGlContext.presentSurface();

###五、<b id='销毁引擎'>销毁引擎</b>
####5.1 相关接口

	/** 
 	* 描述：销毁引擎
 	* 返回值：无
 	* 参数：无
 	*/
	public void DestroyEngine();

####5.2 接口描述
销毁引擎时，会释放和删除引擎所创建一切组件，包括各种缓冲区，Mesh 数据，纹理等等，所以销毁引擎后，不应该再对这些对象进行任何操作。
####5.3 调用示例

	// 销毁美肤引擎
	m_pPGSkinPrettifyEngine.DestroyEngine();
	m_pPGSkinPrettifyEngine = null;

###六、<b id='接入步骤'>接入步骤</b>
####6.1 申请key	
    联系liuzhaohui@camera360.com
####6.2 导入相关文件	

	第一步：新建路径为：us.pinguo.pgskinprettifyengine的package
	放入文件：PGGLContextManager.java和PGSkinPrettifyEngine.java
	（路径不能变，否则so会找不到文件）
	第二步：在libs->armeabi-v7中导入so：libPGSkinPrettifyEngine.so
	
####6.2初始化SDK

	m_pPGSkinPrettifyEngine = new PGSkinPrettifyEngine();
	m_pGlContext = new PGGLContextManager();
	m_pGlContext.initGLContext(0);


	//相机初始化完成后
	//如果用的监听是SurfaceTextureCallback则不需要pGlContext及其相关的所有代码
	m_pGlContext.addSurface(holder);
	m_pGlContext.activateOurGLContext();
	m_iCameraTextureID = m_pGlContext.createGLExtTexture();
	m_pCameraTexture = new SurfaceTexture(m_iCameraTextureID);

	// 初始化引擎（一个pPGSkinPrettifyEngine生命周期内只允许初始化一次）
	m_pPGSkinPrettifyEngine.InitialiseEngine(this,SDK_KEY,false);
	m_pPGSkinPrettifyEngine.SetSizeForAdjustInput(m_sCameraPreviewFrameSize.height, m_sCameraPreviewFrameSize.width);
	m_pPGSkinPrettifyEngine.SetOrientForAdjustInput(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationRightRotate90);
	m_pPGSkinPrettifyEngine.SetOutputFormat(PGSkinPrettifyEngine.PG_PixelFormat.PG_Pixel_BGRA);
	m_pPGSkinPrettifyEngine.SetOutputOrientation(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationFlippedMirrored);

	//控制整体的美肤强度 0~100（可动态调用）
	m_pPGSkinPrettifyEngine.SetSkinSoftenStrength(mnSoftenValue);
	//控制滤镜效果的程度（可动态调用）
	//mfPinkValue   粉嫩程度 0~1.0f
	//mfWhitenValue 白皙程度 0~1.0f
	//mfReddenValue 红润程度 0~1.0f
	m_pPGSkinPrettifyEngine.SetSkinColor(mfPinkValue, mfWhitenValue, mfReddenValue);
	
####6.3数据处理

	//帧处理
	//如果输入的是YV12格式的字节流  
	m_pPGSkinPrettifyEngine.SetInputFrameByYV12(data,m_sCameraPreviewFrameSize.width, m_sCameraPreviewFrameSize.height);
	m_pPGSkinPrettifyEngine.RunEngine();
	m_pPGSkinPrettifyEngine.GetOutputToScreen(m_iSurfaceWidth, m_iSurfaceHeight);
	m_pGlContext.presentSurface();
	//得到处理后的字节流
	m_pPGSkinPrettifyEngine.SkinSoftenGetResult();



	//如果输入的是texture id
	m_pPGSkinPrettifyEngine.SetInputFrameByTexture(m_iCameraTextureID, m_sCameraPreviewFrameSize.width, 			m_sCameraPreviewFrameSize.height);
	m_pPGSkinPrettifyEngine.RunEngine();
	m_pPGSkinPrettifyEngine.GetOutputToScreen(m_iSurfaceWidth, m_iSurfaceHeight);
	m_pGlContext.presentSurface();
	//得到处理后的texture id
	m_pPGSkinPrettifyEngine.GetOutputTextureID();
	
####6.4销毁

	if (m_pGlContext!=null)
		m_pGlContext.activateOurGLContext();
	// 销毁 PGHelixEngine
	if (m_pPGSkinPrettifyEngine != null)
	{
    		m_pPGSkinPrettifyEngine.DestroyEngine();
    		m_pPGSkinPrettifyEngine = null;
    		m_bIsFirstFrame = true;
	}

	// 销毁 GL Texture
	if (m_pCameraTexture != null)
	{
    		m_pGlContext.deleteGLExtTexture(m_iCameraTextureID);
    		m_pCameraTexture.release();
   		 m_pCameraTexture = null;
	}

	// 销毁 GL Surface
	if (m_pGlContext != null)
	{
    		Log.i(LOG_TAG, "releasing window surface");
    		m_pGlContext.releaseSurface();
	}
