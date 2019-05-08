# MFC 按钮同步放大
```
/* 
同步更改尺寸：
第一步：WIN_SIZE-》Onsize
	void XXXDlg::OnSize(UINT nType, int cx, int cy)
	{
		CDialogEx::OnSize(nType, cx, cy);

		// ADD:
		if (nType != SIZE_MINIMIZED)
		{
			ReSize();
			Invalidate();
		}		
	}
第二步：
	XXXDlg;头文件添加：
	// ADD:
	POINT old;//用于记录窗口原始坐标
	void ReSize()
	{
		float fsp[2];
		POINT Newp; //获取现在对话框的大小
		CRect recta;
		GetClientRect(&recta);     //取客户区大小
		Newp.x = recta.right - recta.left;
		Newp.y = recta.bottom - recta.top;
		fsp[0] = (float)Newp.x / old.x;
		fsp[1] = (float)Newp.y / old.y;
		CRect Rect;
		int woc;
		CPoint OldTLPoint, TLPoint; //左上角
		CPoint OldBRPoint, BRPoint; //右下角
		HWND  hwndChild = ::GetWindow(m_hWnd, GW_CHILD);  //列出所有控件
		while (hwndChild)
		{
			woc = ::GetDlgCtrlID(hwndChild);//取得ID
			GetDlgItem(woc)->GetWindowRect(Rect);
			ScreenToClient(Rect);
			OldTLPoint = Rect.TopLeft();
			TLPoint.x = long(OldTLPoint.x*fsp[0]);
			TLPoint.y = long(OldTLPoint.y*fsp[1]);
			OldBRPoint = Rect.BottomRight();
			BRPoint.x = long(OldBRPoint.x *fsp[0]);
			BRPoint.y = long(OldBRPoint.y *fsp[1]);
			Rect.SetRect(TLPoint, BRPoint);
			GetDlgItem(woc)->MoveWindow(Rect, TRUE);
			hwndChild = ::GetWindow(hwndChild, GW_HWNDNEXT);
		}
		old = Newp;
	}
*/
```
# MFC 画图到指定ID上
```
/*
功能;Mat 画入指定IDC
第一步：头文件：在当前的类的的头文件代码区头包含：#include "CvvImage.h"{CvvImage.h\CvvImage.cpp}
第二步：将函数在当前所在的类的头文件声明：void CMFC_CalibrationDlg::drawToDC(IplImage* image, UINT ID)

void XXXDlg::drawToDC(IplImage* image, UINT ID)//画到指定ID（默认IDC_STATIC）在头文件声明函数
{
	CDC* pDC = GetDlgItem(ID)->GetDC();
	HDC pHdc = pDC->GetSafeHdc();

	CRect rect;
	GetDlgItem(ID)->GetClientRect(&rect);

	CvvImage cimg;
	cimg.CopyOf(image);
	cimg.DrawToHDC(pHdc, rect);

	ReleaseDC(pDC);
}

第三步：调用：IplImage image = Mat_src;		drawToDC(&image, ID)
*/
```

