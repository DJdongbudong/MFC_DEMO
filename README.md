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
