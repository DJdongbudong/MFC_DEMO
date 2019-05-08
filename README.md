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
	第三步：调用：IplImage image = Mat_src;		drawToDC(&image, ID)
	*/
```
```
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
```
# MFC创建子弹窗
```
*.rc资源处插入 Dialog -》 对新产生的窗口右键添加类，取名最好带xxDlg -》 自动生成 xxDlg.h 和 xxDlg.cpp 文件
```