# MFC创建子弹窗
```
第一步：*.rc资源处插入 Dialog -》 对新产生的窗口右键添加类，取名最好带 xxDlg -》 自动生成 xxDlg.h 和 xxDlg.cpp 文件
第二步：在父窗口头文件添加新的子窗口头文件。
第三步：使用父窗口进行按钮设置，对按钮A添加事务处理程序，在该事务处理程序写入如下代码：
	xxDlg Dlgclass;
	Dlgclass.DoModal();//当按A按钮即可调用-模态

```
# 按钮事件——打开图片
```
// TODO:  在此添加命令处理程序代码
// 读取图片——global
CString filter;
filter = "所有文件(*.bmp,*.jpg,*.gif,*tiff)|*.bmp;*.jpg;*.gif;*.tiff| BMP(*.bmp)|*.bmp| JPG(*.jpg)|*.jpg| GIF(*.gif)|*.gif| TIFF(*.tiff)|*.tiff||";
CFileDialog dlg(TRUE, NULL, NULL, OFN_HIDEREADONLY, filter, NULL);

// 按下确定按钮 dlg.DoModal() 函数显示对话框
if (dlg.DoModal() == IDOK)
{
	// 打开对话框获取图像信息
	CString BmpName = dlg.GetPathName();     //获取文件路径名   如D:\pic\abc.bmp
	CString EntName = dlg.GetFileExt();      //获取文件扩展名
	EntName.MakeLower();                     //将文件扩展名转换为一个小写字符

	// 加载图片
	{
		/*【方式一】*/
		//【第一步：CImage加载图片】
		ATL::CImage Image;
		Image.Load((BmpName));

		if (Image.IsNull())
		{
			flag_openimage = false;
			MessageBox(_T("没加载成功"));
			return;
		}
		else
		{
			// 标志位——flag_camera
			flag_openimage = true; //相机标志位
		}
		//【第二步：CRect获取控件范围】
		CRect rect;//定义矩形类  
		GetDlgItem(IDC_STATIC_SRC)->GetClientRect(&rect);//将窗口矩形选中到picture控件上 

		//【第三步：CDC】			
		CDC *pDC = GetDC();//获取对话框控件
		pDC = GetDlgItem(IDC_STATIC_SRC)->GetDC();  // 获取picture控件DC
		SetStretchBltMode(pDC->m_hDC, HALFTONE);//调整图片缩放平均颜色
		Image.Draw(pDC->m_hDC, rect);

		/*【方式二】*/
		USES_CONVERSION;
		string str(W2A(BmpName));
		global = imread(str);
		if (global.empty())
		{
			flag_openimage = false;
			MessageBox(_T("没加载成功"));
			return;
		}
		else
		{
			// 标志位——flag_camera
			global_temp = global;
			flag_openimage = true; //相机标志位
		}
		return;
	}
}
```

