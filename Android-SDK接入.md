# PGSkinPrettifyEngine SDK接入流程说明(Android版)
品果视频美肤引擎Android版，最低SDK版本15

采用授权key的方式，一个key支持3个包名

## 1 申请key	
[申请地址](http://www.camera360.com/filter/apply.html)
	
	
## 2 导入相关文件	

	第一步：项目根目录下新建：us.pinguo.pgskinprettifyengine的package
	放入文件：PGGLContextManager.java和PGSkinPrettifyEngine.java
	（注意：路径不能变，否则so会找不到文件）
	第二步：在libs->armeabi-v7中导入so：libPGSkinPrettifyEngine.so
	
## 3 初始化SDK（Engine的初始化和数据处理必须在同一个线程中）

	step1:在onResume中实例化PGSkinPrettifyEngine
	m_pPGSkinPrettifyEngine = new PGSkinPrettifyEngine();
	
	step2: 初始化相机 监听surfaceCreated 在surfaceCreated回调中初始化egl	
	m_pGlContext = new PGGLContextManager();
	m_pGlContext.initGLContext(0);
	
	//构造surface 如果不需要渲染，仅做数据处理，addSurface参数设置为null， m_pGlContext.addSurface(null)；	
	m_pGlContext.addSurface(holder);
	
	//将渲染上下文绑定到渲染面
	m_pGlContext.activateOurGLContext();
	
	m_iCameraTextureID = m_pGlContext.createGLExtTexture();
	m_pCameraTexture = new SurfaceTexture(m_iCameraTextureID);
	
	//把生成的SurfaceTexture对象设置了输出载体		
	m_pCamera.setPreviewTexture(m_pCameraTexture);
	m_pCamera.startPreview();
	
	step3:在onPreviewFrame中对预览帧数据进行处理		
	
	// 初始化引擎（一个pPGSkinPrettifyEngine生命周期内只允许初始化一次）	
	// SDK_KEY 申请链接 http://www.camera360.com/filter/apply.html	
	m_pPGSkinPrettifyEngine.InitialiseEngine(this,SDK_KEY,false);
	m_pPGSkinPrettifyEngine.SetSizeForAdjustInput(m_sCameraPreviewFrameSize.height, m_sCameraPreviewFrameSize.width);
	m_pPGSkinPrettifyEngine.SetOrientForAdjustInput(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationRightRotate90);
	
	//设置输出格式 
	//PG_Pixel_RGBA(0),	/*输出 RGBA 格式 */
	//PG_Pixel_BGRA(1),	/*输出 BGRA 格式 */
	//PG_Pixel_NV21(2),	/*输出 YUV420 格式(NV21格式) */
        //PG_Pixel_YV12(3);       /*输出 YUV420 格式(YV12格式) */
        m_pPGSkinPrettifyEngine.SetOutputFormat(PGSkinPrettifyEngine.PG_PixelFormat.PG_Pixel_BGRA);
	
	
	//设置输出方向
	//PG_OrientationNormal(0),            /* 原样输出 */
        //PG_OrientationRightRotate90(1),     /* 右旋90度输出（注意改变输出宽高） */
        //PG_OrientationRightRotate180(2),    /* 右旋180度输出 */
        //PG_OrientationRightRotate270(3),    /* 右旋270度输出（注意改变输出宽高） */
        //PG_OrientationFlippedMirrored(4),   /* 翻转并镜像输出 */
        //PG_OrientationFlipped(5),           /* 上下翻转输出 */
        //PG_OrientationMirrored(6),          /* 左右镜像输出 */
        //PG_OrientationRightRotate90Mirrored(7), /*右旋90并左右镜像输出*/
        //PG_OrientationRightRotate180Mirrored(8), /*右旋180并左右镜像输出*/
        //PG_OrientationRightRotate270Mirrored(9); /*右旋270并左右镜像输出*/
        m_pPGSkinPrettifyEngine.SetOutputOrientation(PGSkinPrettifyEngine.PG_Orientation.PG_OrientationFlippedMirrored);

	//控制整体的美肤强度 0~100（可动态调用）
	m_pPGSkinPrettifyEngine.SetSkinSoftenStrength(mnSoftenValue);
	
	//控制滤镜效果的程度（可动态调用）
	//mfPinkValue   粉嫩程度 0~1.0f
	//mfWhitenValue 白皙程度 0~1.0f
	//mfReddenValue 红润程度 0~1.0f
	m_pPGSkinPrettifyEngine.SetSkinColor(mfPinkValue, mfWhitenValue, mfReddenValue);
	
## 4 数据处理

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
	
## 5 销毁

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
	
## 6 常见问题
	
	*1：初始化engin时，程序crash。	
	产生原因：PGGLContextManager.java和PGSkinPrettifyEngine.java需要放到指定路径否则so会找不到。
	
	*2：预览帧数据的处理后无美肤效果。	
	产生原因：1.engin的初始化和数据处理必须在同一个线程中。2.sdk授权key失效。	
	
	*3：m_pPGSkinPrettifyEngine.SkinSoftenGetResult返回byteBuffer 从缓冲器中拿bute[]时error
	参考方案：ByteBuffer buffer = m_pPGSkinPrettifyEngine.SkinSoftenGetResult();
		buffer.clear();
		bytes = new byte[buffer.capacity()];
		buffer.get(bytes,0,bytes.length);
