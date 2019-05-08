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