# opencv + 几个图像预处理
```
/*blur 滤波*/
void CMFC_DemoDlg::OnFiltering32795()
{
	// TODO:  在此添加命令处理程序代码
	if (flag_openimage)
	{
		Mat g_srcImage = global_temp;    // 全局的源图像
		// 分别对应全局的方框滤波、均值滤波、高斯滤波、中值滤波、双边滤波的输出图像以及内核值/参数值
		Mat g_dstImgBox, g_dstImgBlur, g_dstImgGaussian, g_dstImgMedian, g_dstImgBilateral;
		IplImage image;

		/*[1]*/
		if (true)
		{
			int g_BoxFilterVal = 5;
			boxFilter(g_srcImage, g_dstImgBox, -1, Size(g_BoxFilterVal + 1, g_BoxFilterVal + 1));
			imwrite("blur_boxFilter.bmp", g_dstImgBox);
			image = g_dstImgBox;
			drawToDC(&image, IDC_STATIC_DST0);
		}
		/*[2]*/
		if (true)
		{
			int g_BlurVal = 12;
			blur(g_srcImage, g_dstImgBlur, Size(g_BlurVal + 1, g_BlurVal + 1),
				Point(-1, -1));
			imwrite("blur_Meanblur.bmp", g_dstImgBlur);
			image = g_dstImgBlur;
			drawToDC(&image, IDC_STATIC_DST1);
		}

		/*[3]*/
		if (true)
		{
			int g_GaussianBlurVal = 5;
			GaussianBlur(g_srcImage, g_dstImgGaussian, Size(g_GaussianBlurVal * 2 + 1,
				g_GaussianBlurVal * 2 + 1), 0, 0);
			imwrite("blur_GaussianBlur.bmp", g_dstImgGaussian);
			image = g_dstImgGaussian;
			drawToDC(&image, IDC_STATIC_DST2);
		}

		/*[4]*/
		if (true)
		{
			int g_MedianBlurVal = 12;
			medianBlur(g_srcImage, g_dstImgMedian, g_MedianBlurVal * 2 + 1);
			imwrite("blur_medianBlur.bmp", g_dstImgMedian);
			image = g_dstImgMedian;
			drawToDC(&image, IDC_STATIC_DST3);
		}

		/*[5]*/
		if (true)
		{
			int g_BilateralFilterVal = 12;
			bilateralFilter(g_srcImage, g_dstImgBilateral, g_BilateralFilterVal,
				g_BilateralFilterVal * 2, g_BilateralFilterVal / 2);
			imwrite("blur_bilateralFilter.bmp", g_dstImgBilateral);
			image = g_dstImgBilateral;
			drawToDC(&image, IDC_STATIC_DST4);
		}
	}	
}



void AdaptiveThereshold(Mat src, Mat dst)
{
	//cvtColor(src, dst, CV_BGR2GRAY);
	int x1, y1, x2, y2;
	int count = 0;
	long long sum = 0;
	int S = src.rows >> 3;  //划分区域的大小S*S
	int T = 15;         /*百分比，用来最后与阈值的比较。原文：If the value of the current pixel is t percent less than this average
						then it is set to black, otherwise it is set to white.*/
	int W = dst.cols;
	int H = dst.rows;
	long long **Argv;
	Argv = new long long*[dst.rows];
	for (int ii = 0; ii<dst.rows; ii++)
	{
		Argv[ii] = new long long[dst.cols];
	}

	for (int i = 0; i<W; i++)
	{
		sum = 0;
		for (int j = 0; j<H; j++)
		{
			sum += dst.at<uchar>(j, i);
			if (i == 0)
				Argv[j][i] = sum;
			else
				Argv[j][i] = Argv[j][i - 1] + sum;
		}
	}

	for (int i = 0; i<W; i++)
	{
		for (int j = 0; j<H; j++)
		{
			x1 = i - S / 2;
			x2 = i + S / 2;
			y1 = j - S / 2;
			y2 = j + S / 2;
			if (x1<0)
				x1 = 0;
			if (x2 >= W)
				x2 = W - 1;
			if (y1<0)
				y1 = 0;
			if (y2 >= H)
				y2 = H - 1;
			count = (x2 - x1)*(y2 - y1);
			sum = Argv[y2][x2] - Argv[y1][x2] - Argv[y2][x1] + Argv[y1][x1];


			if ((long long)(dst.at<uchar>(j, i)*count)<(long long)sum*(100 - T) / 100)
				dst.at<uchar>(j, i) = 0;
			else
				dst.at<uchar>(j, i) = 255;
		}
	}
	for (int i = 0; i < dst.rows; ++i)
	{
		delete[] Argv[i];
	}
	delete[] Argv;
}



/*bw二值化*/
void CMFC_DemoDlg::OnBinaryOnetap2bw()
{
	// TODO:  在此添加命令处理程序代码
	// TODO:  在此添加命令处理程序代码
	if (flag_openimage)
	{
		Mat cvtColor_src = global_temp;
		if (cvtColor_src.type() == CV_8UC3)
		{
			/*
			CV_THRESH_BINARY =0,  /* value = value > threshold ? max_value : 0      
			CV_THRESH_BINARY_INV = 1,  /* value = value > threshold ? 0 : max_value      
			CV_THRESH_TRUNC = 2,  /* value = value > threshold ? threshold : value   
			CV_THRESH_TOZERO = 3,  /* value = value > threshold ? value : 0          
			CV_THRESH_TOZERO_INV = 4,  /* value = value > threshold ? 0 : value        
			CV_THRESH_OTSU = 8  /* use
			*/


			cvtColor(cvtColor_src, cvtColor_src, CV_RGB2GRAY);

			Mat dst0, dst1, dst2, dst3, dst4, dst5, dst6, dst7;
			IplImage image;
			threshold(cvtColor_src, dst0, 50, 200, CV_THRESH_BINARY);
			imwrite("bw_CV_THRESH_BINARY.bmp", dst0);
			image = dst0;
			drawToDC(&image, IDC_STATIC_DST0);

			threshold(cvtColor_src, dst1, 50, 200, CV_THRESH_BINARY_INV);
			imwrite("bw_CV_THRESH_BINARY_INV.bmp", dst1);
			image = dst1;
			drawToDC(&image, IDC_STATIC_DST1);

			threshold(cvtColor_src, dst2, 50, 200, CV_THRESH_TRUNC);
			imwrite("bw_CV_THRESH_TRUNC.bmp", dst2);
			image = dst2;
			drawToDC(&image, IDC_STATIC_DST2);

			threshold(cvtColor_src, dst3, 50, 200, CV_THRESH_TOZERO);
			imwrite("bw_CV_THRESH_TOZERO.bmp", dst3);
			image = dst3;
			drawToDC(&image, IDC_STATIC_DST3);

			threshold(cvtColor_src, dst4, 50, 200, CV_THRESH_TOZERO_INV);
			imwrite("bw_CV_THRESH_TOZERO_INV.bmp", dst4);
			image = dst4;
			drawToDC(&image, IDC_STATIC_DST4);

			threshold(cvtColor_src, dst5, 50, 200, CV_THRESH_OTSU);
			imwrite("bw_CV_THRESH_OTSU.bmp", dst5);
			image = dst5;
			drawToDC(&image, IDC_STATIC_DST5);


			int blockSize = 25;
			int constValue = 10;
			cv::adaptiveThreshold(cvtColor_src, dst6, 255, CV_ADAPTIVE_THRESH_MEAN_C, CV_THRESH_BINARY_INV, blockSize, constValue);
			imwrite("bw_AdaptiveThreshold.bmp", dst6);
			image = dst6;
			drawToDC(&image, IDC_STATIC_DST6);

			dst7 = cvtColor_src;
			AdaptiveThereshold(cvtColor_src, dst7);
			imwrite("bw_FastAdaptiveThereshold.bmp", dst7);
			image = dst7;
			drawToDC(&image, IDC_STATIC_DST7);
		}
		else
		{
			Mat dst0, dst1, dst2, dst3, dst4, dst5, dst6, dst7;
			IplImage image;
			threshold(cvtColor_src, dst0, 50, 200, CV_THRESH_BINARY);
			imwrite("bw_CV_THRESH_BINARY.bmp", dst0);
			image = dst0;
			drawToDC(&image, IDC_STATIC_DST0);

			threshold(cvtColor_src, dst1, 50, 200, CV_THRESH_BINARY_INV);
			imwrite("bw_CV_THRESH_BINARY_INV.bmp", dst1);
			image = dst1;
			drawToDC(&image, IDC_STATIC_DST1);

			threshold(cvtColor_src, dst2, 50, 200, CV_THRESH_TRUNC);
			imwrite("bw_CV_THRESH_TRUNC.bmp", dst2);
			image = dst2;
			drawToDC(&image, IDC_STATIC_DST2);

			threshold(cvtColor_src, dst3, 50, 200, CV_THRESH_TOZERO);
			imwrite("bw_CV_THRESH_TOZERO.bmp", dst3);
			image = dst3;
			drawToDC(&image, IDC_STATIC_DST3);

			threshold(cvtColor_src, dst4, 50, 200, CV_THRESH_TOZERO_INV);
			imwrite("bw_CV_THRESH_TOZERO_INV.bmp", dst4);
			image = dst4;
			drawToDC(&image, IDC_STATIC_DST4);

			threshold(cvtColor_src, dst5, 50, 200, CV_THRESH_OTSU);
			imwrite("bw_CV_THRESH_OTSU.bmp", dst5);
			image = dst5;
			drawToDC(&image, IDC_STATIC_DST5);


			int blockSize = 25;
			int constValue = 10;
			cv::adaptiveThreshold(cvtColor_src, dst6, 255, CV_ADAPTIVE_THRESH_MEAN_C, CV_THRESH_BINARY_INV, blockSize, constValue);
			imwrite("bw_AdaptiveThreshold.bmp", dst6);
			image = dst6;
			drawToDC(&image, IDC_STATIC_DST6);

			dst7 = cvtColor_src;
			AdaptiveThereshold(cvtColor_src, dst7);
			imwrite("bw_FastAdaptiveThereshold.bmp", dst7);
			image = dst7;
			drawToDC(&image, IDC_STATIC_DST7);
		}

	}
}


/*边缘检测*/
void CMFC_DemoDlg::OnEdgeOnetap2edge()
{
	// TODO:  在此添加命令处理程序代码
	if (flag_openimage)
	{
		Mat cvtColor_src = global_temp;
		if (cvtColor_src.type() == CV_8UC3)
		{
			cvtColor(cvtColor_src, cvtColor_src, CV_RGB2GRAY);

			Mat dst0, dst1, dst2, dst3, dst4, dst5, dst6, dst7;
			IplImage image;

			/*[1]*/
			if (true)
			{

				Canny(cvtColor_src, dst0, 3, 9, 3);
				imwrite("edge_Canny.bmp", dst0);
				image = dst0;
				drawToDC(&image, IDC_STATIC_DST0);
			}

			/*[2]*/
			if (true)
			{
				Mat grad_x, grad_y;
				Mat abs_grad_x, abs_grad_y;
				Sobel(cvtColor_src, grad_x, CV_16S, 1, 0, 3, 1, 1, BORDER_DEFAULT);
				convertScaleAbs(grad_x, abs_grad_x);
				Sobel(cvtColor_src, grad_y, CV_16S, 0, 1, 3, 1, 1, BORDER_DEFAULT);
				convertScaleAbs(grad_y, abs_grad_y);
				addWeighted(abs_grad_x, 0.5, abs_grad_y, 0.5, 0, dst1);
				imwrite("edge_Sobel.bmp", dst1);
				image = dst1;
				drawToDC(&image, IDC_STATIC_DST1);
			}
			
			
			/*[3]*/
			if (true)
			{
				Laplacian(cvtColor_src, dst2, CV_16S, 3, 1, 0, BORDER_DEFAULT);
				imwrite("edge_Laplacian.bmp", dst2);
				image = dst2;
				drawToDC(&image, IDC_STATIC_DST2);
			}
			

			/*[4]*/
			if (true)
			{
				Mat grad_x, grad_y;
				Mat abs_grad_x, abs_grad_y;
				Scharr(cvtColor_src, grad_x, CV_16S, 1, 0, 1, 0, BORDER_DEFAULT);
				convertScaleAbs(grad_x, abs_grad_x);
				Scharr(cvtColor_src, grad_y, CV_16S, 0, 1, 1, 0, BORDER_DEFAULT);
				convertScaleAbs(grad_y, abs_grad_y);
				addWeighted(abs_grad_x, 0.5, abs_grad_y, 0.5, 0, dst3);
				imwrite("edge_Scharr.bmp", dst3);
				image = dst3;
				drawToDC(&image, IDC_STATIC_DST3);
			}
			
		}
		else
		{
			Mat dst0, dst1, dst2, dst3, dst4, dst5, dst6, dst7;
			IplImage image;

			/*[1]*/
			if (true)
			{

				Canny(cvtColor_src, dst0, 3, 9, 3);
				imwrite("edge_Canny.bmp", dst0);
				image = dst0;
				drawToDC(&image, IDC_STATIC_DST0);
			}

			/*[2]*/
			if (true)
			{
				Mat grad_x, grad_y;
				Mat abs_grad_x, abs_grad_y;
				Sobel(cvtColor_src, grad_x, CV_16S, 1, 0, 3, 1, 1, BORDER_DEFAULT);
				convertScaleAbs(grad_x, abs_grad_x);
				Sobel(cvtColor_src, grad_y, CV_16S, 0, 1, 3, 1, 1, BORDER_DEFAULT);
				convertScaleAbs(grad_y, abs_grad_y);
				addWeighted(abs_grad_x, 0.5, abs_grad_y, 0.5, 0, dst1);
				imwrite("edge_Sobel.bmp", dst1);
				image = dst1;
				drawToDC(&image, IDC_STATIC_DST1);
			}


			/*[3]*/
			if (true)
			{
				Laplacian(cvtColor_src, dst2, CV_16S, 3, 1, 0, BORDER_DEFAULT);
				imwrite("edge_Laplacian.bmp", dst2);
				image = dst2;
				drawToDC(&image, IDC_STATIC_DST2);
			}


			/*[4]*/
			if (true)
			{
				Mat grad_x, grad_y;
				Mat abs_grad_x, abs_grad_y;
				Scharr(cvtColor_src, grad_x, CV_16S, 1, 0, 1, 0, BORDER_DEFAULT);
				convertScaleAbs(grad_x, abs_grad_x);
				Scharr(cvtColor_src, grad_y, CV_16S, 0, 1, 1, 0, BORDER_DEFAULT);
				convertScaleAbs(grad_y, abs_grad_y);
				addWeighted(abs_grad_x, 0.5, abs_grad_y, 0.5, 0, dst3);
				imwrite("edge_Scharr.bmp", dst3);
				image = dst3;
				drawToDC(&image, IDC_STATIC_DST3);
			}
		}

	}
}


```
